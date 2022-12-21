# 使用 JavaScript 生成的 CSS 自定义属性作为移交机制

> 原文：<https://dev.to/codepo8/using-css-custom-attributes-generated-by-javascript-as-a-handover-mechanism-33ca>

*更新:*有一个关于自定义属性不支持连接的简化，感谢 Sime Vidas、Brian Kardell 和 Greg 惠特沃思纠正了这个问题。

在 CSS 的最后几次迭代中，CSS 和 JavaScript 之间的界限开始变得模糊。CSS 是一种静态语言，旨在定义颜色、外观和感觉，而不是交互性。你可以在维度中使用百分比作为对环境的一种交互，但是对事物变化的反应是 JavaScript 的工作。

在过去的日子里，HTML 提供了结构、CSS 外观和感觉以及 JavaScript 交互性。或者，正如我在 2006 年的书中所说，如果你的网站是一部电影，HTML 就是剧本，CSS 就是电影摄影和导演，JavaScript 就是特效。

现在的 CSS 厉害多了。我们有动画、过渡、calc()和更灵活的值，如 em、rem、vw、vh 等等。我们还可以与伪选择器进行交互，比如悬停、焦点和按钮等交互元素的状态。我们甚至可以用复选框来编写纯 CSS 的完整游戏。

这太棒了！CSS 爱好者更有可能有耐心和知识来制作看起来和行为完全正确的动画或交互。和 CSS 引擎负责很好地执行，并且不影响最终用户设备的交互性或电池寿命。浏览器制造商可以专注于优化引擎，而不是与开发者竞争谁有责任保持事情顺利进行。

但是，仍然存在 CSS 不够用的边界和用例，您需要 JavaScript。通常这些是关于读取浏览器窗口发生的事情的当前状态，或者 CSS 规范中没有考虑的交互。

在这种情况下完全切换到 JavaScript 感觉像是下意识的反应，对我来说更有意义的是找到 JavaScript 和 CSS 交互的方式。JavaScript 读取该值，并以某种方式使其对 CSS 可用。

在过去，唯一的方法是在父元素上存储类，或者在满足某些条件时移除类。但是有了自定义属性(“CSS 变量”)，JavaScript 和 CSS 之间的交互变得容易多了。

自定义属性允许您在 CSS 中设置“变量”并在以后使用它们。例如:

```
::root {
 --company-blue: #369;
}
h1 {
 color: var(--company-blue);
} 
```

自定义属性有一定的限制，不像在 CSS 预处理程序中那样是字符串，所以你不能轻易连接它们自定义属性的工作方式与预处理程序中的 CSS 变量不同。您可以将它们连接起来，但是有一些限制。

感谢 ime Vidas】在 Twitter 上展示了一个工作演示，感谢 [Brian Kardell](https://twitter.com/briankardell) 将[引向 CSS 标准组](https://github.com/w3c/csswg-drafts/issues/542)的讨论。

正如我的同事格雷·惠特沃思解释的那样:

> 关于定制道具，这实际上是不正确的。我想你主要指的是 CSS 中的潜在限制，虽然我注意到 Sime 已经向你展示了连接是可以实现的，但是可能不是在所有的场景中都可以实现(例如:“bar”calc(5+8)将被转换为“foo”calc(58 ),因为它不是有效的 calc，因此它们也转换为字符串，但是带有转义引号。变量中的所有内容都被标记化了，所以它可以是字符串，也可以不是，这取决于标记化器所确定的值。如果它不是一个无效的 ident，但不是一个可以匹配的 ident，那么它被转换成一个字符串，以便在 CSS 中使用。传递给 JS 的任何东西都被转换成一个字符串。你可以在[这个 JSBin](https://jsbin.com/dexepigico/edit?html,css,js,console,output) 里看到这个

改变 CSS 自定义属性最简单的方法是使用 calc()将它们乘以一个值:

```
::root {
 --startwidth: 200;
}
h1 {
 width: (var(--startwidth) * 1px);
}
h2 {
 width: (var(--startwidth) * 0.5px);
} 
```

现在，您还可以在 JavaScript 中定义自定义属性，并将它们添加到任何元素的样式集合中，这是一种很好的方式，只需使用 JavaScript 读取值，其余的交给 CSS。例如，如果您想知道文档滚动了多远，您可以在 JavaScript 中用事件处理程序读取并修改 CSS 自定义属性:

```
window.addEventListener('scroll', (e) => {
 document.body.style.setProperty('--scrolly', window.scrollY);
}); 
```

CSS:

```
h1 {
 position: fixed;
 width: calc(var(--scrolly) * 1px); 
 background: #339;
} 
```

你可以[在这个 JSBin](https://jsbin.com/siheyuc/5/edit?html,css,js,output) 里试试这个

这绝不是一个明智的演示，但我真的很喜欢这样一个事实，即您可以使用 JavaScript 到达 CSS 不能到达的地方，并且仍然允许 CSS 成为交互性的主要驱动力和定义。