# 浏览器 API 的本地化:为什么不能这样做，Chrome？

> 原文：<https://dev.to/room_js/browser-api-s-localization-why-can-t-you-do-this-chrome-1epk>

嘿，德夫斯！我想分享我发现的一件有趣的事情，并询问您对此的看法。

我在玩`Date`的`toLocaleString()`方法，只为了用浏览器的 API 翻译一个月份名称。代码看起来像这样:

```
const date = new Date();
date.toLocaleString('en-GB', { month: 'long' }); 
```

我在我的 Chrome 浏览器上用几种不同的语言测试了它，一切都很完美。有趣的部分在我发布到 Instagram 上时已经出现了:

[https://www.instagram.com/p/B1_r-2RCKcW/embed/captioned](https://www.instagram.com/p/B1_r-2RCKcW/embed/captioned)