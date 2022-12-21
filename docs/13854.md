# 高级 CSS:数量查询+使用 Flex 创建流体响应网格+零媒体查询

> 原文：<https://dev.to/boyum/advanced-css-quantity-queries-creating-a-fluid-responsive-grid-with-flex-zero-media-queries-5h6p>

为每一种可能性设计样式是困难的。为一个✨CMS 生成的 site✨做 CSS 工作，在那里你不能控制网站的标记，这教会了你很多关于充分利用我们所拥有的有限工具的事情。

## 问题

我们设计师的最新挑战是这样的:

创建一个矩形网格，使其在桌面屏幕上的表现如下:

[![If there's one item, let it fill the whole row. If there's two, three or four, split them evenly on one row. If the number of elements is five, put three elements on the first row, then two on the second. Six elements: Three on each row. Seven elements: Four on the first row, three on the second, and if there is eight elements, split them evenly four and four](img/cbedaa8fae58d65f37dc74760a2d19ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pgU09v4S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fg8863ryj5xw4uhlzkk6.png)

不使用 JS 来实现这一点，让它在 IE 10 和更高版本上工作，并且不使用媒体查询，因为我们希望这是流畅的，可以在任何屏幕上工作。

乍一看，这似乎是一个需要解决的小问题。一些 flex 属性应该可以解决大部分问题，但是这里有一个问题:如果有四个项目，它们应该都呈现在同一行上，但是如果用户再添加一个，前三个项目应该显示在一行上，然后接下来的两个项目应该呈现在它们自己的行上。没有 JS 怎么可能做到呢？

## CSS 救援数量查询

在我们深入研究解决方案之前，我想教你一些关于 CSS 计数的知识。CSS 提供了一组伪类，结合一些巧妙的数学运算，可以帮助我们跟踪一个容器中有多少元素。Heydon Pickering 的《CSS 数量查询》很好地总结了这一点，但是作为一个 tldr:通过使用`:first-child`、`:last-child`、`:nth-child()`和`:nth-last-child()`的能力，我们可以，比如说，说出一个`<ul>`中有多少个`<li>`。

## 解

```
<ul>
  <li></li>
  <li></li>
  <li></li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

```
:root {
  --margin: 1rem;
}

ul {
  display: flex;
  flex-wrap: wrap;
  list-style: none;
  margin: calc(-1 * var(--margin));
  padding: 0;
}

li {
  flex-basis: calc(33.3333% - (2 * var(--margin)));
  flex-grow: 1;
  flex-shrink: 0;
  margin: var(--margin);
  min-width: 290px;
}

li:nth-last-child(4n):first-child ~ li, 
li:nth-last-child(4n):first-child, 
li:nth-last-child(7n):not(:nth-last-child(14n)):first-child ~ li, 
li:nth-last-child(7n):not(:nth-last-child(14n)):first-child {
  flex-basis: calc(25% - (2 * var(--margin)));    
} 
```

Enter fullscreen mode Exit fullscreen mode

这个方法使用了[海登·皮克林的巧妙信天翁战略(必读)](http://www.heydonworks.com/article/the-flexbox-holy-albatross)。这里唯一需要注意的是`min-width: 290px`。我们需要设置一个最小宽度，这个最小宽度会让比它更窄的屏幕上的东西偏向一边。一定要低调！

`li:nth-last-child(4n)`选择距离最后一个元素四步的倍数的任何元素(从 1 开始计数)。如果`<ul>`中有十个元素，那么从开始的第三和第七个元素将被选择(10，9，8， **(7)** ，6，5，4， **(3)** ，2，1)。`li:nth-last-child(4n):first-child)`然后将目标指向距离最后一个元素和第一个元素四步的倍数的任意元素。添加一个[通用兄弟组合符(~)](https://developer.mozilla.org/en-US/docs/Web/CSS/General_sibling_combinator) ，你可以将第一个元素之后的所有元素作为目标。

注意，每当在`<ul>`中有四个或七个`<li>`元素的倍数时，`flex-basis`就会发生变化。

## 最终结果

经过一点清理，这里是最终的结果！([转到全屏版本](https://codepen.io/sindre/full/vqWZrz))

[https://codepen.io/sindre/embed/vqWZrz?height=600&default-tab=result&embed-version=2](https://codepen.io/sindre/embed/vqWZrz?height=600&default-tab=result&embed-version=2)