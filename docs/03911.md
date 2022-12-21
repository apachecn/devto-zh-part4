# 关于在您的 Elm 项目中集成 Firebase

> 原文：<https://dev.to/jlengrand/about-integrating-firebase-in-your-elm-project-216b>

[![About integrating Firebase in your Elm project](img/03b953cc7d7bb764a72e9c70cc7f282d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CO_M6Mry--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lengrand.fr/conteimg/2019/09/Screen-Shot-2019-09-06-at-1.07.09-PM.png)

在我的上一篇文章中，我解释了关于 [**Elm**](https://elm-lang.org/) 端口的基础知识，最终我自己学会了如何使用它们。

我最终进入 ports 的原因是我想在我的新 Elm 项目中使用 Firebase，并且最终有一个没有后端管理和维护的辅助项目。

本文介绍了我制作的 [elm-firebase](https://github.com/jlengrand/elm-firebase/tree/master) 资源库，它可以作为在您自己的应用程序中支持 firebase 的基础。 [**你可以试试这里的试玩。**](https://elm-firebase.netlify.com/)

## 你说为什么又是一次整合？

*   如果你用谷歌搜索 elm 和 firebase，你会找到一些匹配的。例如，elmfire 首先出现在我面前，然后是另一个 T2 elm-firebase T3。除了这两个项目已经多年没有更新，没有一个支持 0.19(据我所知，还有最新的 Firebase Cloud Firestore)。
*   我想自己学习更多关于端口的知识，并把构建自己的集成作为一次学习经历。
*   我正积极尝试在网上创建更多的 elm 资源来传播我对语言的热爱:)
*   我和一个朋友正在做一个兼职项目，需要一个数据库:d。

## 那么它在做什么呢？

[使用我开发的演示应用](https://elm-firebase.netlify.com)，你可以:

*   使用 Google OAuth2 登录。使用 [Firebase 认证](https://firebase.google.com/docs/auth)保存用户
*   登录保持缓存，因此您不必一直登录
*   您可以将文本消息保存到数据库中，并查看您编写的消息列表。这部分利用了新的 [Firebase 云 Firestore](https://firebase.google.com/docs/firestore) 。
*   基本的安全措施已经到位，这意味着你不能看到你的朋友的消息。

下面是一段关于这些功能的简短视频:

<figcaption>elm-firebase 的(有限)功能演示</figcaption>

## 那么我该如何入门呢？

最简单的方法可能是[前往资源库](https://github.com/jlengrand/elm-firebase)，克隆它并阅读[的自述文件](https://github.com/jlengrand/elm-firebase/blob/master/README.md)！

希望知识库，甚至这篇文章对你们中的一些人有用:)。如果你有意见、改进或者只是想聊天，请在 **[Twitter](https://twitter.com/jlengrand)** :)上联系我。