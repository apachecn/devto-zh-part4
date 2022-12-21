# 在 Stackblitz 上运行角度项目单元测试

> 原文：<https://dev.to/angular/running-angular-project-unit-tests-on-stackblitz-h20>

*我不是 Stackblitz 团队的一员，所以这不是一篇推广文章:-)我只是认为****stack blitz****是向文章读者展示你的代码的一个很好的方式。但是长期以来，我忽略了一个小特性:如何在 stackblitz* *上运行 Angular 项目，然后切换同一个项目来运行单元测试。感兴趣吗？继续读！*

[![](img/b8e3634f8e52e59c09cda128e8192061.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZmAwuUZq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aq2UANtrbDUlg6DGnSunhag.jpeg) 

<figcaption>切换 Stachblitz 角项目到机组试运行。(图片由[米克尔·贝克](https://unsplash.com/@bechbox) )</figcaption>

> 这篇文章最初发表于 [Hackernoon](https://hackernoon.com/running-angular-project-unit-tests-on-stackblitz-bc091abbfcc9)

#### 为什么我应该在 stackblits 中运行角度项目单元测试？

嗯，有几次我需要单元测试运行来学习，或者创建一些操场链接来参考技术文章。所以…

#### 在 stackblitz 上开始你的 GitHub 项目

每个人都知道如何从 GitHub 的 *stackblitz* 开始 Angular CLI 项目。下面是一段来自[官方文件](https://stackblitz.com/docs#import-from-github)的引文:

> 从 Github 导入
> 
> 您可以在 Github 上运行任何公共回购，只需提供用户名+回购名称，如下所示:
> 
> stackblitz.com/github/{GH_USERNAME}/{REPO_NAME}

比如下面是我的回购链接:

```
https://github.com/kievsash/NoSniffOptionTest 
```

而各自的 *stackblitz* 链接将是:

```
https://stackblitz.com/github/kievsash/NoSniffOptionTest 
```

在这里，您可以看到项目正常运行:

[https://stackblitz.com/edit/github-bzfbxl?embed=1&&](https://stackblitz.com/edit/github-bzfbxl?embed=1&&)

#### 使它运行我们的单元测试

好，很好，是我先开始的。但是如何对它进行测试呢？对我来说并不明显，直到我发现

> stackblitz 只是运行 **main.ts** 文件

(有一件事情，当你知道的时候，它看起来很简单:-)。

因此，要在一个 *stackblits* 中为你的项目启动 Jasmin 测试，我们需要将所有单元测试引导代码放到 **main.ts** 文件中。因此，让我们将当前的 main.ts 重命名为 main.bck，并创建一个新的 main.ts 文件。

为了用 main.ts 中的 Jasmin 运行我们的单元测试，我们接下来应该做:

1.  导入并初始化浏览器模块的 jasmine 和 jasmine
2.  导入 zone.js 模块进行测试
3.  导入出 ***.spec.ts*** 文件
4.  自举角度测试环境

好吧，让我们这样做: