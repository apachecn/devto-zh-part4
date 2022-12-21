# 一键启动您的桌面 PWA 及其服务器

> 原文：<https://dev.to/mwauramuchiri/starting-your-desktop-pwa-and-it-s-server-in-one-click-5ekd>

您刚刚创建了一个应用程序来帮助您完成一些任务。它可以是待办事项、提醒应用、日历应用、电子邮件...我的用例是一个 emailer 应用程序，它使用 [BeeFree.io](https://beefree.io) 和 [Nodemailer](https://nodemailer.com/) 创建一个电子邮件模板，通过 SMTP 发送电子邮件。

#### *此贴仅针对本地提供的桌面 pwa*

首先要把你的 PWA 安装成桌面应用。现在当你的服务器没有运行时打开应用程序会带来 404 错误，因为网址，比如说**T3 http://localhost:8000T5】并不存在。所以你必须在打开 PWA 之前启动服务器。分开做比做 PWA 更合理。我们希望实现真正的应用程序的体验。已安装应用程序的功能通常是:**

1.  应用程序图标出现在 windows“开始”菜单中
2.  一切只需一键启动。

为了实现这些，您需要在您的应用程序文件夹中创建一个 bash 脚本。这将有助于启动节点/wamp 服务器。在脚本文件中，找到并启动您的服务器。

**对于节点 js 服务器；**

```
cd "*app server folder*"
node server.js 
```

**对于 Wamp 服务器；**
查看 StackOverflow 关于在 cmd 上启动 wamp 服务器的回答

<header>

# ![](img/5dbb32dd8e7a84fd6febfe0cdccb56ed.png) [如何编程重启 WAMP 或 Apache？](https://stackoverflow.com/questions/7665706/how-to-programmatically-restart-wamp-or-apache)

Oct 5 '11 Comments: 4 Answers: 4[![](img/a065da4e8eb02494bbdf9da25e72307b.png)7![](img/0a5dbf7e796d422711f0ba4f7b944281.png)](https://stackoverflow.com/questions/7665706/how-to-programmatically-restart-wamp-or-apache) </header>

作为我用来验证站点编程的一些自动化部署+测试脚本的一部分，我有一些更新 Apache 配置文件的脚本。我想以编程方式重新启动 WAMP，使更改生效。有没有好的方法可以做到这一点？

这些脚本是…

<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/7665706/how-to-programmatically-restart-wamp-or-apache)</button>

现在保存脚本，然后创建它的快捷方式文件。您应该将快捷方式文件的图标更改为应用程序的 favicon 图标，并将其重命名为应用程序的名称(移除“- shortcut”名称)。将快捷方式文件放在 Window 的开始菜单文件夹内，一般在*C:\ Users ~ ~ user _ name ~ ~ \ AppData \ Roaming \ Microsoft \ Windows \开始菜单*。请注意，真正的 pwa 应用程序将位于开始菜单文件夹中，在程序/Chrome 应用程序文件夹内(假设您使用 Chrome 浏览器安装了 PWA)。
你现在应该看到应用程序图标出现在 windows 开始菜单中。点击它，它将启动服务器很好，很容易。

服务器启动后。现在我们需要打开 PWA。这基本上是模拟单击 PWA。我用节点 *server.js 文件*通过使用[节点打开模块](https://github.com/pwnall/node-open/)打开。在 node js 中自己编码是相当容易的，只是如果已经编码了，对你来说就更容易了！~~尽可能地懒惰。你是开发商~~

在服务器启动后的服务器文件中，只需要打开 PWA 模块。

```
 var open = require("open");

open(
  "C:/Users/~~user_name~~/AppData/Roaming/Microsoft/Windows/Start Menu/Programs/Chrome Apps/Emailer"
); 
```

注意 url 指向 PWA 并注意 url？使用应用程序 url 在浏览器中打开应用程序，而不是在安装的 PWA 中打开。这种攻击是暂时的，因为 Chrome 已经在考虑用**链接捕获**，这是一个有趣的名字，每当在浏览器中打开与应用程序相关的 url 时，就会打开 PWA。

对于 wamp 服务器，我还没有尝试打开 PWA，但是我很确定有一种方法可以使用 bash 脚本。

桌面 PWA 公告:*[https://appuals . com/Microsoft-and-Google-collabove-for-PWA-platform-on-windows-10/](https://appuals.com/microsoft-and-google-collaborate-for-pwa-platform-on-windows-10/)*