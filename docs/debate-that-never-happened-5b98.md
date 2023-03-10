# 从未发生的争论

> 原文：<https://dev.to/stereobooster/debate-that-never-happened-5b98>

最近我给[写了一篇文章](https://dev.to/stereobooster/styled-components-one-more-time-5g0l)，收到了一条评论。评论是关于文章中的代码片段，它基本上是说:使用`<button>`而不是`<div role="button" tabindex="0">`，因为这是最佳实践，如果你不遵循最佳实践，代码是不好的。

我试图开始一段对话:我要求解释其中的区别，但之后一切都变糟了。

我对这个帖子感到沮丧，我更喜欢有建设性的对话，所以在这个帖子中，我想补偿错过的机会，并写下关于给定主题的更具建设性的对话可能会是什么样子。

## 代码有问题

```
const Label = styled.div("Label");
Label.defaultProps = { role: "button" }; 
```

## 更健康的辩论

**A** :为什么不用按钮？这是一个糟糕的代码，因为它没有遵循最佳实践。

**B** :我不用这个按钮的真正原因是它是从另一篇文章中复制粘贴过来的。但是代码真的那么糟糕吗？`<button>`和`<div role="button" tabindex="0">`有什么区别？

**A** :语义和行为。

**B** :我们说的是哪种语义？语义就是意义，意义不是自己存在的，它需要一个接受者(受众)。对谁或对什么有意义？我将继续假设，在这种情况下，接收器是所有的应用程序(像屏幕阅读器)。

> 添加 role="button "将使元素在屏幕阅读器中显示为按钮控件。
> - [MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/button_role)

所以我的结论是，从 a11y 的角度来看，它们都有相同的语义。你能更具体地说明行为上的区别吗？

**A** :键盘友好(响应回车、空格和可聚焦)，通过字段集禁用，不同状态(`:active`、`:disabled`、`:focus`)。

**B** :好的。我从[我的另一篇关于如何编写可访问的手风琴](https://dev.to/stereobooster/accessible-react-accordion-component-4p99)的文章中复制粘贴了这段代码，我的代码是键盘友好的(由于`tabIndex=0`元素是可聚焦的)，我不需要通过字段集禁用(我的组件不允许禁用手风琴面板)，我不需要`:active`状态，因为我用上/下箭头(`▲/▼`)来显示它。即使遵循 WAI-ARIA 规范，说代码是坏的是否公平？

是的，这是一种不好的做法，因为有了按钮，你需要的 JS 代码就更少了。

**B** :好，我们来一行一行的对比

| `<button` | `<div` | 元素本身 |
| --- | --- | --- |
| type="button " | - | 阻止表单提交 |
| - | role="button " | 添加语义 |
| - | tabindex="0 " | 使元素可聚焦，公平地说，我需要用 tabindex="-1 "来"撤销"一些按钮的可聚焦状态，所以这是一个平局 |
| {填充:0；背景:无；边框:无} | {display: inline-block} | 重置样式 |
| - | 用于回车和空格的 onKeyDown | 我需要处理 onKeyDown 来支持向上/向下箭头，home，end，所以这只是一个开关的情况 |

这没有更多的代码。

**A** :仍然是糟糕的做法，因为它是不可扩展的，如果你将来需要支持禁用状态，你将需要编写更多的代码，如果有按钮，它将开箱即用。

**B** :我同意在这种情况下使用按钮是最好的做法(甚至在对话开始之前我就同意了，正如你看到的[我贴了一个链接到我写的关于按钮](https://dev.to/stereobooster/comment/bj5j)的文章)。但是这个最佳实践并不能证明称这个代码为“坏的”是正确的，因为你不知道这个代码有哪些需求。

**答**:有没有`<div role="button" tabindex="0">`比`<button>`更好的情况？

**B** :有一些边缘情况

案例 1:

> 与其他浏览器不同，Firefox 在默认情况下会跨页面加载保持动态禁用状态。将 autocomplete 属性的值设置为 off 将禁用该功能。参见 bug [654072](https://bugzilla.mozilla.org/show_bug.cgi?id=654072) 。
> - [MDN 关于按钮](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#Notes)

案例二:

在 Safari 中,`<button type="button">`不是“可点击的”,例如，你不能用标签按钮来访问它们，`tabindex=1`没有帮助(我在 Safari 版本 12.0 (14606.1.36.1.9)中测试过)。但是`<div tabindex=1>`是“可饮用的”。

**结尾**

## 结论

我同意在大多数情况下使用`<button type="button">`作为按钮是好的做法，但是这是一个经验法则。当规则不起作用时，存在边缘情况。我不认为脱离上下文来判断代码是可能的(也许在一些非常简单的情况下)——你需要理解需求及其背后的原因。

## 哪里出了问题？

### 攻击性的开始和防御性的回应

正如你所看到的，对话(来自健康辩论)开始和结束都是同一个问题“为什么按钮？”。为什么不早点得出结论？因为第一条语句包含攻击(“代码不好”)。所以第二个反应是防御性的(“T0 和 T1 有什么区别？”你能证明它是坏的吗？)

**替代示例**

[![vip3rousmango profile image](img/8e35181742b55f2cdf041ef220c9a345.png) ](/vip3rousmango) [ Al Romano ](/vip3rousmango) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/vip3rousmango) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/vip3rousmango) [<time datetime="2019-06-09T14:00:42Z">Jun 9 '19</time>](/vip3rousmango/comment/bj8d)

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN" "[http://www.w3.org/TR/REC-html40/loose.dtd">](http://www.w3.org/TR/REC-html40/loose.dtd%22%3E)

从可访问性的角度来看，你忘记了按钮最重要的一点。当你不用它来提交数据的时候使用`type="button"`！

如果你忘记了这个类型，有些浏览器会默认回到`type="submit"`。通过使用正确的类型，你也不需要`e.PreventDefault`。

如果你想在#a11y 开发中做得更好，我会更关注 HTML 符号，以及当你添加 aria 元素时会发生什么变化，因为它会改变元素的行为以及它们对用户的预期意义。

要了解更多信息，我强烈推荐准备好 Hedyon Pickering 的可访问组件。这是我们商店所有前端开发人员的必读书目，因为我们是一个慈善机构，需要在我们做的每件事情中都建立可访问性。

[![stereobooster profile image](img/85dd06cbd01e5925246fdc462edccfe5.png) ](/stereobooster) [ stereobooster ](/stereobooster) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/stereobooster) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/stereobooster) [<time datetime="2019-06-09T14:05:57Z">Jun 9 '19</time>](/stereobooster/comment/bj8g)

胡麻

> 按钮的类型。可能的值有:
> 
> *   **提交**:按钮向服务器提交表单数据。如果未指定该属性，或者该属性被动态更改为空值或无效值，则这是默认值。
> *   **复位**:该按钮将所有控件复位到初始值。
> *   **按钮**:按钮没有默认行为。它可以有与元素事件相关联的客户端脚本，这些脚本在事件发生时被触发。

[developer.mozilla.org/en-US/docs/W...](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#Notes)

### 没有论证

没有细节，一个人怎么能理解另一个人的观点呢？

**替代示例**

[![stereobooster profile image](img/85dd06cbd01e5925246fdc462edccfe5.png) ](/stereobooster) [ stereobooster ](/stereobooster) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/stereobooster) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/stereobooster) [<time datetime="2019-06-08T22:39:31Z">Jun 8 '19</time>](/stereobooster/comment/bin8)

2)你能证实索赔吗？人类的

[![steveblue profile image](img/324f14b1391f0a0dfc72b3f658529b42.png) ](/steveblue) [ Steve Belovarich ](/steveblue) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/iplayitofflegit) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/steveblue) [<time datetime="2019-06-09T01:34:18Z">Jun 9 '19</time>](/steveblue/comment/bip9)

2)

*   [React 不能与定制元素 v1](https://github.com/facebook/react/issues/7249) 互操作
*   [合成事件不完全反映浏览器事件](https://github.com/facebook/react/issues/14857)
*   [不支持被动事件](https://github.com/facebook/react/issues/14856)
*   [自动对焦的奇怪行为](https://github.com/facebook/react/issues/14125)
*   [没有为 img](https://github.com/facebook/react/issues/14035) 正确镜像交叉原点的处理吗

可能还有更多。简而言之，出现问题是因为 React 实现了不同的事件和属性绑定，导致浏览器 API 的不可预测性。

7)有时，React 社区的重要成员表现得好像网络以 React 开始和结束。我不同意。我也没有在其他 JavaScript 框架社区看到这种态度。社区中的人们如何行动很重要。编码与人和代码一样重要。

如果你使用 React 并且喜欢它，那么继续使用 React。这和你无关。这就是为什么我避免使用流行的框架。我用过 React。当它第一次出现时，我真的很感兴趣为什么他们会以这种方式实现前端框架。我甚至从头开始构建了一个实现虚拟 DOM 的类似框架。然后我意识到 DOM 已经是一棵树了。为什么我需要复制它？我认为复制 DOM 的整个前提是有缺陷的。如果 DOM 不是高性能的，那么不应该是浏览器实现更好的性能吗？我们不需要 JS 框架来继承这个衣钵。几年后，结果证明了这一点，浏览器也优化了一些东西。除此之外，现在有几种方法来声明一个组件，在我看来，定制元素是前进的方向。这并不是说像 React 这样的库仍然没有用。定制元素可能仍然需要某种模式来管理项目中的状态。但是 Redux 要处理的范式比 React IMHO 或其他状态模式要多得多。

我不赞同，但我理解论证。我也学到了一些新东西。在这种情况下，我选择不争论，但我可以...

## 我也错了

我自己也不是圣人，前几天我被触发了:

[![stereobooster profile image](img/85dd06cbd01e5925246fdc462edccfe5.png) ](/stereobooster) [ stereobooster ](/stereobooster) [ ![twitter](img/82aa32a73d0a2eadd783a8a531ea2cc3.png) ](https://twitter.com/stereobooster) [![github](img/029e4450541d5c4819d89a83d5315060.png)](https://github.com/stereobooster) [<time datetime="2019-06-09T08:53:11Z">Jun 9 '19</time>](/stereobooster/comment/bj43)

我的意思是，如果你主要关心的是“客户根本不在乎如何运作”，我想这个建议不适合你。

我自己不是圣人，但我不时会想到那些有特殊需求的人，对他们来说，互联网(最大的信息来源，免费教育，网上银行，社交，媒体等...)不可访问。有些人不能使用互联网，因为有些人“根本不在乎”，而另一些人也不在乎，因为他没有得到报酬。

~~W3C 验证器？真的吗？你是从 1999 年来这里旅行的吗？~~
UPD: W3C 验证程序是可以的，但是不适用于 a11y 测试。可以试试[斧](https://github.com/dequelabs/axe-core)进行 a11y 测试。a11y 测试工具不能保证没有 a11y 问题，它们只是帮助找到一些明显的问题。您仍然需要手动测试它

短语“W3C 验证程序？真的吗？你是从 1999 年来这里旅游的吗？”是不恰当的，我后悔我说了。

我想更有耐心。

> 华福勇在 Unsplash 上的照片