# 用不严格的命令打某人

> 原文：<https://dev.to/codingcatdev/slap-someone-with-slack-commands-23oa>

> 原帖:[https://ajonp.com/lessons/slap-someone-with-slack-commands/](https://ajonp.com/lessons/slap-someone-with-slack-commands/)

[https://www.youtube.com/embed/3A4iucpZiwo](https://www.youtube.com/embed/3A4iucpZiwo)

# 松松垮垮

因此，频道上的一个完美派想要在我们的空闲频道上添加一个有趣的`/slap`命令。我基本上说了，接受挑战！快速的谷歌搜索让我走上了正确的道路，找到了[香料工厂 Slapbot](https://spicefactory.co/blog/2015/12/09/slapbot-for-slack-good-old-slap-available-again/) 。我克隆了这个，然后开始做一些调整！

[![slap challenge](img/1e199f1bb2d0fd64ce8c89b2f6640feb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EduXA7tz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566663387/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_12.08.04_PM.png)

## 安装

> 请注意，您可以将它作为独立的节点服务器运行，但是我选择使用 Firebase Cloud 函数，因为我的计划是在以后添加一些机器学习。

在继续之前，我提交了文件`.firebaserc`和`firebase.json`，主要是为了让我可以继续进行更改。对于您的项目，您需要在开始项目设置和 Firebase 初始化之前删除这些文件。

## 初始化 Firebase

> 设置 [Firebase CLI](https://firebase.google.com/docs/cli) 的文档。

```
firebase init 
```

Enter fullscreen mode Exit fullscreen mode

当出现你的选择时，向下箭头并点击空格键`Functions: Configure and deploy Cloud Functions`。

[![Firebase Functions select](img/1630f3d62a3db516e8944117666224d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aWPnus4t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566664040/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_12.26.04_PM.png)

如果你想开始使用一个新项目，选择`Create a new project`，查看 [Firebase Init Docs](https://firebase.google.com/docs/cli) 获取更多信息。

之后，您可以选择 Javascript

[![JS selection for firebase init](img/9df9cc0803fdc09ca7c1bb9de93e9f8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f3ijdaZt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566664040/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_12.26.20_PM.png)

对于最终设置，除了“是否要立即安装与 npm 的依赖关系？”，对于这个 Y(是)。

[![Firebase init Selections](img/e31b3abdbc7b62cf455594a9d9882185.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fvB8bhpC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566664040/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_12.27.07_PM.png) 。

现在你已经完成了，你应该会看到两个新文件`.firebaserc`和`firebase.json`。

## 部署功能

按照现在的情况，这将允许您进行本地测试(这不是本课的目的)。我们真正想要的是将它部署到 Firebase，以便 Slack 可以访问它。因为我们的项目应该已经设置好了，所以我们只需要使用 deploy 命令。

为了让我们访问外部网络，你需要为这个项目切换你的 Firebase 订阅，我推荐 Blaze 计划，因为你将不会被收费，直到你一个月调用 125，000 次，我不知道你的 slack 风扇，但这将是非常令人印象深刻的！您可以选择屏幕左下方的修改项目，它应该会打开一个如下所示的模态。

！[Firebase Modal([https://RES . cloud inary . com/ajonp/image/upload/v 1566667914/ajonp-ajonp-com/blog/Screen _ Shot _ 2019-08-24 _ at _ 1 . 31 . 47 _ pm . png](https://res.cloudinary.com/ajonp/image/upload/v1566667914/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_1.31.47_PM.png))

现在是时候部署我们的功能了:

```
firebase deploy 
```

Enter fullscreen mode Exit fullscreen mode

它应该成功地创建了一个云函数，输出如下: [![cloud deploy output](img/d1250168714c68787b142082c863da2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OeSB-ud7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566667465/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_1.24.11_PM.png)

你可以在 [Firebase 控制台](https://console.firebase.com/)的开发- >功能下查看

[![Functions in Console](img/f37cd10907a5fa6bce28efe6377d5bec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gwPBzWSn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566667721/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_1.28.22_PM.png)

### 本地测试的例子

```
firebase serve 
```

Enter fullscreen mode Exit fullscreen mode

该命令将输出模拟器运行的位置。

```
=== Serving from '/Users/ajonp/Documents/Web/slapbot'...

⚠ Your requested "node" version "8" doesn't match your global version "10"
✔ functions: Emulator started at http://localhost:5000
i functions: Watching "/Users/ajonp/Documents/Web/slapbot/functions" for Cloud Functions...
✔ functions[api]: http function initialized (http://localhost:5000/ajonp-slack-slap/us-central1/api). 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你想对它使用一些 curl 命令，你可以:

```
curl -X POST \
  'https://us-central1-ajonp-slack-slap.cloudfunctions.net/api/slap?callback=YOURCALLBACK' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'cache-control: no-cache' \
  -d 'user_name=ajonp&text=slappy&channel_id=1234&undefined=' 
```

Enter fullscreen mode Exit fullscreen mode

## 松弛配置

转到 Slack 服务部分 https://.slack.com/apps。比如说[https://ajonp-com.slack.com/apps](https://ajonp-com.slack.com/apps)。

### 传入 Webhook

首先，我们需要一种方法让 Slack 接受我们的`/slap`消息，为我们的账户返回 Slack 的系统。我们将添加一个入站 Webhook 来允许这个交互成功。

[![slash incoming webhooks](img/2ea808f59783d203d1c60961c1c38540.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zToJrmTB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566668484/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_1.37.36_PM.png)

*   选择要发布的任何频道(不要担心，当你打耳光时，机器人会使用你输入的频道)
*   复制并粘贴你得到的 URL 的令牌部分(类似于:`https://hooks.slack.com/services/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`
*   所以它将是`/T2UR5KNQ2/BMNTDRAG0/mPToTmuARk2BqYhn93Izzbbb`部分，因为我们将需要它来执行斜杠命令。

[![Full configuration](img/0444206e914ce21e3ea0546dc03edada.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KNzOkjuO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566668484/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_1.41.05_PM.png)

### 斜线命令 App

现在我们需要添加和应用程序来配置斜杠命令。

[![Slash Command Search](img/62a58f1b8bc32c3a8efd1aa08e9cab2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qk4tl2A_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1566667140/ajonp-ajonp-com/blog/Screen_Shot_2019-08-24_at_12.49.28_PM.png)

在 url 中输入您的服务 URL 或者您可以使用我们的-> `https://us-central1-ajonp-slack-slap.cloudfunctions.net/api/slap?callback=YOURCALLBACK`。

现在你有了一个可以与之互动的工作机器人，但这是你和他之间的私人交流，所以你可以说这并不有趣。让我们变得更有趣！

*   取`services`之后的部分，如上例所示，它将是- > `/T2UR5KNQ2/BMNTDRAG0/mPToTmuARk2BqYhn93Izzbbb`
*   回到 Slash 命令，编辑 URL，从上面添加令牌部分，这样它应该在末尾，类似于`https://us-central1-ajonp-slack-slap.cloudfunctions.net/api/slap?callback=/T2UR5KNQ2/BMNTDRAG0/mPToTmuARk2BqYhn93Izzbbb`。

顺便把`https://us-central1-ajonp-slack-slap.cloudfunctions.net/api`替换成你的网址，或者随意使用这个。

## 闲置时的用法

在最简单的形式中，你只需输入:

```
/slap @chouse 
```

Enter fullscreen mode Exit fullscreen mode

这会告诉 slapbot 用一个鱼表情符号给 Chris 一个大大的耳光。您也可以通过键入:
来提供自己的表情符号

```
/slap @chouse :hot_pepper: 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，克里斯将被表情符号扇耳光🌶。LOL！

默认情况下，机器人还会在结尾包含一些戏谑。请继续阅读，了解如何对此进行配置。

## 配置机器人行为

*   要编辑随机戏谑字符串，更改`helpers.js`中的`banterArray`变量。
*   要删除戏谑，请从`server.js`中`botPayload`的`text`属性中删除`banter`变量。
*   要更改机器人名称或图标，请更改`server.js`中`botPayload`的`user_name`和`icon_emoji`属性。
*   要更改默认表情符号，请更改`constants.js`中的`DEFAULT_EMOJI`变量。

恭喜，享受掌掴吧！