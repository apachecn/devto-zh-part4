# 用 jekyll 建立静态网站/博客

> 原文：<https://dev.to/timabell/setting-up-a-static-website-blog-with-jekyll-4j5j>

一些人问我关于我的新博客设置的更多信息，这里有一个关于设置和发布的大致轮廓。有很多方法可以做到这一点，这是目前最适合我的方法。

你需要做的是:

*   来自 [GoDaddy](https://uk.godaddy.com/) 的域
*   在 [GitHub](https://github.com/) 上的账户

# 我喜欢这个设置的地方

*   内置备份，
*   离线编辑，
*   没有 shonkey web gui，
*   免费托管自定义域名和 https，
*   简单的开发友好的模板，
*   一个简单的代码友好的手机友好的设计，
*   完全控制。
*   [博客文章的公开修改历史](https://github.com/timabell/timwise.co.uk/commits/master)无需额外努力(博客文章中不再有“更新:废话”)

# 为什么杰克尔符合这一点

像这样的博客帖子只是一个叫做 markdown 的特殊格式的文本文件。然后可以用 git 对它们进行简单的源代码控制，git 在一个简洁的包中充当版本控制和分布式备份。我在工作中使用 git，所以尽管它不是最容易使用的工具，但对我来说这不是问题。理论上来说，如果你不知道 git，你可以在 github 网络界面上完成所有的编辑工作。

这是目前这个博客的整个文件夹结构。你可以看到真的没有太多。

```
tim@fox:~/repo/timwise.co.uk(master)
$ tree
.
├── 404.md
├── about.md
├── categories.md
├── CNAME
├── _config.yml
├── _drafts
│   └── 2019-06-24-setting-up-a-jekyll-blog.md
├── favicon.ico
├── fonts
│   ├── aramisi.ttf
│   ├── EBGaramond-Regular.ttf
│   └── FuturaPTLight.otf
├── images
│   ├── 404.jpg
│   ├── reverie-demo.png
│   ├── reverie.png
│   └── reverie-text.png
├── _includes
│   ├── analytics.html
│   ├── disqus.html
│   ├── meta.html
│   └── svg-icons.html
├── index.html
├── _layouts
│   ├── default.html
│   ├── page.html
│   └── post.html
├── LICENSE
├── _posts
│   └── 2019-06-21-yet-another-new-blog.md
├── _sass
│   ├── _darcula.scss
│   ├── _highlights.scss
│   ├── _reset.scss
│   ├── _svg-icons.scss
│   └── _variables.scss
└── style.scss

7 directories, 30 files 
```

这些文章只是使用 markdown 格式的文本文件，带有一个`.md`文件扩展名而不是`.txt`，还有一个简短的标题，用于显示页面标题和标签等信息。这里是这个的顶部:

```
---
layout: post
title: Setting up a static website/blog with jekyll
categories: [howto,backstage]
---

A couple of people asked me for more info on my new blog setup here's a rough
outline of what's involved in setup and posting. There are *many* ways of doing
this, this is just the way that suited me best for now. 
```

# 我是如何设置的

我实际上是从另一个设置重新配置，并使用了一些先进的愚蠢，但我会在这里简化到大致的步骤，你需要从冷，因为我相信这是你更感兴趣的。

1.  从 GoDaddy 购买域名
2.  创建 GitHub 帐户
3.  在成为你新网站的基础上找到一个 jekyll 主题(这些主题根据你准备好的程度不同而不同)。这里是[的遐想主题](https://github.com/amitmerchant1990/reverie)，我用了最小的定制。
4.  进入回购设置，![enabling github pages by choosing a
    branch](img/13ae4ddb4d56ec475484844422127a7d.png)
5.  打开 github 页面，![github settings
    tab](img/bc096d882ba0b5bad97e4ff74eceb96b.png)
6.  配置您的域名，![github settings
    tab](img/1cca07b1c432832e1c978405aa7abac6.png)
7.  启用 https ![github settings
    tab](img/f5f5c2020e5b65bd9ba351d1537f9cb8.png)
8.  转到 GoDaddy 并[设置 DNS 以指向 github 服务器](https://help.github.com/en/articles/using-a-custom-domain-with-github-pages)

# 与哲基尔一起写博客

1.  在`_posts`文件夹中创建新的文本文件，命名为`yyyy-mm-dd-your-post-title.md`，
2.  从上面窃取标题块并为你的帖子定制它，
3.  白色的东西
4.  使用 markdown 进行布局，
5.  通过将图像保存到 images 文件夹并添加到 git repo 中来添加图像，使用`![alt textimg/something.png)`在`.md`文件中显示图像，
6.  检查 github 中的所有内容，
7.  您的更改将在推送后几秒钟内生效。

如果你还没有完全准备好，你可以直接在 github 的网络界面上编辑你的博客。

1.  创建新帖子![creating a post](img/95a3e3c1438d993e1581ebd25af0d1da.png)
2.  编辑帖子![editing a post](img/c41063421b8126da3fdc3ad64e778dcc.png)
3.  编辑帖子的文本![editing the text of a post](img/ef522e3ccf8dc85007a1178794ad02f5.png)

# 总结

Wordpress 对于我的需求来说过于复杂，而且使用/阅读速度较慢，这个新的设置意味着我可以在火车上敲敲打打，更有规律地给你发送新的想法。

MailChimp 的 [rss 发送电子邮件](https://mailchimp.com/features/rss-to-email/)的加分，这意味着它会自动到达你的收件箱。