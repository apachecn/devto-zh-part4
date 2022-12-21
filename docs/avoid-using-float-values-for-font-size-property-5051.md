# 避免对字体大小属性使用浮点值

> 原文：<https://dev.to/mmanista/avoid-using-float-values-for-font-size-property-5051>

假设您收到的设计包括使用浮点值作为**字体大小**(例如 14.5px)。你是做什么的？

**不用**。

不要被它适用于 Chrome 和 Firefox 的事实所迷惑。Safari(默认缩放)无法准确处理这些内容。它将数值向上或向下舍入。

> *> = .5* -上舍入:10.5 px =>11px
> <*5*-下舍入:10.4px = > 10px

这基本上意味着在 Safari 上，10.5px 将会呈现出和你使用*11px*一样的效果。