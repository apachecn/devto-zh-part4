# MDL 中的嵌套列表和连续段落支持

> 原文：<https://dev.to/mortoray/nested-lists-and-continued-paragraph-support-in-mdl-12c8>

我的解析器继续进展。我最近做了很多重组，增加了一些新的重要功能。继续段落是最需要努力的一个。这是一个解析器特性，在高层次上，它提供了嵌入式列表和扩展的块引用。

# 需要

最近的两篇文章需要这个特性:

*   这个关于面试中软技能的有嵌入列表。支持这些是一个相当大的挑战。我首先尝试了特殊列表支持，然后因为太复杂而放弃了。
*   一篇关于饱和脂肪的文章需要两个段落的评论(一个“旁白”块)。严格来说，它并不需要这个，但是因为它是一个经常出现的结构，所以感觉是时候支持它了。

延续功能支持这两者。

# 延续

由于 MDL 基于 markdown 语法，新段落创建了新的文本块。正如文章所显示的，很少有不希望这样的情况。延续将一个块与前一个块组合在一起。

空白缩进控制着延续。较长的缩进将该块分组到前一个块。这个例子来自饱和脂肪的文章。

```
@Aside
In fairness, the WHO is heavily influenced by politics, lobbying, and cultural norms. It’d be impossible for them to publish that differs too much from current trends. Though I’m concerned by a followup report^whoup in 2016\. It’s a significant meta-study, but appears to have hand-picked studies supporting their cause. It makes little mention of contrary studies, nor low-carb trends, still makes that non-causal leap to heart disease, and fails to mention the differentiation of LDL classes. There is good information in there, but it feels like politics have kept it biased. We can’t ignore the types of diets that WHO is analyzing as well. They are focused on popular diets, which include a lot of animal fats, as well as industrially refined products. Their consumer level recommendations^whohealth for a healthy-diet include nuts, seeds, and oils which contain a lot of saturated fat. Switching from one source to the other probably has a positive health impact — too bad that advice contradicts their own heart-diet research. 
```

Enter fullscreen mode Exit fullscreen mode

第二段的缩进使人联想到前一段。最初的段落有一个`@Aside`注释——更多的是注释，是文本流的一部分。这些在输出时被转换成块引号。我想确保这两段是同一个块引号的一部分，而不是两个单独的块引号。这使得正确设计它们的样式更加容易。

# 嵌套列表

相同的功能用于创建嵌套列表。

```
- Outer A
 - Inner I
 - Further In 1
 - Further In 2
 - Inner 2
 - Outer B
 - Outer C 
```

Enter fullscreen mode Exit fullscreen mode

其呈现为:

*   外部的
    *   内心的我
        *   进一步在 1
        *   进一步在 2
    *   内部 2
*   外部 B
*   外部 C

缩进列表与其上面的列表项组合在一起。这是标准的连续规则，不是列表的特殊规则。因为这不是一个特殊的规则，它允许在列表中使用其他类型的块，而不仅仅是纯文本。

# 其他进展

你可能已经看到[我用类型注释](https://mortoray.com/2019/06/11/a-failed-experiment-with-python-type-annotations/)做了实验。尽管我认为这仍然很粗糙，但我坚持了下来，首先是弄清楚如何升级到 Python 2.8。现在部分注释很有帮助！重构文档树成为可能，我很容易就发现了许多 bug。

文档树的代码和结构变化很大。直到我有足够的所有我想做的文档的例子，我不会稳定任何东西。这也意味着现在没有自动化测试用例——尽管我有几个我经常使用的小样本文档。

我现在正试图用 MDL 来处理我所有的文档。这一篇，还有我发表的最后四篇，都是用 MDL 写的，导出到 HTML 和 Markdown。