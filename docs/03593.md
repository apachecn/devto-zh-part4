# 查尔斯代理自动化公司

> 原文：<https://dev.to/mmazzarolo/charles-proxy-automation-1884>

Charles Proxy 是一个非常棒的跨平台 HTTP/HTTPS 调试代理服务器应用程序，可能是同类程序中最用户友好的。它的初始设置是没有痛苦的，并且可以通过一个漂亮的用户界面进行配置。

我最近开始比平时更多地使用它，所以今天我花了一些时间来检查如何通过命令行界面自动使用 Charles。

> 由于是跨平台的，Charles 兼容 MacOS、Linux 和 Windows。这篇文章中的说明是基于 MacOS 的，所以如果你打算在另一个 OS 上遵循它，它们可能需要一些小的调整。

## 查尔斯命令行选项

Charles 支持一些现成的命令行选项，记录在这里的。

不幸的是，它们似乎只作为新的 Charles 会话的参数运行，所以您将无法在正在运行的 Charles 实例上运行命令。

**开始特定的查尔斯会话**

一个[查理斯会话](https://www.charlesproxy.com/documentation/using-charles/sessions/)包含你所有的记录信息。它由会话窗口表示；默认情况下，当您启动 Charles 时，会自动创建一个新会话。

会话可以从**文件→保存会话** ( `⌘` + `S`)中保存。

保存后，如果需要，可以通过运行
从保存的会话中启动 Charles

```
/Applications/Charles.app/Contents/MacOS/Charles YOUR_SAVED_SESSION_PATH 
```

Enter fullscreen mode Exit fullscreen mode

**使用特定配置启动 Charles】**

这一部分没有很好地记录，所以我不得不在设置中挖掘一点，以了解它是如何工作的。

默认情况下，查尔斯将其配置存储在`/Users/YOUR_USER/Library/Preferences/com.xk72.Charles.config`中，这是一个可读的 xml 文件，其中包含了**所有的**查尔斯设置。

如果需要，你可以将这个文件复制到另一个位置，调整你感兴趣的设置，然后直接从这里启动 Charles:

```
/Applications/Charles.app/Contents/MacOS/Charles -config YOUR_CONFIG_PATH 
```

Enter fullscreen mode Exit fullscreen mode

请注意，只有当 Charles 没有运行时，您才可以手动编辑配置文件。

**在启用节流的情况下启动查尔斯**

简单易行:

```
/Applications/Charles.app/Contents/MacOS/Charles -throttling 
```

Enter fullscreen mode Exit fullscreen mode

**以无头模式启动查尔斯**

您可以在 headless 模式下运行 Charles，这样就不会出现 UI，但它仍然会代理内容。

```
/Applications/Charles.app/Contents/MacOS/Charles -headless 
```

Enter fullscreen mode Exit fullscreen mode

你怎么能在无头模式下控制查尔斯？你可以使用它的网络界面。

## 查尔斯网络接口和网络服务

Charles 有一个 [web 界面](https://www.charlesproxy.com/documentation/using-charles/web-interface/)，它使你能够从浏览器控制 Charles，或者使用一个外部程序将 web 界面作为 web 服务。

web 界面可以启用**代理→ Web 界面设置**。您可以允许匿名访问，或者配置用户名和密码。您可以通过在配置为使用 Charles 作为其代理的 web 浏览器中访问 http://control.charles/的[来访问 web 界面。](http://control.charles/)

web 接口本身是一个很好的补充，但是对于我的用例来说，它最有趣的部分是它公开了几个端点，这些端点可以用来以编程方式更改正在运行的 Charles 实例的配置。

它们没有被记录，但是，根据官方网站的建议，我可以从网络界面中提取它们。

假设:

*   `192.168.1.41`是您的 Mac 的 IP 地址
*   查尔斯正在使用端口`8888`(它的默认端口)

你可以这样调用 cURL 命令:

```
curl -v -x http://192.168.1.41:8888 http://control.charles/YOUR_COMMAND 
```

Enter fullscreen mode Exit fullscreen mode

### 可用命令

**节流命令**

*   `/throttling/deactivate`
*   `/throttling/activate`:启用上次使用的节流预设。
*   `/throttling/activate?preset=YOUR_PRESET`:使用以下特定选项之一启用节流:
    *   56 千比特/秒的调制解调器
    *   `256+kbps+ISDN%2FDSL` : 256 kbps ISDN/DSL
    *   `512+kbps+ISDN%2FDSL` : 512 kbps ISDN/DSL
    *   `2+Mbps+ADSL` : 2 Mbps ADSL
    *   `8+Mbps+ADSL2` : 8 Mbps ADSL2
    *   `16+Mbps+ADSL2%2B` : 16 Mbps ADSL2+
    *   VDSL:32 兆比特/秒
    *   `32+Mbps+Fibre` : 32 Mbps 光纤
    *   `100+Mbps+Fibre` : 100 Mbps 光纤
    *   `3G` : 3G
    *   `4G` : 4g

**录制命令**

*   `/recording/start`
*   `/recording/stop`

**工具命令**

*   `/tools/breakpoints/enable`
*   `/tools/breakpoints/disable`
*   `/tools/no-caching/enable`
*   `/tools/no-caching/disable`
*   `/tools/block-cookies/enable`
*   `/tools/block-cookies/disable`
*   `/tools/map-remote/enable`
*   `/tools/map-remote/disable`
*   `/tools/map-local/enable`
*   `/tools/map-local/disable`
*   `/tools/rewrite/enable`
*   `/tools/rewrite/disable`
*   `/tools/black-list/enable`
*   `/tools/black-list/disable`
*   `/tools/white-list/enable`
*   `/tools/white-list/disable`
*   `/tools/dns-spoofing/enable`
*   `/tools/dns-spoofing/disable`
*   `/tools/auto-save/enable`
*   `/tools/auto-save/disable`
*   `/tools/client-process/enable`
*   `/tools/client-process/disable`

### 获取设置状态

使用上面显示的相同模式，您还可以获得特定设置的状态(`enabled` / `disabled`)。

不幸的是，它涉及到解析网页界面的 HTML 输出，但从我的测试来看，它似乎一直工作正常:

假设你想获得**重写**设置状态，你可以运行:

```
curl -v -x http://192.168.1.41:8888 http://control.charles/tools/rewrite/ 
```

Enter fullscreen mode Exit fullscreen mode

它会给你以下的 HTML:

```
<html>
  <head>
    Charles Web Interface
    <link rel="stylesheet" href="../../css/plain.css" />
  </head>
  <body>
    <h1>Charles Web Interface</h1>
    <h2>Rewrite</h2>
    <p>Status: Enabled</p>
    <ul>
      <li><a href="enable">Enable</a></li>
      <li><a href="disable">Disable</a></li>
      <li><a href="../">Back</a></li>
    </ul>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细观察，你可以很容易地发现状态，它总是以下列方式出现:

```
<p>Status: STATUS</p> 
```

Enter fullscreen mode Exit fullscreen mode

### 2019-10-07 更新:

发表这篇文章后，我在 Twitter 上联系了[@ Charles proxy](https://twitter.com/charlesproxy)，开始讨论我们可以做些什么来允许通过 CLI 编辑设置(例如:添加新的重写规则):

> ![Charles Proxy profile image](img/926dd9ecbad12d08d04e0fd502795337.png)查尔斯代理@查尔斯代理![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)[@ mazzarolomatteo](https://twitter.com/mazzarolomatteo)嗯。好问题。没有，但是也许我们可以在控制 API 中添加一个重新加载设置命令？2019 年 9 月 22:56PM-06[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=1170108587978313728)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=1170108587978313728)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=1170108587978313728)