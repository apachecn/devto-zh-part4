# 用 Crystal 浏览网页

> 原文：<https://dev.to/watzon/browsing-the-web-with-crystal-3mf2>

如果您已经在 web 开发行业工作了相当长的时间，那么您可能已经听说过 Selenium、WebDriver 和 Puppeteer 等工具。这些主要被吹捧为“测试自动化框架”，因为它们使开发人员能够通过说“去这里”、“点击这个”、“截取一个截图并与之前的截图进行比较，确保没有任何变化”来自动化 web 应用的测试。这些工具也有助于网页抓取，因为它们允许你看到的网页与浏览器看到的完全一样。这意味着 JavaScript 渲染的内容是可废弃的！

迄今为止，Selenium 可能是这些工具中最流行的。它无处不在，从测试环境和 CI 到 web scrapers 和 pentesting 工具。它非常强大，但有一个主要缺点。Java。

任何曾经编写或使用过 Java 应用程序的人都知道它有一个主要缺点。内存使用。Java 应用程序很重，这限制了它们在资源较少的系统和容器化应用程序上的有用性。这并没有阻止人们去尝试，但是如果不是必须的话，为什么要使用额外的资源呢？

因此，考虑到速度和内存效率，我最近开始为水晶生态系统设计自己的解决方案；一个我决定在 Firefox 牵线木偶协议之后称之为牵线木偶的解决方案。

## 探索牵线木偶

当然，在包装任何类型的 API 之前，您必须做一些研究。不幸的是，牵线木偶并没有得到很好的证明。就方法和功能而言，Marionette 和 WebDriver 有很多共同之处，Marionette 建立在 WebDriver 协议提供的功能之上。

很自然，我的第一个谷歌搜索是“火狐木偶协议”，这将我带到了描述该协议的页面，但没有我希望的那么详细。也就是说，它至少描述了消息发送到 Firefox 时必须采用的格式。不幸的是，在浏览了该网站的整个木偶部分后，我没有找到任何关于木偶协议接受什么命令的信息。所以我继续寻找。

我在牵线木偶文档中看到的东西提到了 Python 客户机。现在，我让所有人都知道我对 Python 的极度厌恶，但如果它能帮助我弄清楚木偶协议有哪些可用的方法，那就值得了。所以我跟踪了他们的参考客户端的链接，并开始钻研 Python 代码(我很高兴地发现它写得非常好)。

经过一点挖掘，我找到了我要找的东西！[这个文件](https://searchfox.org/mozilla-central/source/testing/marionette/client/marionette_driver/marionette.py)包含了 Python 客户端用来与木偶交互的大多数方法，包括方法的名称和消息发送的格式。于是开始了构建我自己的客户端的过程。

## 构建客户端

主要的客户端功能相当简单，因为我有一个全功能的 Python 客户端。我还设法找到了一个用 Go 编写的客户端[,它非常相似，但没有 Python 客户端那么强大。我仍然没有设法找到一个牵线木偶接受的 RPC 命令列表，但是在这两个客户端之间，我能够相当容易地构建我自己的命令。](https://github.com/njasm/marionette_client)

如果没有我的雇主 NeuraLegion 的帮助，这个项目是不可能的，至少在我能够让它工作的时间内是不可能的。

NeuraLegion 是一家应用安全公司，提供由 AI 支持的 SASS AIAST 解决方案。或者，对于不会说渗透测试器的人来说，这是一家使用人工智能软件测试网站和其他应用程序安全性的公司。该软件必须能够像你或我一样浏览网页，但它还需要能够做你在日常网页浏览中通常不会做的事情，如修改 HTTP 头，在 POST 请求中向开放 API 路由发送攻击载荷等。因此，仅仅一个标准的 Selenium 类型的客户机是不够的。

我们需要一个代理。

## 构建代理

有两种主要类型的代理:正向代理，用于将来自专用网络或内部网的输出请求转发到互联网，通常通过防火墙；反向代理，代表客户端从一个或多个服务器检索资源；然后，这些资源被返回给客户端，就好像它们来自代理服务器本身一样。

Firefox，因此也是牵线木偶，已经内置了对转发代理的支持。你所要做的就是给它提供一个 HTTP 代理、HTTPS 代理和潜在的 FTP 和 SOCKS 代理的地址，它会在所有请求到达浏览器之前通过这些地址。不幸的是，这需要一个单独的代理，并且隧道 HTTPS 请求变得更加困难。因此，人们认为反向代理更有意义。

我不会详细介绍反向代理是如何工作的，如果你想查看代码，你可以在这里找到它。基本上这一切意味着:

当牵线木偶在`extended`选项设置为`true`的情况下启动时，一个`Proxy`对象被实例化，服务器被启动。然后，`Browser#goto`方法将所有导航请求转发给代理，并告诉它获取什么页面。代理获取资源并在页面内容上施了一点`gsub`魔法，将所有内部链接替换为指向代理服务器的链接。所有内部资源在获取时都通过代理进行隧道传输，允许我们潜在地修改 javascript、图像等。在他们到达页面之前。然后，所有内容都被打包并返回给浏览器，就像什么都没发生一样。

基本上，这意味着在浏览器知道页面被触摸之前，你可以对页面做任何你想做的事情，这对 AppSec 来说是非常强大的。

## 使用它

好了，关于木偶如何在引擎盖下工作，我已经让你厌烦够了，让我们看看你实际上是如何使用它的。

首先，以防不明显，或者如果你只是跳到这一部分，木偶需要水晶运行。因此，如果你还没有安装 Crystal】，如果你打算继续的话，你可能想去下载它。你还需要安装 Firefox。

您需要做的第一件事是启动您的终端并创建一个新的 Crystal 项目。

```
crystal init app browser
cd browser 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的`shard.yml`文件中添加一个依赖项。

```
dependencies:
  marionette:
    github: watzon/marionette
    version: 0.1.0 
```

Enter fullscreen mode Exit fullscreen mode

现在在您的终端上运行`shards install`，牵线木偶应该被安装到`lib`目录中。

现在我们打开`src/browser.cr`，添加以下内容:

```
require "marionette"

Marionette.launch do
  goto("https://dev.to")
  puts title
  puts url
end 
```

Enter fullscreen mode Exit fullscreen mode

确保 Firefox 当前没有运行，如果运行的话，这将不起作用，因为 Firefox 一次只允许一个实例。现在在你的终端运行

```
crystal run ./src/browser.cr 
```

Enter fullscreen mode Exit fullscreen mode

几秒钟后，你应该会看到一串蓝色和白色的调试语句

```
DEV Community 👩‍💻👨‍💻
https://dev.to/ 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你。成功了！但这还远远不是牵线木偶所能提供的全部。为什么我们不做些有趣的事情，比如截图。

```
require "marionette"

Marionette.launch do
  goto("https://dev.to")
  save_screenshot("dev.to.jpg", full: false)
end 
```

Enter fullscreen mode Exit fullscreen mode

在撰写本文时,“dev.to.jpg”是这样的:

[![dev.to](img/13f2f3a152b527d972676c52beac253c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ifbILWp8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/DtxmtCW.png)

`full: false`选项允许我们只捕捉视窗中的内容。如果你没有设置这个选项为真，你会得到一个非常大的图像。

在我们最后一个小测试中，让我们访问一些网站，然后导出一个 HAR 文件。HAR 文件是一种 JSON 格式的归档文件格式，用于记录 web 浏览器与站点的交互。基本上，它能够存储关于页面负载的所有细节。

```
require "marionette"

Marionette.launch do
  goto("https://www.google.com")
  goto("https://neuralegion.com")
  goto("https://watzon.tech")
  export_har("multisite.har")
end 
```

Enter fullscreen mode Exit fullscreen mode

运行完成后，您应该在当前工作目录中有一个名为`multisite.har`的文件。你可以通过使用谷歌的 [HAR 分析仪](https://toolbox.googleapps.com/apps/har_analyzer/)来测试它是否工作正常。

我已经尝试在[自述文件](https://github.com/watzon/marionette#readme)和[官方文件](https://watzon.github.io/marionette/)中很好地记录了木偶，所以如果你对这个项目感兴趣，请去看一看。贡献和建议总是被接受的。

感谢你阅读这篇文章。请不要忘记点击自我助推器按钮之一(个人来说，我喜欢独角兽)，如果你这么想分享到社交媒体。如果你分享到 twitter，请务必在@_watzon 标记我。

一些有帮助的链接:
[https://crystal-lang.org/](https://crystal-lang.org/)
[https://github.com/kostya/benchmarks](https://github.com/kostya/benchmarks)
[https://github.com/kostya/crystal-benchmarks-game](https://github.com/kostya/crystal-benchmarks-game)
[https://github.com/crystal-lang/crystal](https://github.com/crystal-lang/crystal)

在线找我:
[https://medium.com/@watzon](https://medium.com/@watzon)
[https://twitter.com/_watzon](https://twitter.com/_watzon)
[https://github.com/watzon](https://github.com/watzon)
[https://watzon . tech](https://watzon.tech)