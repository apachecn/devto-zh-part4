# 使用 Lunr.js 和 Node.js 向 Hugo 站点添加搜索

> 原文：<https://dev.to/hugo__df/add-search-to-a-hugo-site-with-lunr-js-and-node-js-3m9h>

[Hugo](https://gohugo.io) “世界上最快的网站构建框架”是 [JAMStack](https://jamstack.org/) (JavaScript、API、预构建标记)网站的绝佳选择。

[Lunr.js](https://lunrjs.com/) 是“有点像 Solr，但是小得多，也没有那么亮”，它是一个类 Solr 搜索引擎的纯 JavaScript 实现。

它唯一没有提供的东西就是搜索。在[“搜索您的 Hugo 网站”](https://gohugo.io/tools/search/)中，它确实为您提供了一些整合选项。

没有一个是即插即用的，所以我使用 Lunr.js、一个小的 Node.js 脚本和几行客户端 HTML/JavaScript 代码编写了自己的简单解决方案。

这是 Node.js 优势的一个很好的例子:集成一个纯 JavaScript 库和预先计算搜索索引是一件轻而易举的事情。

你可以在 codewithhugo.com/search/?q=lunrjs 看到搜索在进行。

在 gist.github.com/HugoDF 找到完整的要点

## 用 frontmatter 加载所有降价内容

我们只想索引一个目录，即“content/posts”目录，我们的`loadPostsWithFrontMatter`函数将接受 posts 目录的完整路径。

这个函数做的第一件事是读取目录内容以获取所有文件名。然后，它读取每个文件并解析 frontmatter 和 markdown。它将内容和前端数据合并成一个对象。它还将内容截断为 3000 个字符，以避免生成巨大的(2MB+)索引文件。

```
const fs = require('fs').promises;
const {promisify} = require('util');
const frontMatterParser = require('parser-front-matter');
const parse = promisify(frontMatterParser.parse.bind(frontMatterParser));
async function loadPostsWithFrontMatter(postsDirectoryPath) {
const postNames = await fs.readdir(postsDirectoryPath);
const posts = await Promise.all(
postNames.map(async fileName => {
const fileContent = await fs.readFile(
`${postsDirectoryPath}/${fileName}`,
'utf8'
);
const {content, data} = await parse(fileContent);
return {
content: content.slice(0, 3000),
...data
};
})
);
return posts;
} 
```

## 创建 Lunr.js 索引

给定一个帖子列表，我们希望使用标题作为参考(稍后将详细介绍)，并索引标题、内容和标签字段。

```
const lunrjs = require('lunr');
function makeIndex(posts) {
return lunrjs(function() {
this.ref('title');
this.field('title');
this.field('content');
this.field('tags');
posts.forEach(p => {
this.add(p);
});
});
} 
```

## 把所有的东西放在一起

下面的脚本需要包含前面定义的 JavaScript 函数才能工作，并且位于 Hugo 项目的根位置，以便将所有的文章读入搜索索引。

在[gist.github.com/HugoDF/aac2e529f79cf90d2050d7183571684b](https://gist.github.com/HugoDF/aac2e529f79cf90d2050d7183571684b)见完整文件。

这个函数实际上只是注销了字符串索引。为了将它放入文件，我们可以添加`await fs.writeFile('./path/to/index.json', JSON.stringify(index), 'utf8')`或者我们可以重定向文件的输出(这更灵活一点)。

```
async function run() {
const posts = await loadPostsWithFrontMatter(`${__dirname}/content/post`);
const index = makeIndex(posts);
console.log(JSON.stringify(index));
}
run()
.then(() => process.exit(0))
.catch(error => {
console.error(error.stack);
process.exit(1);
}); 
```

## 生成索引文件

我个人创建了一个包含一个`.gitkeep`文件的`static/gen`文件夹。把空文件夹添加到 git 然后忽略，那么我的 Lunr.js 搜索索引生成命令是:

```
node ./build-lunrjs-index.js > static/gen/search-index.json 
```

您也可以将搜索索引放入静态文件夹的根目录:

```
node ./build-lunrjs-index.js > static/search-index.json 
```

甚至直接放在`public`里:

```
node ./build-lunrjs-index.js > public/search-index.json 
```

在每一种情况下，都不要试图将输出重定向到一个不存在的目录(尤其是在您的持续集成管道中)。

## 消耗 Lunr.js 索引客户端

要使用 Lunr.js 索引，我们只需加载它并调用`lunr.Index.load`，如下所示:

```
fetch('/gen/search-index.json').then(function (res) {
return res.json();
}).then(function (data) {
const index = lunr.Index.load(data);
const matches = index.search(searchString);
}); 
```

更全面的集成可能如下所示。

我们希望有一个提交按钮和一个清晰的链接搜索框(形式)。当页面加载时，我们首先通过将它解析为一个`URLSearchParams`来检查`q`参数包含了什么。

如果它是空的，显示一条信息消息。

如果有一个搜索查询，我们使用`fetch`加载搜索索引，使用`lunr.Index.load`加载到内存中，并根据它进行搜索。在此之前，我们还使用 Hugo slices 和一点 JavaScript 生成了一个帖子标题- >搜索结果映射。

使用 title -> result mapping，我们显示相关的搜索结果。

```
<form method="get" action="">
<input id="search" name="q" type="text" />
<button type="submit" class="button">Search</button>
<a href="/search">Clear</a>
</form>
<div id="#app"></div>
<script src="https://unpkg.com/lunr/lunr.js"></script>
<!-- Generate a list of posts so we can display them -->
{{ $p := slice }}
{{ range (where .Site.RegularPages "Section" "==" "post") }}
{{ $post := dict "link" .RelPermalink "title" .Title "content" (substr .Plain 0 200) -}}
{{ $p = $p | append $post -}}
{{ end }}
<script>
const posts = JSON.parse(
 {{ $p | jsonify }}
);

const query = new URLSearchParams(window.location.search);
const searchString = query.get('q');
document.querySelector('#search').value = searchString;
const $target = document.querySelector('#app');

// Our index uses title as a reference
const postsByTitle = posts.reduce((acc, curr) => {
 acc[curr.title] = curr;
 return acc;
}, {});

fetch('/gen/search-index.json').then(function (res) {
 return res.json();
}).then(function (data) {
 const index = lunr.Index.load(data);
 const matches = index.search(searchString);
 const matchPosts = [];
 matches.forEach((m) => {
 matchPosts.push(postsByTitle[m.ref]);
 });

 if (matchPosts.length > 0) {
 $target.innerHTML = matchPosts.map(p => {
 return `<div>
 <h3><a href="${p.link}">${p.title}</a></h3>
 <p>${p.content}...</p>
 </div>`;
 }).join('');
 } else {
 $target.innerHTML = `<div>No search results found</div>`;
 }
}); 
```

你可以在 codewithhugo.com/search/?q=lunrjs 看到搜索在进行。

在[gist.github.com/HugoDF](https://gist.github.com/HugoDF/aac2e529f79cf90d2050d7183571684b)看完整的要点

[无刷标志
n](https://unsplash.com/@ellladee?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)