# 自动创建新的博客文章

> 原文：<https://dev.to/ankursheel/automating-the-creation-of-a-new-blog-post-3pba>

MDX 和 gatsby 对于建立一个博客来说是很棒的。但是，每当我想写一篇新的博客文章时，创建所有的框架材料是一件痛苦的事情。

## 创建新博文的步骤

1.  在*content/posts/{ year }/{ date-slug }/*下新建一个文件夹
2.  在上面的文件夹中新建一个 *index.mdx* 文件。
3.  从模板文件或现有帖子中复制 frontmatter。
4.  修改新帖子的大部分属性。
5.  如果帖子有图片库，那么
    1.  创建一个子文件夹*图片*。
    2.  从现有文件夹中复制一个 *data.json* 文件并更新它。

下面是这篇文章的一个例子

```
---
author: 'Ankur  Sheel'
date: '2019-09-12'
slug: 'automating-creation-of-new-blog-post'
title: 'Automating  the  creation  of  a  new  blog  post'
excerpt: 'Creating  a  new  blogpost  in  markdown  and  gatsby  has  quite  a  bit  of  skeleton  stuff.  Lets  see  how  to  automate  it.'
tags:
    - 'tutorial'
    - 'gatsby'
    - 'typescript'
featuredImage: ''
featuredImagePosition: ''
imageFacebook: './image-facebook.png'
imageTwitter: './image-twitter.png'
--- 
```

> 如果所有这些都可以通过一个命令生成，那不是很好吗？

现在你可能会问，花额外的几个小时来自动化一些只需要我几分钟时间的事情是否值得。

你可能会说，这并没有节省我太多的时间，当我开始一个新的帖子时，也没有太多的上下文切换。但是每次手动创建它都很痛苦，而且容易出错。

> 让我们自动化所有的事情。

## 我们将使用的库

使用*纱线*或 *npm* 安装以下组件。

*   [slugify](https://github.com/sindresorhus/slugify) :自动 slugify 标题
*   查询者 : CLI 获取标题、摘录、标签等用户输入
*   [jsToYaml](https://github.com/alexcrist/json-to-pretty-yaml) :将 json 转换为 Yaml
*   mkdirp :创建目录的助手
*   [更漂亮](https://github.com/prettier/prettier) : *可选*。代码格式化程序使用更漂亮的配置来格式化生成的文件。

## 让我们构建脚本

### 助手功能

```
const padLeft0 = (n: number) => n.toString().padStart(2, '0');

const fromRoot = (...p: string[]) => path.join(__dirname, '..', ...p);

const formatDate = (d: Date) => `${d.getFullYear()}-${padLeft0(d.getMonth() + 1)}-${padLeft0(d.getDate())}`;

const listify = (a: string) => (a && a.trim().length ? a.split(',').map(s => s.trim()) : ''); 
```

### 使用 Inquirer 获取用户的详细信息

在我的例子中，我得到了*标题*、*描述*、*标签*和*是否帖子有图片*。

```
const prompt = await inquirer.prompt([
    {
        type: 'input',
        name: 'title',
        message: 'Title',
    },
    {
        type: 'input',
        name: 'description',
        message: 'Excerpt/Description',
    },
    {
        type: 'input',
        name: 'tags',
        message: 'Tags/Keywords (comma separated)',
    },
    {
        type: 'list',
        name: 'images',
        message: 'Post has image gallery (yes/no)',
        choices: [{ name: 'Yes', value: 'Y' }, { name: 'No', value: 'N' }],
    },
]);

const { title, description, tags, images } = prompt; 
```

### 创建文件夹结构

```
const date = new Date();
const slug = slugify(title);

const destination = fromRoot('content/posts', `${date.getFullYear().toString()}`, `${formatDate(date)}-${slug}`);

mkdirp.sync(destination); 
```

日期设置为当前日期，并根据标题自动创建。

创建的文件夹具有一致的结构。

### 用 frontmatter 创建 MDX 文件

```
const yaml = jsToYaml.stringify({
    author: 'Ankur Sheel',
    date: formatDate(new Date()),
    slug,
    title,
    excerpt: description,
    tags: listify(tags),
    featuredImage: '',
    featuredImagePosition: '',
    imageFacebook: './image-facebook.png',
    imageTwitter: './image-twitter.png',
});
const markdown = prettier.format(`---\r\n${yaml}\r\n---\r\n`, {
    ...require('../.prettierrc'), // eslint-disable-line global-require
    trailingComma: 'es5',
    endOfLine: 'crlf',
    parser: 'mdx',
});

fs.writeFileSync(path.join(destination, 'index.mdx'), markdown); 
```

那么在上面的代码片段中发生了什么呢

*   ***第 1-12 行*** :创建一个 JSON 对象，使用 *jsToYaml* 将其转换为 *YAML* 对象。
*   ***第 13-18 行*** :使用 prettier 获取 *yaml* 对象的格式化字符串，用 *-* 包装，使其成为 MDX 期望的格式。

一些属性是硬编码的，比如*作者*、*图片 Facebook* 、*图片 Twitter* ，其余的是自动生成的。

文件名也总是被命名为 *index.mdx* ，并且位于创建的文件夹中。

### 创建 images 文件夹和 data.json *(如果需要)*

```
if (images === 'Y') {
    const data = {
        gallery: [{ image: `./${slug}-1.jpg`, title: '' }],
    };

    const json = prettier.format(JSON.stringify(data, null, 4), {
        ...require('../.prettierrc'), // eslint-disable-line global-require
        trailingComma: 'es5',
        endOfLine: 'crlf',
        parser: 'json',
    });

    const imagesDestination = path.join(destination, 'images');
    mkdirp.sync(imagesDestination);

    fs.writeFileSync(path.join(imagesDestination, 'data.json'), json);
} 
```

让我们看看这里发生了什么

*   ***第 1 行*** :仅当用户指示该帖子将有图片库时，才创建图像文件夹。
*   ***第 2-4 行*** :为*图片*创建一个基本的 JSON 对象。
*   ***第 6-11 行*** :使用 prettier 获取 *json* 对象的格式化字符串。
*   ***第 13-14 行*** :创建图片文件夹。
*   ***第 16 行*** :创建 *data.json* 文件。

## 其他注释

*   使用[扑通](https://github.com/amwmedia/plop)编写生成器可能会更好/更快，这样我就有一个可以使用的模板文件。扑通还支持修改一个文件，这可能是有帮助的，如果我有很多博客帖子，并希望改变 frontmatter 结构。

## 结论

建立一个新的博客帖子可能会花费我几分钟的时间，并且很容易出错，但是使用这个脚本，我只需要调用脚本，输入几个特定于帖子的值，我就可以开始写作了。

你可以在这里看到整个脚本[。](https://github.com/AnkurSheel/ankursheel_website/blob/master/scripts/createNewBlogpost.ts)

你在你的项目中自动化过什么吗？你会对这个脚本做什么修改？请在评论中告诉我。