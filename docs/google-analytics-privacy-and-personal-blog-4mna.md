# 谷歌分析、隐私和个人博客

> 原文：<https://dev.to/stereobooster/google-analytics-privacy-and-personal-blog-4mna>

我想开始我的个人博客，我需要那里的分析。我想用 Hugo(或者另一个静态站点生成器)，用 Netlify(或者 now，或者 S3 + CloudFlare)托管。这意味着我需要使用第三方服务来收集分析，对不对？我不打算赚钱博客(不知道如何？)，所以我不想为分析服务付费。我想最便宜的是以每月 5 美元的价格租用数字海洋服务器(例如每年 60 美元)。但是谷歌分析的费用是 0 美元。另一方面，我担心来访者的隐私。

这里的演示是[这里的](https://stereobooster.netlify.com/)。源代码是[这里的](https://github.com/stereobooster/hugo-pipes-parcel/tree/basic-theme)。

## 当前解

现在，我决定使用谷歌分析和 ganalytics。

在将任何数据保存到用户设备之前，我需要征得同意

```
<div class="cookie" hidden>
  <p>
    Can we store cookies on your device? See <a href='{{ .Site.BaseURL }}privacy/'>Privacy Policy</a> for details.<br>
    <button type="button" class="cookie-yes">Yes</button> <button type="button" class="cookie-close">Close</button>
  </p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果我没有得到同意，我会向 Google analytics 发送[匿名请求](https://developers.google.com/analytics/devguides/collection/protocol/v1/parameters#aip)，但不在设备上存储任何标识符。

如果我同意，我会发送一个带有存储 id 的常规请求。

有一种表格，用户可以用它来撤销同意并删除本地存储的标识符。

```
<form class="consent">
  You can revoke consent and remove unique identifier ("cookies") at any time using the form below.
  <noscript>This form needs JavaScript enabled in the browser in order to work</noscript>
  <br><br>
  <input type="radio" id="agree" name="consent" value="agree">
  <label for="agree">I <b>allow</b> to store a unique identifier on my computer to track my visits to this site.</label>
  <br><br>
  <input type="radio" id="disagree" name="consent" value="disagree">
  <label for="disagree">I <b> don't allow</b> to store unique identifier on my computer.</label>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

该表格在带有隐私政策的页面上，其中解释了网站使用 Google analytics 和数据发送时间。

再销售功能已关闭。广告报告功能已关闭。我不想和谷歌分享数据。

我只是想知道我的帖子的浏览量，流量的主要来源(搜索，社交，推荐)，也许地区-没有什么花哨的。

## 寡妇

你觉得我的解决方案怎么样？从隐私角度来说公平吗？我错过了什么吗？