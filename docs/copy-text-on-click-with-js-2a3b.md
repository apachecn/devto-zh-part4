# 用 JS 点击复制文本

> 原文：<https://dev.to/polettoweb/copy-text-on-click-with-js-2a3b>

昨天，我偶然发现用 JavaScript 将一些文本复制到剪贴板的必要性，我认为与其他人分享我使用过的方法可能会有用。

具体来说，我不得不在彩色样本上点击复制一些十六进制值，我不得不在普通 JS 中完成它。

我的方法使用每次点击自动传递的“事件”。

*   它选择被单击元素(event.target)内的文本
*   并将其复制到剪贴板中。
*   出于演示目的，它会打开一个警告框，通知所选的十六进制代码

我发现它对设计师分享颜色特别有用，但当然也可以用于许多其他方面(例如仪表板、点击时的表单预填充等)。)

[https://codepen.io/polettoweb/embed/KjazEZ?height=600&default-tab=result&embed-version=2](https://codepen.io/polettoweb/embed/KjazEZ?height=600&default-tab=result&embed-version=2)