# Math.min 返回无穷大？

> 原文：<https://dev.to/dance2die/math-min-returns-infinity-1bi6>

# 前言

我在玩“数学”。 [min](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min) / [max](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max) ”而空呼叫的结果看起来像是倒退了。

[![empty math min max results](img/1421cb8406d0dab35f13d3fa69c30278.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---2B59Ibt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dz85uli1kcrsvrob93tc.png)

# 问题

难道`Math.min`不应该回`-Infinity`而`Math.max`、`Infinity`吗？

# MDN 文档

关于 [Math.max](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max#Description) 的 MDN 文档显示

> 如果没有给定参数，结果是-无穷大。

和 [Math.min](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/min#Description) 文档显示

> 如果没有给定参数，结果是无穷大。

# 但是为什么呢？🤔

经过一番思考，觉得有道理。

假设您正在向`Math.min(3)`传递一个值。最小值应该`3`，因为它是传递给函数的唯一值。`3`应该比 JavaScript 的最低值要低。

除了无穷大本身以外的任何值(`Infinity === Infinity`是`true`)都应该是最小值，由于`3`小于`Infinity`，所以`Math.min`返回 3。

`Math.max`也是如此。如果调用`Math.max(3)`，`3`大于`-Infinity`，则返回`3`。

# 但是...

我不确定我的思考过程是否正确，但至少它有助于理解当没有参数传递给`Math.min/max`函数时会返回什么默认值。

有人能告诉我是否理解返回值背后的原因吗？