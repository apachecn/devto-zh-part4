# 将博客从 Wordpress 迁移到 Jekyll

> 原文：<https://dev.to/samba_code/migrating-a-blog-from-wordpress-to-jekyll-4flk>

所以我决定是时候重新开始写作了。因此，自从做出这个决定后，我尽我所能拖延和避免写作。这段时间主要是把我的博客从自己的 Wordpress 转移到 GitHub 页面上的 Jekyll。

## 一个简短的词为什么

Wordpress 是一个非常有能力的平台，有巨大的支持和大量的插件和主题。我绝对会把它推荐给任何一个刚开始想开一个博客的人。

然而它又大又慢又笨重。大量的网络是由 Wordpress 驱动的，所以它必须是所有人的所有东西，它变得很庞大。我的博客是一个大概 30 页的集合，一旦建立就不会改变。

谷歌有一个很好的工具，可以评估你网站的速度和其他一些指标。我的网站得分不高。

[![](img/8b983a34d71b688f89c25b50e3c67176.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RK_3JAnw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l73bw6eydewwjarctd7c.png)
[![](img/ef033b8795205351cc8f8a8eb8b758ce.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--x8TPoLub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4hkyh31tci3ovlfvd5vq.png)

我也听说了很多关于人们使用一个叫做 Gatsby 的工具来写博客的事情，老实说，正是这个工具让我掉进了这个兔子洞。

### 那么为什么不是盖茨比呢？

我知道一个静态站点生成器需要一些技术知识和一些额外的工作，但是在稍微了解了一下盖茨比的世界后，我觉得这对我的小博客来说是一个巨大的过度。我只是想运行一些命令，并在本地有一个静态博客，然后调整一下，推。每一篇“盖茨比入门”的文章似乎都包含了一本小小说。我肯定它很棒，但我就是不愿意付出努力。

Jekyll 的另一个主要优势是 GitHub 页面原生支持(甚至鼓励)它。这意味着现在我需要做的就是将我的帖子提交到 GitHub，它会自动构建并部署到我的站点上。额外的好处:它与 HTTPS 一起开箱。

## 好吧，那么什么是哲基尔呢？

这是一个静态站点生成器。你写你的文章/页面在 markdown(实际。md 文件！)，然后它用一些模板把它们组合起来，吐出一个超快的静态网站。您可以根据自己的意愿配置站点和模板(更改模板、内容、css、添加 javascript 等)。它是用 Ruby (boo)写的，但你不需要知道任何使用它的方法，所以这是一个优点。

## 从哪里开始

所以首先要做的是:这一点以前已经有人做过了，并在博客上发表过。我发现[这个帖子](https://girliemac.com/blog/2013/12/27/wordpress-to-jekyll/)和[这个帖子](https://blog.webjeda.com/wordpress-to-jekyll-migration/)特别有用。然而，我碰到了两个都没有涉及的问题，我想再发一个帖子也无妨。

买者自负:我在苹果电脑上。

首先，你需要把你的 Ruby 版本升级到 2.6.X 左右。在 Mac 上要做到这一点，你需要安装 RVM。

```
 \curl -sSL https://get.rvm.io | bash -s stable 
```

现在你有了 RVM，你可以升级 Ruby。

```
rvm install ruby-2.6.3 
```

希望你的 Ruby 版本现在已经达标了。

```
(base) Sams-MacBook-Pro:sam sam$ ruby -v
ruby 2.6.3p62 (2019-04-16 revision 67580) [x86_64-darwin18] 
```

现在我们可以安装 Jekyll 本身。

```
gem install jekyll-importer
gem install jekyll-import
gem install jekyll bundler 
```

导航到您存储代码的位置，我们将创建一个新的 jekyll 项目。使用您的网站名称；如果你有自己的域名，就这么命名吧。

```
jekyll new yourdomain.io 
```

您将拥有一个名为 yourname.io 的新文件夹，其中包含您的站点。cd 放进去。我们现在可以运行这个。

```
bundle exec jekyll serve 
```

这个神奇的命令在 localhost:4000 本地托管您的站点，并允许您查看您的精彩站点。显然它有点粗糙，但是让我们把它放到 GitHub 上，然后我们开始定制它。

### 到 GitHub！

GitHub 允许每个用户账户一个页面(你可以在你所有的子项目中使用它们。，但只有你的顶级 GitHub 用户有一个)。你需要创建一个名为. github.io 的 repo，对我来说就是 sameric . github . io。

在您的本地项目上，将它设置为 git 项目，并推送到您的新 repo

```
git init
git add *
git commit -m "initial commit"
-- follow instructions on github to add remote and push. 
```

希望你现在可以进入设置> GitHub 页面，你会看到一些有用的错误信息。

### 定制您的主题

当你安装 Jekyll 时，真正不明显的是如何定制它。

[![](img/2509a9eeef6598e051b96889e4d92ded.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w92efPUT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/stb9gzsscqylrxvd3x2g.png)

没有 HTML 文件？你如何改变这一点？如此困惑！

原来主题是捆绑在 gem 文件中的。你可以在 [jekyll 网站](https://jekyllrb.com/docs/themes/)上阅读完整的 doc-o 文件，但基本上你需要找到文件在你的文件系统上的位置，如果你想编辑它们，就把它们复制进来。我认为这是一个勇敢的尝试，让你跟上主题的变化，但在现实中，大多数人都想定制他们的主题，所以默认情况下不在本地爆炸似乎有点没有意义。

在 config.yml 中找到你的主题的名字；在撰写本文时，默认值是“最小值”。在 mac 上，运行

```
open $(bundle show minima) 
```

所有有趣的文件都会被揭露。_layouts 是各种页面类型的布局(与博客最相关的是“post.html”)，而 _includes 是跨布局的可重用 html 片段。将相同的文件夹结构和文件复制到您的项目中，默认情况下它们会被覆盖。最起码你需要把 post.html 抄过来，这样我们以后可以添加评论。

你可能想要一个完全不同的主题。这些可以在 RubyGems 的[这个链接](https://rubygems.org/search?utf8=%E2%9C%93&query=jekyll-theme)找到，但缺点是没有顶级预览，所以想要得到一个你喜欢的有点麻烦，尽管有更多来自[这个 GitHub](https://github.com/planetjekyll/awesome-jekyll-themes) 的直接演示链接。如果你喜欢一个主题，只需在你的 config.yml 中改变主题名称，jekyll 会处理剩下的事情。

无论何时你改变你的主题，你都需要重新定制。

### 从 Wordpress 导出旧内容

我原以为这是一场噩梦，但结果却很简单。有一个很棒的插件叫做[WordPress-to-jekyll-exporter](https://ben.balter.com/wordpress-to-jekyll-exporter/)它可以做到这一点——它会把你所有的帖子放到适合 Jekyll 的文件中。它会把它们放进拉链里。简单地解压这个文件，并将你的所有帖子复制到你的主题的 _posts 目录中。

如果你在本地刷新你的页面，你现在应该在 jekyll 站点本地拥有你的整个 Wordpress 博客。

小注意:我遇到了错误，因为我的旧帖子包含代码中的花括号。在这种情况下，您需要将整个内容包装在一个原始标签
中

```
 {{ "{% raw " }}%}
{ your code here }
 {{ "{% endraw " }}%} 
```

### 设置注释

我假设你已经在 disqus 评论系统上了。如果没有，注册 Disqus(这是一个非常棒的评论引擎),然后按照他们的指南去做。

假设你已经在 disqus 上了，你只需要把 [Disqus 通用代码](http://docs.disqus.com/developers/universal/)粘贴到你的 post.html 中任何你想显示评论的地方。如果您有多个站点，请确保您在 Disqus 中选择了正确的站点！我被抛出一个循环，因为 Disqus 混淆了我的帐户，给了我一个失败的通用代码。确保 src 部分下的内容与你在 disqus 中的博客名称相似！

```
s.src = 'https://sambastuff.disqus.com/embed.js'; # Make sure this line looks like your site! 
```

*需要注意的要点*:在本地你看不到任何评论。我认为这是因为 Disqus 检查它是从正确的站点提供的。这意味着你需要推进到 GitHub 才能看到它的工作。此外，假设你像我一样有一个自定义域，你需要将你的自定义域指向 GitHub 页面，并将你的 url 放入 GitHub 页面设置中，以便它们显示。

我想这就是一切；如果您遇到问题，请发表评论，我会尽力帮助您！