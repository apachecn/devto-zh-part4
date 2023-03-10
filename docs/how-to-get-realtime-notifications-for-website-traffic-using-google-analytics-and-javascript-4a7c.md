# 如何使用谷歌分析和 JavaScript 获得网站流量的实时通知

> 原文：<https://dev.to/prjseal/how-to-get-realtime-notifications-for-website-traffic-using-google-analytics-and-javascript-4a7c>

## 我爱统计

如果你和我一样，那么你也会对你网站的访问者统计感兴趣。当你的网站有超过一定数量的访问者时，你会很兴奋。

你可能会花一些时间看实时统计数据，等着看数字上升。当有很多访问者访问你的网站时，你可能会错过它，你可能希望有一种方法可以实时通知你网站上的大量用户。

有了这段代码，你可以设置它在实时访问者的数量达到你感兴趣的数量时向你发送通知。

[![enter image description here](img/639b6158258bf49ac75d28ba57a666a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mwqUGOES--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codeshare.co.uk/media/1512/visitor-stats.png)

## 使用 Google Analytics 实时指标

有了这段代码，你可以打开 google analytics，进入实时概览页面，右键点击实时访问者数量，然后点击 inspect。然后转到控制台选项卡，粘贴这段代码。你需要用你自己的网址替换掉 slack webhook 网址。

## 给我代码

```
(function checkStats() {
    var threshold = 10;
    var waitTimeInSeconds = 30;
    var number=document.getElementById("ID-overviewCounterValue").innerHTML;
    if(number > threshold) {
        var url = "https://hooks.slack.com/services/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"; //replace this with your slack webhook url
        var text = "There are " + number + " visitors on the site right now.";
        $.ajax({
            data: 'payload=' + JSON.stringify({
                "text": text
            }),
            dataType: 'json',
            processData: false,
            type: 'POST',
            url: url
        });
    }
    setTimeout(checkStats, waitTimeInSeconds * 1000);
})(); 
```

## 向 Slack 注册您的 WebHook Url

[点击此处设置传入网页挂钩](https://my.slack.com/services/new/incoming-webhook/)

*   登录到 Slack
*   选择发布到的频道
*   然后点击绿色按钮添加传入的 WebHooks 集成
*   你会得到一个网页挂钩网址。保密。

**确保将代码中的 url 替换为你的 slack webhook url**

## 通知太多？

编辑阈值数和秒数，以适应您的站点容量和检查频率。

如果这让你烦恼，只需刷新页面，重复这些步骤，编辑阈值和秒数，以减少检查频率。

## [T1】Chrome 扩展？](#chrome-extension)

你可能会想这应该是 chrome 的扩展。已经有一些了，一些是免费的，一些是付费的。我只想得到一些对我有用的简单的东西。请随意使用我的代码，并在 Chrome 扩展中使用它，但如果你这样做，请让它成为免费的。