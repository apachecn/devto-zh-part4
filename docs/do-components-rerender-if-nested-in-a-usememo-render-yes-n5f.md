# 嵌套在 useMemo 渲染中的组件会重新渲染吗？(是)

> 原文：<https://dev.to/kelleyvanevert/do-components-rerender-if-nested-in-a-usememo-render-yes-n5f>

是的，他们(当然)会。

但出于某种原因，我之前没有想到这一点(无论哪种方式)，这让我昨天有点好奇。结论:是的，虚拟 dom 树被记忆了，但是只有*到包含的组件实例引用*，它们将根据自己的条件处理它们的(重新)呈现。另一种思考方式是:对一些虚拟 dom 树结构的记忆并不意味着它被排除在 diffing 算法之外，它仅仅意味着它没有被(完全)重新计算。

这里有一个小涂鸦可以探索:

[https://codesandbox.io/embed/magical-star-xopgm](https://codesandbox.io/embed/magical-star-xopgm)