# Optional HTML

> 原文：<https://dev.to/stolleydotdev/optional-html-1jam>

Jens Meiert 是省略可选 HTML 的忠实粉丝。他收集了一份非常详尽的指南，涵盖了哪些内容可以省略以及何时省略。从那里，他还链接到了他运行测试的其他帖子，并开发了说明省略可选 HTML(从可选的结束标记到单词属性周围的引号)的性能增益的指标。

我的问题是，可选规则伴随着大量的警告和上下文问题。作为一名开发人员，我想要一个可读的、一致的代码库。引用你的属性，例如，总是。我仍然用 XHTML-strict 风格编写 HTML。我不希望有些属性的模糊性包含在引号中，而另一些属性则不然。全部引用。

此外，总有可能因为内容的变化而被迫改变上下文，从而改变规则。举个愚蠢的例子，

```
<aside class=summary>A summary</aside> 
```

Enter fullscreen mode Exit fullscreen mode

那合法。但是第二个额外的类被添加，它不是:

```
<!--Invalid-->
<aside class=summary review>A summary and review</aside> 
```

Enter fullscreen mode Exit fullscreen mode

通过插入引号并从一开始就减少歧义，随后的调整潜在地更不容易出错，简单地通过减少改变内容的认知负荷，同时保持操作规则:

```
<aside class="summary">A summary</aside>
<!-- becomes: -->
<aside class="summary review">A summary and review</aside> 
```

Enter fullscreen mode Exit fullscreen mode

尽管正如 Jens 自己在他的指南附录中指出的，验证是必要的。

一个可能的折衷方案是:用 HTML 或任何其他语言编写冗长、严格的样式。然后将它留给一个工具来移除可选的东西。即使这样，验证也是必要的。