# 项目间带有项目符号的可访问内联列表

> 原文：<https://dev.to/sapegin/accessible-inline-list-with-bullets-between-items-2bn8>

很久以来，我在我的网站上就有这样一个列表:

比萨德纳咖啡

直到我在一个屏幕阅读器中尝试过，并意识到屏幕阅读器读取我用来实现项目符号的伪元素(`::before`和`::after`)内容。

屏幕阅读器会读到类似“Pizza MIDDLE DOT dner MIDDLE DOT Kaffee”的内容。不太好。让我们试着修理它。

## 错误解决方案:伪元素中的文本

这是我最初的解决方案，在一个`::before`伪元素中有一个项目符号字符，相邻的兄弟选择器只在元素之间显示它，而不是在每个元素前面:

```
li + li::before {
  content: ' · ';
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/iamsapegin/embed/ZgYprX/?height=600&default-tab=result&embed-version=2](https://codepen.io/iamsapegin/embed/ZgYprX/?height=600&default-tab=result&embed-version=2)

**优点:**简单。

**缺点:**不可及。屏幕阅读器会将每个项目符号读作*中间点*或类似的东西。

## 右解:标记

解决可访问性问题的一个简单方法是将项目符号移到标记中，并用`aria-hidden="true"` :
包装它们

```
<ul>
  <li>Pizza <span aria-hidden="true"> · </span></li>
  <li>Döner <span aria-hidden="true"> · </span></li>
  <li>Kaffee</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

[https://codepen.io/iamsapegin/embed/RXNGqb/?height=600&default-tab=result&embed-version=2](https://codepen.io/iamsapegin/embed/RXNGqb/?height=600&default-tab=result&embed-version=2)

优点:在屏幕阅读器中运行良好。

如果可以避免的话，我不喜欢在标记中保留列表项目符号之类的东西，但是使用文本字符作为项目符号，这可能是唯一的解决方案。

## 右解:图片或 CSS 中的伪元素

这是我最后的解决方案。它是基于最初的，错误的，解决方案与伪元素，但使用图形的项目符号。它可以是简单形状的 CSS，如圆形或箭头，也可以是更复杂形状的 SVG。

我只有一个圆，所以我使用 CSS:

```
ul {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
}
li + li::before {
  content: '';
  display: inline-block;
  vertical-align: middle;
  margin: 0 0.5ch;
  width: 3px;
  height: 3px;
  border-radius: 50%;
  background-color: currentColor;
} 
```

Enter fullscreen mode Exit fullscreen mode

我将`display: flex`添加到了`ul`中，使其独立于 HTML 中可能存在的空白。

[https://codepen.io/iamsapegin/embed/wVBzZo/?height=600&default-tab=result&embed-version=2](https://codepen.io/iamsapegin/embed/wVBzZo/?height=600&default-tab=result&embed-version=2)

优点:在屏幕阅读器中运行良好，标记简洁。

缺点:更多的 CSS。需要用 CSS 或者 SVG 画一个子弹。

## 结论

这两种解决方案在小屏幕上看起来都不太好，因为项目不能放在一行中。我不知道一个完美的方法，所以把它当作一个家庭作业。[如果你找到好东西，请告诉我。](https://twitter.com/iamsapegin)

这个案例很好地提醒了我们，要更加注意我们的网站和应用程序的可访问性。我们至少应该[做基本的可访问性测试](https://daverupert.com/2018/07/assistive-technologies-i-test-with/)，不要总是相信我们的直觉，就像我在这个列表中做的那样，是错误的，并且把不可访问的特性发布到产品中。