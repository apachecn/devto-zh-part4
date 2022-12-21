# 原生 web 组件-样式阴影 dom -如何？

> 原文：<https://dev.to/anpfeiffer/native-web-components-styling-shadow-dom-how-e1a>

嘿，

我在做原生组件。嗯，edge 和 ie 似乎有很多问题，但 chrome 和 firefox 等浏览器可以解决。看起来像。

我有一个包含 css 类的 index.html 文件。我希望这个类或者至少其中的一些能在我的组件的影子 dom 中工作。

我找到了 chrome 使用::part()的方法。这样我就可以给我的组件“share-button”赋予样式了

好吧。在 chrome 和 opera 上运行得很好，但在 firefox(最新版本)上就不行了。

有没有办法为不支持::part()浏览器提供支持？

谢谢你