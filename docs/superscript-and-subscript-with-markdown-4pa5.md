# 标上和标下

> 原文：<https://dev.to/stephencweiss/superscript-and-subscript-with-markdown-4pa5>

Markdown 不直接支持上标或下标。然而，有多种策略可以将上标和下标添加到你的减价商品中。

最简单的，也是我几个月来一直在做的，就是利用角色本身。例如，使用 Mac 上的表情键盘(或 Windows 中的符号键盘)，可以手动插入您想要的字符。这是一个有效的角色，所以 Markdown 会尊重它。

然而，在为 web 编写时，也可以使用降价编译器。编译器的例子包括 Remark 和 Liquid。会将 markdown *编译成* HTML，这意味着文件主体中的有效 HTML 将被考虑。

这很强大，因为它允许混合 Markdown *和*HTML——在我的情况下，这意味着我可以使用`<sup>`标签来<sup>轻松地</sup>在上标上添加单词，而不需要从符号键盘上一次选择一个字符。

当看减价时，这可能看起来没有实际上标/下标漂亮，但它*非常方便。*

再次感谢金成提出这个问题，并鼓励我找到一个更好的解决方案。 <sup>2</sup>

## 脚注

*   <sup>1</sup> [remarkjs/remark:由@ unifiedjs collective | Github 的插件支持的 Markdown 处理器](https://github.com/remarkjs/remark)
*   <sup>2</sup>[https://sung . codes](https://sung.codes)