# 里程碑式的角色(WAI-ARIA 介绍第二部分)

> 原文：<https://dev.to/kayla/landmark-roles-introduction-to-wai-aria-part-2-2kn7>

##### 在这个系列的第一部分，我将谈论角色。今天的文章是关于 ARIA 的标志性角色。

地标角色描述了大面积的内容，使辅助技术(ATs)更容易跳过或在页面区域之间导航。

浏览器相当智能。有时当你使用语义标记时，他们知道你的意思。我将指出这些角色中的哪些角色被隐式地分配给了哪些语义 HTML 元素。当使用这些元素时，通常认为使用相应的 ARIA 角色是多余的。不过，用它们也无妨。

## `role="application"`

`application`角色将页面的一部分声明为应用程序...似乎够明显了。通常，默认情况下，页面被声明为 web 文档。该角色禁用 ATs 的典型导航控件，因为应用程序可能需要这些导航控件中的一些来实现不同的功能。例如，如果应用程序是一个游戏，应用程序区域可能需要上下箭头键来移动游戏中的对象。因此，强烈建议不要使用它。

## `role="banner"`

`banner`角色表示站点范围的信息。这通常是公司名称和徽标的位置。它还可能包含站点的搜索栏。元素有一个隐含的角色`banner`。带有`role="banner"`属性的`<header>`标签或更少的语义标签(如`<div role="banner">`，是**不推荐**)应该是`<body>`元素的直接子元素。

## `role="complementary"`

`complementary`角色表示次要信息。这些信息应该补充页面的主要焦点，但是能够被理解。元素有一个隐含的角色`complementary`。(屏幕阅读器[大多支持`aside`没有明确角色](https://www.scottohara.me/blog/2019/04/05/landmarks-exposed.html#testing-nvda-with-ie11-edge-firefox-and-chrome)。)如果这篇博客文章是它自己的页面，底部的推荐阅读链接将是`<aside>`元素或其他具有`complementary`角色的元素的良好候选。

## `role="contentinfo"`

`contentinfo`角色主要应用于网站的页脚。它包含关于文档的信息，如隐私和版权信息。它最初是用来表示页面特定的信息，但现在似乎已经转移到覆盖页面页脚的所有信息。元素有一个隐含的角色`contentinfo`。值得注意的是，一些屏幕阅读器/浏览器的组合并没有将页脚作为地标。我推荐阅读斯科特·奥哈拉的文章“HTML5 地标暴露”

## `role="form"`

当使用`aria-label`或`aria-labelledby`属性来标记表单时，`<form>`元素有一个隐式角色`form`，如下面的代码示例所示。值得注意的是，一些屏幕阅读器/浏览器组合并不将表单作为地标，即使使用了`aria-label`或`aria-labelledby`。为了解决这个问题，您可以向您的`<form>`元素添加一个`role="form"`属性。再次推荐[斯科特·奥哈拉的文章《HTML5 地标暴露》。](https://www.scottohara.me/blog/2019/04/05/landmarks-exposed.html)

```
<h1 id="heading">Name Collection Form</h1>
<form role="form" aria-labelledby="#heading">
  <label for="name">Name</label>
  <input type="text" id="name" />
  <button type="submit">Submit</button>
</form>
```

## `role="main"`

页面的主要内容。每页应该只有一个主要部分，可以用`<main>`元素来表示。元素有一个隐含的角色`main`。

标明页面的主要部分，并搭配一个跳转链接，可以帮助视力正常的键盘用户更快地浏览页面内容。

```
<a href="#main">Skip to Main Content</a>
<nav>
  <ul>
    <li>Home</li>
    <li>About</li>
  </ul>
</nav>
<main id="main">
  ...Content!...
</main>
```

## `role="navigation"`

`<nav>`元素有一个隐含角色`navigation`。如果每页使用一个以上的导航，用`<nav>`元素上的`aria-label`属性来区分它们是很重要的。这包括任何类型的二级导航、面包屑和分页。

```
<nav aria-label="Site Navigation">
  <ul>
    <li>Home</li>
    <li>About</li>
  </ul>
</nav>
<nav aria-label="Page Navigation">
  <ul>
    <li>First Section</li>
    <li>Second Section</li>
  </ul>
</nav>
```

## `role="region"`

当内容不适合另一个标志性角色时，通常使用`region`角色。元素有一个隐含的角色`region`。尽量少用这个，因为你不会希望你的 AT 用户在毫无意义的`<section>`或`region`的海洋中跋涉。

## `role="search"`

`search`角色表示包含网站或 web 文档搜索的部分。这可以包含在`<header>`元素中。没有特定的搜索元素，所以这个角色需要在围绕搜索的`<form>`元素上。

```
<h1 id="searchHeading">Search</h1>
<form role="search" aria-labelledby="#searchHeading">
  <label for="search">Search</label>
  <input type="search" id="search" />
  <button type="submit">Search</button>
</form>
```

这里有一个 CodePen 的例子，展示了这些里程碑式的角色。

[https://codepen.io/kaylasween/embed/yLBxmMg?height=600&default-tab=result&embed-version=2](https://codepen.io/kaylasween/embed/yLBxmMg?height=600&default-tab=result&embed-version=2)

## 建议阅读关于地标角色的内容

*   [快速提示:ARIA 地标角色和 HTML5 隐式映射](https://a11yproject.com/posts/aria-landmark-roles/)
*   [WAI-ARIA 文档的标志性角色是什么？](https://www.nomensa.com/blog/2010/what-are-wai-aria-document-landmark-roles)
*   [无障碍地标](https://www.scottohara.me/blog/2018/03/03/landmarks.html)
*   [HTML5 地标曝光](https://www.scottohara.me/blog/2019/04/05/landmarks-exposed.html)

##### 下周我们来讲文档结构角色！请随时在评论中留下你对标志性角色的任何疑问！