# 如何获取 iPad 的 VSCode

> 原文：<https://dev.to/alhilali/how-to-get-vscode-for-ipad-3p91>

我一直希望能够在 iPad 上有效地编码。苹果最近发布了新的 iPadOS，我认为这是一个尝试的好时机。

[![](img/e1c2a69e585cb8fcb9509fa3fe11e27f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--prlULngF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6lmtuast1hcg7tiprzoz.jpeg)

我将向你介绍我是如何在 iPad Pro 上获得上述内容的。这是受另一个[博客](https://medium.com/@ow/its-finally-possible-to-code-web-apps-on-an-ipad-pro-90ad9c1fb59a)的启发，该博客解释了如何使用数字海洋运行 VSCode。然而，我决定改用我的 Macbook Pro w/ 16gb 内存。原因是， [**编码器**](https://github.com/cdr/code-server) 消耗你的内存，并能够在云服务器提供商(AWS，数字海洋等)上有效地使用它...)你每个月至少需要花费 20 美元。

**预先请求**

1.  [**编码器**](https://github.com/cdr/code-server) (一个在你的浏览器上获得 VSCode 的惊人开源项目)
2.  一台可以运行编码器的机器——我将解释我是如何在 MacOS 上完成的
3.  一台 iPad:)

专业提示:使用 Chrome remote desktop 从你的 iPad 控制你的电脑来执行本教程中提到的步骤。

**让我们开始吧**
我们首先要确保设置好要从 iPad 连接的机器。从该机器:

1.  前往编码器 github 页面>发布页面
2.  下载最新版本(MacOS 的达尔文版本)
3.  解压文件后，您可以使用`./code-server --no-auth --allow-http`从终端运行它

你应该可以从你的机器上通过 http://localhost:8443 访问它。

现在，让我们确保我们可以从您的本地网络(也称为互联网)外部访问该服务器。
来自同一台机器:

1.  访问您的路由器设置>端口转发(从系统偏好设置>网络>路由器 IP 找到您的路由器 IP)
2.  将您选择的外部端口启用到代码服务器的端口 8443，并将其指向您机器的 IP
3.  获取您的公共 IP (Google it)

以下是我的华为路由器配置。

[![](img/f391f2a0bda468d0cc8ee2359f62011b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AN8IA0Dp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wi5qc4z52003w5j3dg2i.jpeg)

为了能够从我的 iPad 访问，我还必须做一件事，那就是使用 SSL/TSL 证书来保护连接。你可以在这里找到更多关于如何做的信息。

完成上述步骤后，使用生成的证书再次运行编码器:
`./code-server --cert=/Users/{user}/certs/MyCertificate.crt --cert-key=/Users/{user}/certs/MyKey.key --allow-http --no-auth`

您应该能够通过 http://{public-ip}:{port}从自己的 WIFI 外部访问 code-server(注意，我们使用的是 http 而不是 https)。现在，您的 ipad 上已经有了完整版本的 VSCode，并集成了一个终端来运行您喜欢的框架。

您可以重复上述步骤来打开不同的端口，以便能够同时运行 VSCode 以及 Angular/React/Ionic 或您将使用 VSCode 开发的任何项目。

尽管我还没有在我的 iPad 上经常使用它，但我认为在 iPad 上使用 VSCode 确实是一种享受。通过这种方式，我可以随时随地调整任何项目。

如果你一路上遇到任何问题，请告诉我，我会尽力帮忙。