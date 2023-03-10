# 在组织 CSS 时保持理智

> 原文：<https://dev.to/leewarrickjr/staying-sane-while-organizing-css-52hc>

[![Mug with 'CSS is Awesome' written on it, but 'Awesome' overflows the border of the text](img/cb0a3db43a141a8a56ea6e2e99a92e38.png "Mug with 'CSS is Awesome' written on it, but 'Awesome' overflows the border of the text")](https://leewarrick.com/blog/static/2fd06797aa9f09bb57ed8458570b82f3/971c4/mugcss.jpg)

在我做前端开发人员的时候，我看到和听到了很多关于 CSS 的抱怨。

我经常看到人们使用诸如 Bootstrap、Materialize、布尔玛、Tailwind 等 CSS 框架。为了避免写 CSS。虽然 CSS 框架可以是很好的加速器，但它们通常需要扎实的 CSS 技能来有效地运用它们。也就是说，如果你不擅长 CSS，你可能也不会擅长使用 CSS 框架。

不管是不是框架，当遇到困难时，人们倾向于用越来越多的 CSS 代码来解决问题，直到找到可行的解决方案。本质上，他们是在遵循“不会打结？扎很多！”开发方法。

不幸的是，一个巨大的、不合适的结是不可靠的，会削弱绳子。

所以不要成为“看什么粘”的开发者。我将向您展示如何在您的样式表中添加一个小的组织，这将使您快速启动并运行，而不会让您心烦意乱。

## 为成功做准备:重置 vs 正常化

HTML 元素有自己的默认样式。不幸的是，大部分都是彻头彻尾的丑陋(看看你，表单输入元素)。

更糟糕的是，浏览器*仍然没有达到 100%的默认风格。这意味着某些元素在不同的浏览器上会有不同的表现。*

您甚至可能会在样式表中一次又一次地覆盖默认样式，以获得您想要的外观或定位。不要落入这个陷阱。一次性处理默认样式。

也就是说，有两个强大的、预先构建的解决方案可以驯服默认样式:重置和正常化。

**注意**:不要忘记在所有其他风格之前加上这些**——记住层叠！**

### CSS 复位

[CSS 重置](https://cssreset.com/scripts/eric-meyer-reset-css/)本质上是核选项。它清除了所有元素中的所有样式，或多或少地使它们只是在名称上有所不同。例如，`<h1>`看起来像`<p>`标签。

如果您需要从头开始，这是一个有效的解决方案，但是您通常不需要完全删除默认样式。如果你这样做了，你将不得不返回并再次用*更多的* CSS 使`<h1>`变大变粗。

### 规格化 CSS

标准化 CSS 是比重置更好的解决方案。它的目标是消除浏览器之间的差异，解决默认样式最常见的问题，同时保持其余部分不变。

大多数 CSS 框架已经内置了一个 normalize，所以如果你正在使用 Bootstrap，Materialize 等，你就不需要添加它。

### 快捷简单的选项

这里还有第三个选项值得一提。如果你想快速完成工作，并且不使用框架，你可以用下面的方法消除大部分恐惧

```
* {
    margin: 0;
    padding: 0;
    /* box-sizing: border-box */
    /* border: 1px solid black */
} 
```

如果您使用元素的默认填充和边距进行滚动，这最终会去掉所有通常必须进行的微调。(`*`是选择所有元素的通配符选择器)

你可以随意添加`box-sizing`位，以确保你的宽度/高度不是累加的。这意味着如果你说一个元素是`500px`，不管你后来添加的填充、边距或边框是什么，它都将是正确的。我发现自己通常不会这样做，但当你做像素完美的布局时，它会有所帮助。

**注**:最后一点🔥**热点提示**🔥用于调试 CSS 布局。如果您想知道一个元素有多大或者是什么形状，添加一个边框可以快速地向您展示一些东西是如何呈现的。这将为所有元素添加一个边框，向您展示所有内容在页面上的布局。

## 写好标记并‘随波逐流’

CSS Zen Garden 向我们展示了 CSS 在设计 HTML 样式方面的强大力量，无论它是如何编写的。

那句关于权力和责任的名言是什么？🤔🕷

HTML 有一定的 T2 流量。它从上到下被读取和渲染，某些元素是`block`或`inline`等。您对这种默认行为了解得越多，您就能更好地利用它，并且您需要编写更少的 CSS 来改变它。

是的，CSS 可以改变标记的默认行为。然而，如果你在编写 HTML 的时候考虑到了你的最终目标，你将会很少使用 CSS 来使你的标记行为恰当。

## 使用 Flexbox 和实用程序类控制布局

学习 CSS 时，元素的布局和定位可能是冲突的最大来源。你希望一个元素在一个特定的位置，所以你写了十行 CSS 代码，几乎可以完美地定位它。然后你再写十行 CSS，但是现在是垂直偏离中心的，等等。

有一些工具和技巧可以极大地减少你将元素放置在你想要的地方所花费的时间。

### 浮动

在说 flexbox 之前，我们先简单说一下浮动。如果你使用浮动布局，*请停止*。Flexbox 与老版本的浏览器高度兼容，而且更容易使用。

浮动对于在元素周围环绕文本非常有用，但是对于布局来说却很糟糕。它们中断了 HTML 的流程，并需要一个 [clearfix](https://www.w3schools.com/howto/howto_css_clearfix.asp) hack 来为后面的其余元素恢复流程。

(**注**:要查看这些实例，请访问[的原始帖子](https://leewarrick.com/blog/staying-sane-with-css/)

这里有一个简单的例子:

```
<div id="float-example">
    <img src="https://picsum.photos/200" id="floatImage"/>
    <!-- Let's say we want this blob of text to wrap around our example image -->
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris ultricies sem id ante ullamcorper, a commodo eros molestie. Phasellus leo justo, malesuada scelerisque ullamcorper et, finibus ut metus. Integer egestas tellus leo, et consectetur tortor bibendum at. Sed non imperdiet risus. Sed at metus mauris. Donec ullamcorper, nunc sit amet ullamcorper mattis, diam sapien viverra nisi, sit amet pretium ligula mi in est. Nulla finibus imperdiet fringilla. Proin fringilla augue et turpis placerat, sit amet commodo enim dictum. Vivamus viverra mollis augue, in fermentum odio placerat eu. Aenean porta tellus quis urna tristique sagittis.
</div> 
```

```
#floatImage {
    /* 'float' makes the text wrap around the image, try 'right' */
    float: left;
    /* the rest of this is to better illustrate the wrapping effect */
    shape-outside: circle();
    border-radius: 100px;
    margin: 5px;
}

#float-example::after {
    /* Another gripe about floats is having to use a clearfix! */
    content: "";
    display: table;
    clear: both;
} 
```

如果您删除一些 lorem 文本，并删除 clearfix hack，您将看到浮动是如何破坏页面流的。

如果你正确使用，浮动是一个很好的工具。但是对于布局，你可能不想使用浮动。

### Flexbox

Flexbox 是一个强大而简单的工具，用于创建布局和定位元素。如果你从未听说过，Wes Bos 有一个很棒的免费系列，叫做 _ [什么是 Flexbox？](%5Bhttps://www.youtube.com/watch?v=Vj7NZ6FiQvo&list=PLu8EoSxDXHP7xj%5D(https://www.youtube.com/watch?v=Vj7NZ6FiQvo&list=PLu8EoSxDXHP7xj)_y6NIAhy0wuCd4uVdid) _。如果你已经熟悉 flexbox，你也可以使用一个很棒的[可视化备忘单](http://flexbox.malven.co/)。

从概念上讲，flexbox 就是创建容器。所以你用`display: flex`创建一个 flex 容器，然后每个直接的子元素将基于父容器元素以某种方式对齐。

这也是一个一维的布局系统。每个容器在一个方向上运行，或者水平运行`flex-direction: row`(默认)，或者垂直运行`flex-direction: column`。然后，里面的孩子可以很容易地从那里居中/对齐。

#### 为什么不是网格？

绝对可以用网格！CSS Grid 非常类似于 flexbox，只是功能更强大。如果说 Flexbox 是一维布局系统，那么 Grid 就是二维的。你几乎可以设置一个 CSS 网格容器来做任何你想做的事情。(如果你想要一个很棒的免费 CSS 网格课程…是的， [Wes Bos](https://www.youtube.com/watch?v=T-slCsOrLcc&list=PLu8EoSxDXHP5CIFvt9-ze3IngcdAc2xKG) 也为你准备了。)

那为什么要用 flexbox 呢？老实说，我喜欢它，因为它非常简单明了。它做一件事，而且做得很好。此外，您完全可以嵌套 flex 容器，并将它们组合成您需要的任何东西。

## 组件 vs 工具类

所以当谈到 CSS 的组织时，有两个阵营:组件和实用类。

### 组件

组件可以被认为是 bootstrap 如何组织它们的构建块。“超大屏幕”或“导航条”等。可能由应用于某个元素的一个类来表示，也可能由内部元素上使用的一些子类来表示。这里的方法是在 HTML 中使用更少的类名，灵活地对它们进行范围/命名，并且通常用少量的类来设计更大块的标记。

给这样的类命名的一个流行惯例是 [BEM](http://getbem.com/) 。BEM 代表“Block，Element，Modifier”，它本质上是类名中的`--`和`__`的系统，以增加 CSS 的可重用性和可组合性。

这里有一个 5 分钟的视频 [BEM，让你快速上手。](https://www.youtube.com/watch?v=SLjHSVwXYq4)

问题是这降低了 CSS 的可重用性。你不能很好地将超大屏幕样式应用到导航条上。BEM 和智能命名只能带你到这里。

### 实用工具类

组件化类的相反方法是实用程序类。这些小类通常只做一件事。例如，`mx-auto`将适用于`margin-left: auto; margin-right: auto`。

Bootstrap 和 Materialize 有一组实用程序类，如果你想看更多的例子，Tailwind CSS 完全由实用程序类组成。

公用事业类的力量正在迅速发展。如果您定义了足够多的实用程序类，那么在进行原型设计时，您几乎不需要离开 HTML 文件。

这里的缺点是，你的 HTML 元素会很快被 CSS 类弄得臃肿不堪，而用来定义一个组件的十个实用类的组合会导致大量重复的代码。

### 寻找平衡

根据我的经验，两种方法的结合是明智的选择。将少量的全局实用程序类与更大的标记块表示类结合起来是最有意义的。这样做还有一个额外的好处，就是让你的标记更具可读性，因为像`card`这样的组件类名会列在像`row`或`column`这样的布局类名旁边。所以`card row`会告诉你这张卡有一行子元素。

让我们看一些例子。让我们构建一个标准布局，从一个标题和一些导航链接开始。

这一点点 CSS 将让我们做很多繁重的工作。我们将从使用 [CSS 变量](https://codeburst.io/css-variables-explained-with-5-examples-84adaffaa5bd)的全局调色板开始，然后定义我们的布局类。

(**注**:要查看这些实例，请前往[原帖](https://leewarrick.com/blog/staying-sane-with-css/) )

```
:root {
    --ex-black: #1C0F13;
    --ex-green-dark: #6E7E85;
    --ex-green-light: #B7CECE;
    --ex-purple-dark: #BBBAC6;
    --ex-purple-light: #E2E2E2
}
/* These are going to be our main, bread and butter layout classes */
.row, .column {
    display: flex;
    flex-wrap: wrap;
}
.column {
    flex-direction: column;
}
.container {
    max-width: 500px;
    margin: auto
}
/* These additional classes will be used to space/align content */
.center-x {
    justify-content: center;
}
.center-y {
    align-items: center
}
.split {
    justify-content: space-between
} 
```

接下来，我们将用 BEM 为我们的导航标题添加一些样式。

```
/* add some styles to prettify */
.header {
    background-color: var(--ex-black);
    padding: 10px 5px;
}
.header__logo {
    color: var(--ex-purple-light);
    margin: 0;
}
.header__link {
    color: var(--ex-purple-dark);
    margin: 0 5px;
} 
```

```
<header class="header">
    <div class="container row split center-y">
        <h1 class="header__logo">Logo</h1>
        <div class="row">
            <p class="header__link">Link</p>
            <p class="header__link">Link</p>
            <p class="header__link">Link</p>
        </div>
    </div>
</header> 
```

那个头球很快就完成了！

让我们再深入一点，制作一个大屏幕/行动号召，并加入一些文字:

```
.jumbotron__container {
    background-color: var(--ex-green-light);
}
.jumbotron {
    height: 300px;
    color: var(--ex-black);
}
.jumbotron__button {
    background-color: var(--ex-purple-light);
    border: 2px solid var(--ex-green-dark);
    border-radius: 4px;
    color: var(--ex-green-dark);
    transition: background-color 200ms, color 200ms
}
.jumbotron__button:hover {
    color: var(--ex-black);
    background-color: var(--ex-purple-dark);
    border: 2px solid var(--ex-black);
}
.ex-heading {
    text-align: center;
    margin: 24px 0;
    color: var(--ex-green-dark);
}
.ex-text {
    color: var(--ex-black);
    max-width: 200px;
} 
```

```
<header class="header">
    <div class="container row split center-y">
        <h1 class="header__logo">Logo</h1>
        <div class="row">
            <p class="header__link">Link</p>
            <p class="header__link">Link</p>
            <p class="header__link">Link</p>
        </div>
    </div>
</header>
<main>
    <div class="jumbotron__container">
        <div class="jumbotron column center-x center-y">
            <h2>This is a moving message</h2>
            <p>And this is why you should click the button below</p>
            <button class="jumbotron__button">Call to Action</button>
        </div>
    </div>
    <h3 class="ex-heading">Read Stuff</h3>
    <section class="container row split">
        <p class="ex-text">
            Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris ultricies sem id ante ullamcorper, a commodo eros molestie. Phasellus leo justo, malesuada scelerisque ullamcorper et, finibus ut metus. Integer egestas tellus leo, et consectetur tortor bibendum at.
        </p>
        <p class="ex-text">
            Sed non imperdiet risus. Sed at metus mauris. Donec ullamcorper, nunc sit amet ullamcorper mattis, diam sapien viverra nisi, sit amet pretium ligula mi in est. Nulla finibus imperdiet fringilla.
        </p>
    </section>
</main> 
```

酷吧？

作为一个 CSS 初学者，做这样的事情需要花费我几个小时和更多的 CSS 代码！

**注意**:如果你在手机上检查这个布局，你会发现它反应灵敏，无需任何额外的工作。如果没有足够的空间，属性会让我们的子元素换行。一般来说，Flexbox 可以很好地创建响应式布局。

## 离别的思念

希望现在你已经看到了在你的 CSS 中应用一些组织原则的力量。只要稍加练习，您就可以只用几行代码完成一些令人惊叹的事情。

有问题吗？评论？下面贴吧！告诉我你是如何组织你的 CSS 的！

*最初发表于[leewarrick.com/blog](https://leewarrick.com/blog/staying-sane-with-css/)T3】*

*喜欢这个帖子？请[订阅我的简讯](https://tinyletter.com/leewarrick)*