# 如何用@media 在 CSS 中实现黑暗模式

> 原文：<https://dev.to/joelvarty/how-to-implement-dark-mode-in-css-with-media-3d02>

黑暗模式在 Windows 和 MacOS 以及 iOS 设备中都是一个非常受欢迎的功能。

现在，有了这些操作系统上的最新浏览器，您也可以在您的网站或基于 webview 的应用程序上提供黑暗模式体验。

这很简单，只需将所有黑暗模式覆盖封装在一个特定的媒体查询中，该查询仅在浏览器操作系统上检测到黑暗模式时激活。

就是这里:

```
@media (prefers-color-scheme: dark) {

} 
```

Enter fullscreen mode Exit fullscreen mode

我希望这能帮助一些人在他们的网站或应用程序上实现黑暗模式！

...

想要更多吗？我在这里写了一个完整的教程，带你经历一个使用 npm 和 bulma 实现黑暗模式主题的漫长过程。