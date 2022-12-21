# Flutter:在开发中使用自签名 SSL 证书

> 原文：<https://dev.to/remejuan/flutter-using-self-signed-ssl-certificates-in-development-20ce>

[![](img/c3a3e4fbff785f963e1decadeb27548f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TvTv-uOZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A73IgUxPfyXUKZAaIXgutrw.png)

我最近在工作中的 RnD 阶段开始与 Flutter 一起工作，在此之前，Ionic 一直是计划的方向，但考虑到业务需求，它显示出自己是一个不可行的选项。就个人而言，很可能从职业上甚至对企业而言，这都是塞翁失马，焉知非福。

这个问题向我们介绍了 flutter，我自己对 discover 非常满意，它易于使用，教程非常丰富，而且来自一个打字稿前端环境，既不同又熟悉。

现在还为时尚早，但到目前为止，我在使用它时只遇到了一个重大问题，那就是当我试图连接到我的本地 api 时，它自然有一个自签名证书。解决这个问题，或者更准确地说，处理这个问题似乎比我想象的要复杂得多。

虽然谷歌上出现了许多结果，但不幸的是，对于一个陷入困惑 4 天的人来说，没有一个答案给了我足够的背景来理解我需要做什么，许多小时后和大量阅读后，我开始通过我通读的各种代码位和帖子来发现背景。

> 这只是一个简单的文档，希望下一个新人能找到它，并得到一个完整的图片，希望更容易理解。

就我的 Google Fu 所显示的，我能找到的只有 2 个包支持使用**坏的**证书，那就是[HttpClient](https://api.dartlang.org/stable/2.4.0/dart-io/HttpClient-class.html)和 [Dio](https://github.com/flutterchina/dio) ，当我到达 Dio 时，我已经基本上制定出了自己的解决方案，所以下面的代码是针对 http client 的。

HttpClient 有一个名为 *badCertificateCallback* 的方法，我写了一个类来管理我所有 API 调用的 cookies，目前包含了我的 GET 和 POST。

```
static HttpClient client = new HttpClient()
    ..badCertificateCallback = (\_certificateCheck); 
```

所以在实例化 HttpClient 类时，我用函数 *badCertificateCallback* 实例化了它，我把它写成:

```
static bool \_certificateCheck(X509Certificate cert, String host, int port) => host == 'local.domain.ext'; 
```

该函数本身只是期望一个 bool，我发现的许多帖子只是建议让它返回 true，但这是不安全的，即使这被保留下来并投入生产，它带来的风险也非常低。

简而言之，这是你需要做的事情，让你的自签名证书在你的开发环境中工作，现在为了确保完整的上下文，也许收集一些额外的想法，这里是我正在使用的类的当前版本: