# 数据-质量保证属性！为 UI 测试自动化选择元素的更好方法

> 原文：<https://dev.to/christinepinto/data-qa-attribute-a-better-way-to-select-elements-for-ui-test-automation-48lm>

我想每个必须自动化 web UI 的人都有这样的情况，为页面上的一些元素找到一个稳定的学习选择器是一件很困难的事情。拥有稳定的选择器是很重要的，这样即使页面结构或样式发生变化，UI 自动化测试也不会失败。

## 为什么不用 ID 或者 CSS 类名？

ID 和 css 类名除了在 UI 自动化测试中使用之外，还有另一个目的。Css 类主要用于样式元素，它们也可能在产品页面上变丑。ID 还有另一个目的，有时也可能只是改变，或者因为干扰页面上的其他功能而无法实现。

## 解答:数据-qa 属性

data-*属性是 HTML 5 中的新特性。这些 HTML 属性使我们能够在每个 HTML 元素上创建自定义属性。因此，解决方案是为 HTML 元素创建“数据-质量保证”属性。这些属性只能用于 UI 自动化测试，不会干扰页面上的任何功能或目的。

### 数据-质量保证属性示例

[!["Code Example"](img/19f0c965344e7043b9b5711ad8f560b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1L7Pxl8C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://miro.medium.com/max/2000/1%2AwOSM8oCwce_trCWGVdywLQ.png)