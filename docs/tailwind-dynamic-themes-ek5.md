# 顺风动态主题...救命啊！

> 原文：<https://dev.to/codingcatdev/tailwind-dynamic-themes-ek5>

# 爱的顺风！需要动态主题...救命啊！

顺风又让我觉得写 HTML 很有趣了！我真的很喜欢把我的网站移植到 Gatsby，不使用 web 组件，真的只是回到 html！然而，这其中缺少的一部分是动态主题。正如你在视频中看到的，我真的很喜欢为我的用户提供这种简单的功能。

[https://www.youtube.com/embed/SR6Ty-5Cy3M](https://www.youtube.com/embed/SR6Ty-5Cy3M)

## 这个为什么不行？

在我的盖茨比项目中加入顺风之后，我只是用它来设置背景:

```
<div class="bg-ajonp-primary-500"></div> 
```

Enter fullscreen mode Exit fullscreen mode

当您在调试工具中查看时，它用包含十六进制颜色的类的直接 css 来表示。

```
.bg-ajonp-primary-500 {
    background-color: #7b1fa2;
} 
```

Enter fullscreen mode Exit fullscreen mode

我想说的是

```
.bg-ajonp-primary-500 {
    background-color: var(--ajonp-primary-500);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 研究至今

我围绕这个插件做了一些研究。

[https://www.npmjs.com/package/tailwind-css-variables](https://www.npmjs.com/package/tailwind-css-variables)

我不完全确定是否会使用默认的 tailwind 类引用任何颜色类。所以这是我需要帮助理解的部分。

## 我如何在 Ionic 中完成这个？

我一直使用离子 v4(又名核心)所有组件利用根级别的 css 变量进行引用。这使得我们可以通过改变这些变量来轻松切换这些主题。下面的 pull 请求展示了如何在任何 Ionic 项目中使用它的一个很好的例子。

我已经提交了一份申请，要求将它添加到 Ionic 文档中。

[https://github.com/ionic-team/ionic-docs/pull/929](https://github.com/ionic-team/ionic-docs/pull/929)

## 你做过这种事或认识任何可能知道的人吗？

如果有人有真知灼见，我将不胜感激！如果听到的评论感觉不好，你可以通过以下方式联系我(尽管在我看来这是一个安全的地方)。

🌎地点:[https://ajonp.com](https://ajonp.com)🎥YouTube:[https://bit.ly/ajonp-youtube-sub](https://bit.ly/ajonp-youtube-sub)T5】📦GitHub:[https://github.com/ajonpllc](https://github.com/ajonpllc)
💬懈怠:[https://bit.ly/ajonp-slack-invite](https://bit.ly/ajonp-slack-invite)T11】🐦推特:[https://bit.ly/ajonp-twitter](https://bit.ly/ajonp-twitter)