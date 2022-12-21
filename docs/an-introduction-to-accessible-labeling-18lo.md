# 无障碍标签介绍

> 原文：<https://dev.to/lkopacz/an-introduction-to-accessible-labeling-18lo>

*最初发布于[a11ywithlindsey.com](https://www.a11ywithlindsey.com/blog/introduction-accessible-labeling)。如果你想看屏幕阅读器如何解释这些的视频示例，它们就在源代码中！*

上周，我得到了相当多的人，他们认为 aria states 的博客文章很有帮助。本周，我想继续这个话题，回顾一下我花了很长时间才理解的东西:标签。你如何给东西贴标签？什么需要贴标签？所有给事物贴标签的方法有什么区别？

在这篇文章之后，你应该对所有事物标签有了更坚定的理解。

## 表单标签

缺少表单标签是我看到的最常见的易访问性错误之一。大多数人都是这样做的:

```
<form>
  <input type="search" placeholder="Search" />
</form> 
```

我要为后面的人重复一遍:

> 占位符属性**不足以**为屏幕阅读器标记字段。

为了正确标记，我们需要确保有一个`<label>`存在，并且**与输入**相关联。关联它的方法是给`<input>`元素一个`id`，并将该值与标签上的`for`属性相匹配。

```
<form>
  <label for="search">Search</label>
  <input type="search" placeholder="Search" id="search" />
</form> 
```

请注意，`for`属性中的字符串与输入中的`id`匹配。匹配这些字符串就是关联它们的方式！

如果一个设计师给了你一个模仿品，却完全没有办法告诉他们在设计上加一个标签，你很幸运。您可以添加一个`visually-hidden`或`sr-only`(仅屏幕阅读器)类。我总是使用这个来自[a11y 项目](https://a11yproject.com/posts/how-to-hide-content/) :
的 CSS

```
.visually-hidden {
  position: absolute !important;
  height: 1px;
  width: 1px;
  overflow: hidden;
  clip: rect(1px 1px 1px 1px); /* IE6, IE7 */
  clip: rect(1px, 1px, 1px, 1px);
} 
```

上面的代码会变成这样:

```
<form>
  <label class="visually-hidden" for="search">Search</label>
  <input type="search" placeholder="Search" id="search" />
</form> 
```

非常简单，这也适用于`<select>`元素。

### 标签中嵌套输入

我也见过人们嵌套他们的输入。嵌套输入不是我的首选方法，但是如果我这样做:

```
<form>
  <label>
    Search:
    <input type="search" />
  </label>
</form> 
```

这个 HTML 是有效的，使用 Wave 工具不会产生任何错误。然而，这使得隐藏标签变得更加困难，如果你需要这样做的话。所以根据自己的需求来选择方法吧！

## 使用`aria-label`

作为免责声明，这是我最不喜欢的给屏幕阅读器贴标签的方式。我更喜欢用存在于标记中的元素来标记事物。然而，有时候，这是必要的。有几次我发现有必要:

1.  我无法控制标记，必须用 JavaScript 修改 DOM。与创建一个完整的元素并追加它相比，`setAttribute()`要容易得多。
2.  我在一个无法添加`<text>`元素的 SVG 中工作

使用`aria-label`的方法是将字符串放入属性中。例如，我在 MDN 网站上发现了这个:

```

  <!-- all the children -->
 
```

aria-label 属性值不需要匹配另一个元素的`id`。

## `aria-describedby`和`aria-labelledby`

`aria-describedby`和`aria-labelledby`需要一段时间来解释，因为这两个属性听起来是一样的。所以让我们从字面上来理解它们。

标签就像标题。他们告诉你什么是东西。我们什么时候宣布一个标题或标签:在元素之前。

让我们看一下来自 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-labelledby_attribute)的第三个例子。

```
<div id="radio_label">My radio label</div>
<ul role="radiogroup" aria-labelledby="radio_label">
  <li role="radio">Item #1</li>
  <li role="radio">Item #2</li>
  <li role="radio">Item #3</li>
</ul> 
```

当我们使用 VoiceOver 导航到广播组时，它会显示“我的广播标签，广播组？”电台标签在之前**宣布，它告诉我们关于电台组的事情。**

让我们用上面的相同标记添加一个带有 div 的`aria-describedby`属性。

```
<div id="radio_label">My radio label</div>
<ul
  role="radiogroup"
  aria-labelledby="radio_label"
  aria-describedby="radio_desc"
>
  <li role="radio">Item #1</li>
  <li role="radio">Item #2</li>
  <li role="radio">Item #3</li>
</ul>
<div id="radio_desc">
  A bit more about this radio group. Here are some words.
</div> 
```

正文“关于这个广播组的更多信息。下面是一些话”发生在画外音宣布完广播组之后。这是一个非常微妙的区别，在大多数情况下，这并不重要。

我们放入这些属性的字符串就像上面的表单标签。我们希望`aria-describedby`或`aria-labelledby`属性匹配我们希望屏幕阅读器读取的元素的`id`。

## 结论

我希望我帮助你理解了更多关于易访问性标签的混乱世界！让我知道你的想法，或者如果你有任何其他问题，请在 Twitter 上留言。

还有，我有一个 [patreon](https://www.patreon.com/a11ywithlindsey) ！如果你喜欢我的工作，可以考虑每月认捐 1 美元。如果你认捐了 5 美元或更多，你将能够对未来的博客文章进行投票！干杯！祝你一周愉快！