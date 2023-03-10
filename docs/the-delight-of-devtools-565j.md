# 浏览器开发工具的乐趣

> 原文：<https://dev.to/bugmagnet/the-delight-of-devtools-565j>

我当时在 Moz.com 的网站上，在他们的通知面板里(不登录就进不去，我也不会给你我的！)

在那里，我有 800 多份 CSV 报告在等待，这些报告是在 6 月份自动生成的。它们都需要存档。唯一的方法就是点击“消息”复选框，直到选中 25 条，然后点击“存档”按钮。

做 32 次的想法没有吸引力。用 Selenium 编写脚本也不行。所以我想到了使用浏览器的 devtools 窗口，在这里是 Chrome 的。

我右键单击“Messages”复选框，检查元素，并设计出一个简单的 CSS 路径。

```
var cb = document.querySelectorAll("th input[type=checkbox]")[0]; 
```

Enter fullscreen mode Exit fullscreen mode

我对“存档”按钮做了同样的操作。

```
var ar = document.querySelectorAll('button[test-action="archive"]')[0]; 
```

Enter fullscreen mode Exit fullscreen mode

我注意到我不得不点击复选框两次，最后显示如下:

```
cb.click();cb.click();ar.click() 
```

Enter fullscreen mode Exit fullscreen mode

在控制台中，简单地向上箭头并按回车键，直到所有的消息都被归档，这样就简单快捷多了。

啊，浏览器开发工具的乐趣！