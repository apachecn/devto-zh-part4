# 我为火狐浏览器克隆了一个流行的 Gmail 扩展。这里是如何！

> 原文：<https://dev.to/paulgaumer/i-built-a-clone-of-a-popular-chrome-extension-for-firefox-here-is-how-4h4o>

当切换浏览器时，最令人沮丧的事情之一一定是一些你最喜欢的插件不可用。我最近试图完全从 Chrome 切换到 Firefox，但中途不得不停止，因为没有我日常使用的扩展: *Rapportive* (现在实际上叫做 Linkedin Sales Navigator)。

[![Alt Text](img/dd44df161d6a958d8b98c92e17d77dc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b3IHjViq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fbi9wsnv8899x509nijh.png)

Rapportive 是一个纯 Chrome 的 gmail 工具，在侧边栏显示你的电子邮件联系人的 Linkedin 简介。您无需离开收件箱就可以访问他们的个人资料图片、姓名、职务、公司和联系级别。当你每周与几十个新人交流时，这种附加功能很快变得至关重要。

几个月前，我决定开发 Rapportive 的 Firefox 版本 reporter T1，同时开发我的第一个 T2 real T3 浏览器扩展的原因。

[![Alt Text](img/71ebf58f2ec2b4adb6ad75c39c2a5ed4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LsEVB_SP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fdm9ti9sdy4anlkw7et9.png)

## 1 -从 Linkedin 获取信息

第一步是确保我可以仅使用电子邮件地址从 Linkedin 检索所需的数据。快速浏览 Linkedin 文档可以发现，该 API 相当有限，不允许访问基于地址的个人资料。

在 Chrome 中仔细查看了 Rapportive 发出的请求后，我注意到调用下面的 URL 会返回我想要的 Linkedin 对象，作为一个 HTML 字符串。

[https://www . LinkedIn . com/sales/Gmail/profile/view bye mail/your _ email _ address](https://www.linkedin.com/sales/gmail/profile/viewByEmail/your_email_address)

在你的浏览器里试试吧！(你需要连接到 Linkedin)

## 2 -了解编码附加组件

第二步是真正弄清楚如何编码和构建一个浏览器插件。这个过程相当简单，主要需要声明两件事:

*   包含附加组件工作和在 Firefox 中显示所需的所有信息
*   包含应用程序的实际逻辑

然后，您可以通过从 Firefox 的首选项:`about:debugging#addons`->-T1】中加载来本地测试您的扩展。

要了解更多细节并从一个非常基本的例子开始，请查看[您的第一个扩展| MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Your_first_WebExtension)

## 3 -将数据注入 Gmail

### 计时

现在，我有了一个外壳，可以加载和访问格式良好的数据(感谢 Linkedin)，我只需要将它注入到我们的 gmail 布局中。嗯……事情没那么简单。

Gmail 的 DOM 就像一个来自地狱的洋葱，骄傲地展示着无限的层数。具有疯狂类名的嵌套 div 和 spans，所有加载都有不同的计时。

在找到能够托管我们侧边栏的 div 之后，主要的挑战是找到注入我们内容的好时机。同样，gmail 界面的每个元素在不同的时间加载，我的脚本第一次运行得太早，在 div 实际创建之前。听`DOMContentLoaded`和其他页面生命周期事件不能做到这一点。

你可以在这里了解更多关于这些事件的信息。

作为备份计划，我选择通过一个`setInterval`来周期性地监听所需 div 的存在。成功了！

```
const windowLoaded = setInterval(function() {
    if (document.querySelector(".y3") !== null) {
      clearInterval(windowLoaded);
      // RUN YOUR CODE
  }, 100); 
```

### Gmail API

从 email 对象中提取联系人的电子邮件地址并运行 fetch 请求后，Linkedin 窗口成功显示在侧栏中。耶！

下一步是确保在新页面中打开新邮件时再次触发获取请求。为此，我需要一种方法来知道我是在收件箱视图、线程视图还是电子邮件视图中。在这个阶段，我发现了两个第三方 API，它们有助于轻松操作和收听 gmail 界面。

第一个是 [**InboxSDK**](https://www.inboxsdk.com/)
设置很简单，我很快通过了几个测试，直到意识到该 API 只能在 Chrome 和 Safari 中工作。下一个！

第二个是 [**Gmail.js**](https://github.com/KartikTalwar/gmail.js)
我发现设置稍微复杂一些(需要节点包和构建工具),文档不如 InboxSDK 清晰。这个库在 Firefox 上运行，提供了我需要的方法。但是在我的例子中，只有少数几个真正起作用(我甚至不得不在某个时候修改包文件中的源代码)。对我来说太不稳定了，使用 API 也产生了 CORS 问题。回到起点！

在一次类似经历的启发下，我决定简单地倾听 url 的变化并做出相应的反应。嘣！

```
let currentUrl = document.location.href;
setInterval(function() {
  if (document.location.href != currentUrl) {
    currentUrl = document.location.href;
    // RUN YOUR CODE
  }
}, 500); 
```

## 4 -发布到 Mozilla 的附加组件库

几个小时的编码、调整和试验之后，Rapporteur 终于准备好了。为了让用户轻松获得一个插件，你只需要发布它并将其列在 Mozilla 的插件库上。

该页面将介绍所需的所有步骤:[提交附加产品| MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/Distribution/Submitting_an_add-on) 。

你现在可以在这里为火狐安装*报告员*([报告员——获得这个扩展🦊](https://addons.mozilla.org/en-US/firefox/addon/rapporteur/))或者在这里看源代码:[GitHub-paulgaumer/reporter](https://github.com/paulgaumer/rapporteur)。

非常感谢你的阅读，请随时让我知道你的想法！