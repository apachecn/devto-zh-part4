# 从 GIT repo 管理您的 dev.to 博客文章，并使用持续部署来自动发布/更新它们

> 原文：<https://dev.to/maxime1992/manage-your-dev-to-blog-posts-from-a-git-repo-and-use-continuous-deployment-to-auto-publish-update-them-143j>

你是否曾经希望有一个 monorepo ( **1* )包含你在 GitHub 上的所有 dev.to 帖子，一旦你将更新合并到主分支，它们就会自动在 dev.to 上更新？

*1)或者多个存储库，这都没关系😃*

然后你会对即将发生的事情感到满意，否则我会在下一节让你知道为什么你会想要这样。

## 为什么我要把我所有的博客文章放在 git 回购上？

*   不要害怕在编辑你的文章时弄糟它
*   与开发时相同的良好实践(格式化、提交、拥有变更历史、编辑时比较等)
*   使用[更漂亮的](https://prettier.io/)来格式化 markdown 和所有的代码片段
*   通过创建一个针对你的文章的公关，让人们为你的文章做出贡献(厌倦了因为一些错别字而导致评论偏离方向？只要让人们知道他们可以在你的博客文章末尾做公关就行了)
*   在你的博客文章附近创建代码示例，并确保它们是正确的，感谢 [Embedme](https://github.com/zakhenry/embedme) ( **1* )

Embedme 允许你在实际的文件中编写代码，而不是在一个 Markdown 文件中，然后在需要的地方为你注入代码。

如果你不喜欢用漂亮或嵌入的，你可以简单地把它们去掉，但我认为这是一个很好的东西！

# 设置时间长吗？

不会超过几分钟。大概 3 到 5 分钟，从无到有，到在 dev.to 上自动部署一个更新到使用这个新的设置🔥！

# 演示一下怎么设置！

您可以选择是将这个工作流集成到现有的存储库中，还是使用我制作的**模板**来简化从头开始的过程。

在这篇博文中，我们将重点介绍如何开始使用模板，但是阅读这篇文章后，你会意识到集成到你自己的工作流程中是多么容易。

主要步骤:

*   1.复制模板
*   2.创建一个开发目标令牌
*   3.把令牌递给特拉维斯·CI
*   4.将您的博客文章放入新的存储库中

## 1。复制模板

前往[https://github.com/maxime1992/dev.to](https://github.com/maxime1992/dev.to)复制模板:

[![Copy the template repository](img/25b85617f6dd8d9a242f69797b70ca2f.png "Copy the template repository")](https://res.cloudinary.com/practicaldev/image/fetch/s--j1zp5dIw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/github-template-repo.png)

根据需要设置存储库的名称和描述:

[![Define the name, description and visibility of the repository](img/5462483df158ae39547595345ed580fa.png "Define the name, description and visibility of the repository")](https://res.cloudinary.com/practicaldev/image/fetch/s--Jmk3UMMN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/create-repository-from-template.png)

*(注意:如果你想使用 Travis 的免费实例来持续部署你的博客文章，那么这个库必须是公开的)*

一旦生成了存储库，您应该会看到如下所示的内容:

[![Generated repository](img/78cd32f344e5970c4dbaff15a9cd2339.png "Generated repository")](https://res.cloudinary.com/practicaldev/image/fetch/s--M_VJUKCF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/repository-generated-from.png)

## 2。创建一个开发目标令牌

转到[https://dev.to/settings/account](https://dev.to/settings/account)并为令牌命名，这样您就可以记住您在哪里使用它:

[![Generate a dev to token](img/a6e617420079e5d5ce661624dd9c4c78.png "Generate a dev to token")](https://res.cloudinary.com/practicaldev/image/fetch/s--Hyo8AFJL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/dev-to-generate-token.png)

暂时保持页面打开。

## 3。把令牌递给特拉维斯

**编辑:2020 年 7 月 8 日**

[Beeman](https://dev.to/beeman) 在 [dev.to](https://dev.to) 上写了一篇很棒的帖子使用 [Github 动作](https://github.com/features/actions):

[![beeman image](img/4ca22dc89cfc4e4b081e269ad7970c4f.png)](/beeman) [## 使用 GitHub 动作自动化您的开发帖子

### 比曼🐝7 月 5 日 203 分钟读数

#devto #publication #stackbit #github](/beeman/automate-your-dev-posts-using-github-actions-4hp3)

Github Actions 相对于 Travis 的主要优势在于，如果回购是私有的，你可以获得 2000mn 的空闲时间(这应该绰绰有余！).

虽然我们将重点关注如何使用 Travis 作为我们的 CI，但如果您更喜欢使用 Github Actions，请查看 Beeman 的文章！=)

前往[https://travis-ci.org/account/repositories](https://travis-ci.org/account/repositories)

由于 GitHub 存储库刚刚创建，Travis 可能还没有看到回购。如果是这样的话，点击左边的“同步帐户”按钮来强制刷新你的存储库。

[![Travis sync account and repositories](img/24a60367be07f6238a845b1a808b8c4e.png "Travis sync account and repositories")](https://res.cloudinary.com/practicaldev/image/fetch/s--Leg3i4z2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/travis-ci-sync-account.png)

完成后，您应该能够找到您的项目，您需要单击右侧的切换按钮来激活它:

[![Travis activate the repository](img/231ef96bb554e36db1adca983d85eb58.png "Travis activate the repository")](https://res.cloudinary.com/practicaldev/image/fetch/s--sMV2st6v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/travis-ci-my-dev-to-activated.png)

你现在可以点击“设置”。

我们现在需要将 dev.to 令牌传递给 Travis。在“环境变量”部分，定义一个名为`DEV_TO_GIT_TOKEN`的新变量。返回到生成令牌的 dev.to 选项卡，复制它并粘贴到“value”输入中。

[![Pass the dev to token to Travis](img/487b3253e8a660d8f5379f1d67ebd479.png "Pass the dev to token to Travis")](https://res.cloudinary.com/practicaldev/image/fetch/s--q-A4RrQu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/travis-ci-set-token.png)

准备好后，点击“添加”按钮。

## 4。将您的博客文章放入新的存储库中

您需要做的第一件事是确保您的 package.json 定义了一个属性`repository.url`。它看起来应该是这样的:[https://github.com/maxime1992/my-dev.to.git](https://github.com/maxime1992/my-dev.to.git)，带有您自己的用户名/存储库名。它将用于从那里检索您的文章图像。

然后，注意在项目的根目录下有一个`dev-to-git.json`。这是我们管理想要发布的博客文章的地方。

json 文件应该包含一个数组，每个条目应该有两个字段:`id`和`relativePathToArticle`。

示例:

```
[  {  "id":  133785,  "relativePathToArticle":  "./blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/manage-dev-to-blog-posts-with-continuous-deployment.md"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

没有简单的方法来管理一篇文章的创作(还没有？)但这是一件快速/一次性的事情。进入 [https://dev.to](https://dev.to) ，点击顶部的“写文章”按钮。写一个标题(以后可以更新)，按下“保存草稿”按钮。这样，文章还没有发表，只有你可以看到。

由于 dev.to 没有在页面上显示一篇博客文章的 ID，所以我做了一个小查询来在页面中找到它。在文章的 dev.to 页面上打开浏览器控制台，粘贴以下内容:

```
+$('div[data-article-id]').getAttribute('data-article-id'); 
```

Enter fullscreen mode Exit fullscreen mode

这将为您提供要放入`dev-to-git.json`的文章的 ID。

你的博客应该有一个“前沿问题”的标题。例如，我正在写的一篇博文是:

```
--------
published: false
title: "Manage your dev.to blog posts from a GIT repo and use continuous deployment to auto publish/update them"
cover_image: "https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/github-travis-dev-to.png"
description:
tags: devto, publication, blogpost, continuousdeployment, github, travis
series:
canonical_url:
-------- 
```

Enter fullscreen mode Exit fullscreen mode

这意味着你可以直接从用 Markdown 写的博客文章中管理所有这些属性。多酷啊。

最后，所有本地图像链接都被重写，变成指向 GitHub 上文件的远程链接。是的，甚至您的图像也可以成为您版本化过程的一部分🙌！

# 感谢阅读

不要害羞，让我知道你对☺️.评论这个项目的看法你喜欢吗？会考虑用吗？如果没有，我很想知道在您的工作流程中还缺少什么来让它更好地为您服务。

* * *

我在伦敦市中心的 [CloudNC](https://cloudnc.com) 工作，我们正在[招聘](https://cloudnc.com/careers)！

[![CloudNC is recruiting](img/fe318d3f4ccacaa0b621fb4f4d358a99.png "CloudNC is recruiting")](https://res.cloudinary.com/practicaldev/image/fetch/s--1SrPLCKA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/maxime1992/my-dev.to/master/blog-posts/manage-dev-to-blog-posts-with-continuous-deployment/assets/join-cloudnc.jpg)

我们每个月的第一个星期五都有一个 hackday，在最后一个星期五，我决定参与这个项目。我已经开源了一个名为 [dev-to-git](https://github.com/maxime1992/dev-to-git) 的 npm 模块，它可以处理在 dev.to(模板存储库使用的)上读取和发布的大部分繁重工作。

# 发现错别字？

如果你在这篇博文中发现了一个错别字、一个可以改进的句子或者任何其他应该更新的内容，你可以通过 git 存储库访问它，并发出一个 pull 请求。请不要发表评论，直接前往[https://github.com/maxime1992/my-dev.to](https://github.com/maxime1992/my-dev.to)并打开一个包含您所做更改的新拉动式请求。