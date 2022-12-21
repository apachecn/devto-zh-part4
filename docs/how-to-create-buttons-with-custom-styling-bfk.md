# 为什么在创建自定义按钮时应该避免使用 div

> 原文：<https://dev.to/rrjoson/how-to-create-buttons-with-custom-styling-bfk>

假设您需要创建一个自定义样式的注册按钮。您可以尝试创建一个 div 元素，并添加您的 CSS 和 onClick 处理程序。不使用 div 元素，一个好的做法是使用 button 元素而不是 div。

这是因为使用按钮元素提供了内置的键盘可访问性。另一个你可能想为你的按钮使用语义 HTML 的原因是它对 SEO 有好处。与 div 相比，搜索引擎更重视按钮内的关键词。