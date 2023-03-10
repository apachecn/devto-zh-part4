# 强势 vs 新兴市场

> 原文：<https://dev.to/chinchang/strong-vs-em-5cb2>

`<strong>`和`<em>`是我们在开始学习 HTML 时学习的两个最基本的标签。我相信你一定多次使用过这两个标签。我也是。但是在我内心深处，我总是有点困惑，不知道应该使用哪种情况。事实上，在哪些地方不应该使用标签。因此，我最终决定查找规格，以彻底消除这一疑虑。我必须承认，规范在解释每一个的目的方面做得很好。

如果你也面临使用`<strong>`和`<em>`标签的困惑，那么你就在正确的地方。这篇文章试图重新表达我通过阅读 <cite>W3C 规范</cite>所获得的知识。

## TL；速度三角形定位法(dead reckoning)

如果只是视觉重要性，你要`strong`。如果它改变了句子的意思，使用`em`。

## `<strong>`标签

首先，引用 <cite>[HTML 规范](https://html.spec.whatwg.org/multipage/text-level-semantics.html#the-strong-element)</cite>

> 强元素表示其内容的高度重要性、严肃性或紧迫性。

让我们试着理解这 3 个用例。

### 重要性

重要性可以想象成“等级”。例如，假设你在不同的段落中描述某些项目。你可能会通过将项目名称放在`strong`标签中来开始段落，就像这样:

```
<p>
  <strong>Item X</strong>: This paragraph is all about describing Item X, it's
  properties and behaviour.
</p>

<p>
  <strong>Item Y</strong>: This paragraph is all about describing Item Y, it's
  properties and behaviour.
</p> 
```

从视觉上看，它们的行为类似于标题。我将重要性与层次进行比较，因为如果有人需要快速浏览段落中描述的所有项目，他们只需浏览同样以粗体显示的`strong`文本(因此看起来层次更高)。

### 严肃

一个典型的例子是当你需要显示一个警告或者错误的时候。你可以简单地显示一个描述整个警告的句子，但是有可能用户忽略了阅读整个文本，从而忽略了警告。为了更清楚地表明特定文本传达的是警告，我们可以将它放在`strong`标签中。同样，`strong`标签的粗体外观帮助我们传达这不仅仅是另一个文本。这是很严重的事情，需要看一看。

简单的例子:

```
<strong>Warning: You data will lost in 35 seconds</strong> 
```

其呈现为:

**警告:您的数据将在 35 秒内丢失**

### 尿急

这个用例是我经常遇到的，尤其是在写邮件的时候。假设你正在写一个很长的段落，有些东西你想让读者首先阅读。那些东西可以用`strong`标签包装。

同样，整个前提是，阅读你的长文本的人没有时间阅读所有文本，即使他们有时间，也可能有某些重要的文本片段需要首先阅读，并采取紧急行动。标签帮助我们实现了这一点。

我知道所有 3 个使用案例看起来都很相似，并且相互重叠。这是真的。但是总的来说，它们确实有助于构建一个好的意图集，在这个意图集中你会用到`strong`标签。

## `<em>`标签

标签读作`emphasis`,但当然不仅仅如此。事实上，正是这个单词*强调*让我们很多人误以为它类似于`strong`标签，后者也用来强调某事的重要性。事实并非如此。

再次引用 <cite>[HTML 规范](https://html.spec.whatwg.org/multipage/text-level-semantics.html#the-em-element)</cite> :

> em 元素表示其内容的重音。

这里需要注意的重要一点是强调。它实际上与你如何说一段特定的文字有关。

我来用 gif 解释一下`em` tag 的要领:

[![A person making air quotes while text shows on then screen: Get the "Laundry" (with quotes around the word - "laundry")](img/808b12d5dcc6ac0fe76f7d7dcbc412f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_pkuKGh3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://media.giphy.com/media/31P5RGyIVBhMMfw0F7/200w.webp)

[通过 GIPHY](https://giphy.com/)

我相信你见过人们在说一个长句中的特定单词或短语时做这个引用的手势。想象他们在说那个单词/短语时没有做那个手势。你会从那个演讲者那里得到同样的意思吗？不。因为说话者在句子的特定部分所施加的特定重音，改变了他们原本的意思。

这正是`em`标签在写作时帮助你做的事情。您可以将句子的某些部分换行，以模拟说话时的重音。

为了更清楚，我想给你看来自 <cite>HTML 规范</cite>的确切例子，因为我认为它完美地解释了`em`标签的作用。

> 这些例子显示了改变重音是如何改变意思的。首先，毫无压力地概述事实:
> 
> `<p>Cats are cute animals.</p>`
> 
> 通过强调第一个词，该陈述暗示所讨论的动物种类是有问题的(也许有人在断言狗很可爱):
> 
> `<p><em>Cats</em> are cute animals.</p>`
> 
> 把重音移到动词上，可以突出整个句子的真实性是有问题的(也许有人在说猫不可爱):
> 
> `<p>Cats <em>are</em> cute animals.</p>`
> 
> 通过将它移到形容词，猫的确切性质被重申(也许有人认为猫是卑鄙的动物):
> 
> `<p>Cats are <em>cute</em> animals.</p>`
> 
> 同样，如果有人断言猫是蔬菜，有人纠正这一点可能会强调最后一个词:
> 
> `<p>Cats are cute <em>animals</em>.</p>`
> 
> 通过强调整个句子，可以清楚地看出说话者正在努力表达观点。这种强调通常也会影响标点符号，因此这里有感叹号。
> 
> `<p><em>Cats are cute animals!</em></p>`
> 
> 愤怒加上强调可爱可能会导致这样的标记:
> 
> 猫是可爱的动物！

希望这些例子能让你明白。

## 屏幕阅读器辅助功能

`<strong>`和`<em>`不仅仅是为了在视觉上传达语义，它们还应该向屏幕阅读器用户传达相同的意思，最好是通过改变声音。

根据我的测试，VoiceOver 似乎无法区分普通文本和强调文本。此外，最近网络上似乎没有关于其他屏幕阅读器如《大白鲨》和《NVDA》的研究。因此，我不会详细讨论这个话题。如果有人能分享关于这个问题的研究，我将不胜感激，我可以在这里更新。

然而，屏幕阅读器在不断改进，我们对标签的正确使用最终只会帮助用户。

此外，值得注意的是，可访问性并不总是与屏幕阅读器有关。带有`strong` / `em`标签的视觉差异对于提高阅读理解和帮助像阅读障碍这样的认知障碍是非常重要的。

## 总结

下一次当你弄不清在`strong`和`em`中使用哪个标签时，想想这个:你改变某个单词或短语的视觉外观的意图是什么？你的重音通过改变句子的意思真的有助于更好地交流吗(就像你说话时会强调的那样)？

如果只是视觉重要性，你要`strong`。如果它改变了句子的意思，使用`em`。

## [T3`b`和`i`标签呢？](#what-about-raw-b-endraw-and-raw-i-endraw-tags)

在`strong` / `b`和`em` / `i`之间进行选择时还有一个常见的困惑。从视觉上看，它们和它们的对应物完全一样。那有什么区别？

区别在于语义。`strong`和`em`在使用时会带来一些额外的上下文或含义。另一方面，`b`和`i`只是为了在样式上和视觉上区分句子的某个部分。你可以把`b`标签看作是一个应用了 CSS 的`span`。类似地，`i`可以被认为是一个`span`，上面应用了`font-style: italics`的 CSS。

所以如果你想表达重要性、紧迫性或严肃性，就用`strong`。如果你只是需要吸引注意力而没有任何重要性，使用`b`或者干脆使用 CSS `font-weight`。类似地，如果你想给某物施加压力，使用`em`。如果只是为了突出显示不同类型的文本，比如另一种语言的单词，那么就使用`<i>`或者只使用 CSS `font-style`。

[阅读更多关于`b`标签](https://html.spec.whatwg.org/multipage/text-level-semantics.html#the-b-element)

[阅读更多关于`i`标签](https://html.spec.whatwg.org/multipage/text-level-semantics.html#the-i-element)

这就是这篇文章的全部内容。希望这有助于更好地理解这些常见的标签。

* * *

特别感谢 [Jitendra Vyas](https://twitter.com/jitendravyas) 、[、@stommepoes](https://twitter.com/stommepoes) 以及[Slack Web Accessibility group](https://web-a11y.slack.com/)可爱的成员们审阅这篇文章。