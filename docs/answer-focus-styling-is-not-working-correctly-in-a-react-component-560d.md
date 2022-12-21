# 答:焦点样式在 react 组件中不能正常工作？

> 原文：<https://dev.to/zeyadetman/answer-focus-styling-is-not-working-correctly-in-a-react-component-560d>

<header>![](img/540c2ba90e5a347bd57c676bb96dfee0.png) [answer re: Focus styling is not working correctly in a react component?](https://stackoverflow.com/questions/55086679/focus-styling-is-not-working-correctly-in-a-react-component/55087153#55087153) Mar 10 '19[![](img/83f13d376e6608cc602ae93b1cdbae4e.png)4![](img/fd423aaf5fec73c645f97544689ea934.png)](https://stackoverflow.com/questions/55086679/focus-styling-is-not-working-correctly-in-a-react-component/55087153#55087153) </header>

没有`tabindex`和根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/tabindex#Accessibility_concerns) ，你不能使用 css/html 来做这件事:

> 避免将`tabindex`属性与非交互内容结合使用，以使键盘输入的内容具有交互焦点。一个例子是使用一个`<div>`元素来描述一个按钮，而不是…

[Open Full Answer](https://stackoverflow.com/questions/55086679/focus-styling-is-not-working-correctly-in-a-react-component/55087153#55087153)