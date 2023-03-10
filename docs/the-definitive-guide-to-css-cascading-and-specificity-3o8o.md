# CSS 级联和特异性的权威指南

> 原文：<https://dev.to/billmei/the-definitive-guide-to-css-cascading-and-specificity-3o8o>

<small>本文节选自 [*无痛 CSS*](https://www.painlesscss.com/guide-to-css-specificity.html) 。</small>

级联是级联样式表的一个重要部分，但是它经常被解释得很糟糕，在线教程会导致更多的混乱而不是清晰。我认为这很遗憾，因为我发现了一个更有效的心智模型来解释 CSS 层叠。与其考虑“层叠”或“特异性”，不如把它想象成一场“CSS 锦标赛”。

举行比赛是为了决定冲突的赢家。CSS 锦标赛的目的是解决两个或更多 CSS 属性之间的冲突，这些属性试图将相同的样式应用到相同的 HTML 元素上。

让我们看一个例子:

[![](img/f5c73da9977222aa1cd6ac14a0c8da36.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2RYzAobz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/tournament-before.png)T3】

```
p.maroon#malamute button[disabled] {
  margin: 100px;
  cursor: copy;
}
.sapphire-shepherd p.headline.content button[disabled] {
  padding: 2rem;
  cursor: wait;
}
#purple-poodle button {
  cursor: pointer;
  text-decoration: underline;
}
#teal-terrier.fullpage button.submit-btn {
  line-height: 2.5;
  cursor: help;
} 
```

```
<body class="fullpage" id="teal-terrier">
  <div id="purple-poodle">
    <article class="sapphire-shepherd">
      <p class="maroon headline content" id="malamute">
        <button class="submit-btn" disabled>
          What happens when you hover over me?
        </button>
      </p>
    </article>
  </div>
</body> 
```

<small>这些代码样本来自[无痛 CSS](https://www.painlesscss.com) ，这是一个从基本原理开始教你 CSS 的书籍和视频课程。你可以在[课程](https://www.painlesscss.com)中下载包含所有 60 个代码样本和解决方案的完整报告。</small>

这次锦标赛有四名选手。每位参赛者提交三份参赛作品:

*   条目 1 包含 CSS 规则中 ID 选择器的数量
*   条目 2 由 CSS 规则中的类、属性和伪类选择器的数量组成
*   条目 3 包含 CSS 规则中标签和伪元素选择器的数量

[![](img/a631d447a4b593976233e59ac82ee585.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--znYl03zS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Introduction.png)

每个框中的数字代表分配给该条目的选择器的数量。

我们的竞争对手正在争夺`cursor` CSS 属性，以下是冲突声明:

*   `cursor: copy;`
*   `cursor: wait;`
*   `cursor: pointer;`
*   `cursor: help;`

赢得 *CSS 锦标赛*会有一份诱人的奖品:获胜者的`cursor`值将应用于选中的`button`。

让我们来认识一下竞争对手:

### 竞争对手 1:栗色雪橇犬

```
p.maroon#malamute button[disabled] {
  margin: 100px;
  cursor: copy;
} 
```

这个 CSS 规则有一个 ID 选择器(`#malamute`)、一个类选择器(`.maroon`)、一个属性选择器(`[disabled]`)和两个标签选择器(`p`、`button`)。

[![](img/adfddb1fccbdd32f06f2ee61a79ea110.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bDSWg4aJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Maroon-Malamute.png)

我们可以用一个数字来概括这个竞争对手的参赛作品:`122`。这个数字也被称为选择器的*特异性水平*(或*特异性得分*)。这个分数越高，特异性越高。

让我们认识一下其他竞争者:

### 竞争对手 2:蓝宝石牧羊人

```
.sapphire-shepherd p.headline.content button[disabled] {
  padding: 2rem;
  cursor: wait;
} 
```

[![](img/48e8c091f58cf22a85aba7695d007bd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lx8W5lfC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Sapphire-Shepherd.png)

该选择器的特异性得分为`041`。

### 竞争对手 3:紫色狮子狗

```
#purple-poodle button {
  cursor: pointer;
  text-decoration: underline;
} 
```

[![](img/e4922501e6f5858d89e85a01b886fc8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KAgu1lQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Purple-Poodle.png)

该选择器的特异性得分为`101`。

### 竞争对手 4:缇尔梗

```
#teal-terrier.fullpage button.submit-btn {
  line-height: 2.5;
  cursor: help;
} 
```

[![](img/3e4fa5db0f6790d466b6de0dfd4537e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sghpw-Au--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Teal-Terrier.png)

该选择器的特异性得分为`121`。

## 比武规则

虽然`cursor`存在冲突，但下面的声明并不相互竞争。它们不参加锦标赛，它们都作为样式立即应用到`button`上，无需参加锦标赛。

*   `margin: 100px;`
*   `padding: 2rem;`
*   `text-decoration: underline;`
*   `line-height: 2.5;`

锦标赛有三轮。在第一轮比赛中，所有参赛选手提交他们在第一轮比赛中的得分。得分最高的竞争对手赢得该轮比赛。类似地，参赛者提交第二轮的参赛作品 2 和第三轮的参赛作品 3。

### 回合 1

对于第一轮，我们来看看所有竞争者的 ID 选择器的数量。ID 选择器数量最多的竞争对手赢得这一轮比赛:

[![](img/baa8f064c5f350ebe54481f1e1ceeb1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TWQ7Eja---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Round-1-Before.png)

蓝宝石牧羊人没有 ID 选择器，所以它立即从第一轮被淘汰！

[![](img/ed7f3802d8001bc23782f93eb5bb0858.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q-S6wIYc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Round-1-After.png)

一旦竞争者被淘汰，它就不能参加任何后续的比赛。

栗色雪橇犬、紫色狮子狗和蓝绿色梗各有 1 个 ID 选择器，所以它们都进入第二轮。

### 第二回合

对于第二轮，查看所有剩余竞争者的类、属性和伪类选择器的数量。获得最多分数的竞争对手赢得本轮比赛:

[![](img/287d4b259b70201b56f7983e9e85805d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pSTcb-Es--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Round-2-Before.png)

即使蓝宝石牧羊人在这一轮中拥有最多的职业选择者，但它在上一轮中已经被淘汰了，所以它没有获胜！如果竞争对手留在比赛中，它是否“可能赢”并不重要——一旦竞争对手被淘汰，它就永远消失了。

栗色雪橇犬和蓝绿色梗在第二轮比赛中并列第一，因此它们进入第三轮比赛，紫色狮子狗被淘汰:

[![](img/c716cb12c4e2beff453dbaa3a9256cd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w5C5YLZ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Round-2-After.png)

### 第三回合

对于第 3 轮，查看所有剩余竞争对手的标签和伪元素选择器的数量。拥有最多分数的竞争者赢得锦标赛！

[![](img/1d87cf448b29e8ef097c5b982199e48c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--id7r_mOv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Round-3-Before.png)

栗色雪橇犬有`2`标签选择器，蓝绿色梗有`1`标签选择器，所以**栗色雪橇犬**赢得比赛！

[![](img/e161ee9214bc1bd319338349c3b81989.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vGu1DVqK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/Round-3-After.png)

最后的结果是栗色的马拉穆特 CSS 规则“赢”了其他规则:

```
p.maroon#malamute button[disabled] {
  margin: 100px;
  cursor: copy
} 
```

并且声明`cursor: copy`被应用于所选择的 HTML 元素。

[![](img/f17d5ae046c3404e4116026afb715dee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gJHMikI---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/tournament-after.png)

在这个例子中，`cursor`是冲突中唯一的 CSS 属性。如果有不止一个属性发生冲突(比如，两个或多个 CSS 规则都有不同的`margin`值)，那么我们将为下一个属性(`margin`)再次运行这个锦标赛，从头开始。

## 快捷方式:特异性评分

既然你已经理解了锦标赛的类比，让我们简化整个事情，使它在实践中更容易使用。

首先，不要按照 ID、类和标签选择器来分解条目，让我们按照如下方式给每个 CSS 规则分配分数:

*   每个 ID 选择器值 100 分
*   每个类、属性或伪类选择器值 10 分
*   每个标签或伪元素选择器值 1 分

然后，我们将把每位参赛者的分数加起来:

#### 竞争对手 1:栗色雪橇犬

`p.maroon#malamute button[disabled] {}`

`(1 × 100) + (2 × 10) + (2 × 1) = 122`

栗色雪橇犬有`122`分。

#### 竞争对手 2:蓝宝石牧羊人

`.sapphire-shepherd p.headline.content button[disabled] {}`

`(0 × 100) + (4 × 10) + (1 × 1) = 41`

蓝宝石牧羊人有`041`点。

#### 竞争对手 3:紫色狮子狗

`#purple-poodle button {}`

`(1 × 100) + (0 × 10) + (1 × 1) = 101`

紫色狮子狗有`101`分。

#### 竞争对手 4:缇尔梗

`#teal-terrier.fullpage button.submit-btn {}`

`(1 × 100) + (2 × 10) + (1 × 1) = 121`

缇尔梗有`121`点。

### 特异性等级

接下来，我们不再一轮一轮地看锦标赛，而是将每位参赛者的所有特异性分数从最高到最低排序:

1.  `122`点数:栗色雪橇犬
2.  `121`分:缇尔梗
3.  `101`点数:紫色狮子狗
4.  `041`点数:蓝宝石牧羊人

谁是赢家？简单！锦标赛的冠军是得分最高的选手:马龙·马拉穆特。另一个简单的事实是，从下到上阅读这个列表是竞争对手被淘汰的顺序！所以蓝宝石牧羊人第一个被淘汰，然后紫色狮子狗第二个被淘汰，然后蒂尔梗第三个被淘汰。

“等一下，”你可能会问，“当这种排名方法如此容易时，你为什么要费这么大劲解释整个锦标赛的类比？”这是因为当你有超过 9 个类别(或者标签，或者 id，等等)时，这种分类特异性分数的捷径不起作用。)在一个元素上。这里有一个例子:

```
.big.big.big.big.big.big.big.big.big.big.big.big.big {
  font-size: xx-large;
}
.medium.medium {
  font-size: medium;
}
#x-small {
  font-size: x-small
} 
```

<small>这些代码样本来自[无痛 CSS](https://www.painlesscss.com) ，这是一个从基本原理开始教你 CSS 的书籍和视频课程。你可以在[课程](https://www.painlesscss.com)中下载包含所有 60 个代码样本和解决方案的完整报告。</small>

```
<p class="medium big" id="x-small">
  What size am I?
</p> 
```

以下是“分数”:

*   `.big.big.big.big.big.big.big.big.big.big.big.big.big {}`的分数为`(13 × 10) = 130`
*   `.medium.medium {}`的分数为`(2 × 10) = 20`
*   `#x-small {}`的分数为`(1 × 100) = 100`

您可能认为元素的大小是`xx-large`，但实际上是`x-small`！

[![](img/af4fac350ca17615482d586d94268056.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L2xz3U1C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-087.png)

在最初的锦标赛中，各轮(和参赛)是相互独立的。快捷排名方法不适用于 9 个以上的类，因为当您将第 10 个类添加到一个已经有 90 分的选择器中时，它的分数就变成了 100，并且它“压倒”了 ID 选择器，这是不应该发生的。出现这个问题是因为我们使用的是以 10 为基数的计数系统，你可以通过改变基数来避免这个问题，但在这一点上，使用锦标赛类比作为你的心理模型要简单得多。

幸运的是，在实践中，我们通常不会在一个 CSS 规则中编写超过 9 个选择器，所以我们将所有特异性分数相加的捷径对于您在现实世界中遇到的大多数情况都很方便。

## 比武的例外

通常锦标赛的获胜者是被渲染的 CSS 声明；但是也有一些例外情况，即不是获胜者的声明会被呈现出来:

### 内嵌样式

HTML 元素上的任何内联样式(使用`style`属性直接在 HTML 元素上编写 CSS，而不是使用单独的样式表)都会自动覆盖锦标赛的任何获胜者。比如:

```
#bold {
  font-weight: bold;
} 
```

```
<p style="font-weight: normal;" id="bold">
  This text is not bold.
</p> 
```

[![](img/b681bb994ae4ee7782e7fd045d25e877.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SAk4uzdR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-088.png)

除了我在*无痛 CSS* 一书的[第 6 章](https://www.painlesscss.com)中描述的原因，这是我们试图避免内联样式的另一个原因:它们不能被我们在样式表中编写的任何 CSS 规则覆盖。

与直觉相反，在某些情况下，当你为 React、Vue 或 Angular 等前端框架编码时，使用内联样式可能是最佳实践。我稍后会在《T2》中讨论这个问题，而不是级联。现在，我们将避免使用内联样式。

这是这个例外的例外！虽然内联样式会覆盖锦标赛获胜者，但是任何使用`!important`关键字的声明都会覆盖内联样式。

### [T1】！重要的](#important)

`!important`是 CSS 中的一个特殊关键字，让你的声明*总是*赢得锦标赛，无论如何，*即使元素上有内联样式*！看起来是这样的:

```
p {
  font-weight: bold !important;
}

#not-bold {
  font-weight: normal;
} 
```

<small>这些代码样本来自[无痛 CSS](https://www.painlesscss.com) ，这是一个从基本原理开始教你 CSS 的书籍和视频课程。你可以在[课程](https://www.painlesscss.com)中下载包含所有 60 个代码样本和解决方案的完整报告。</small>

```
<p style="font-weight: normal;" id="not-bold">
  This text IS bold.
</p> 
```

[![](img/db33c1568e24ff93a9381f36dfc93059.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M0QK00VF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-089.png)

我们避免在 CSS 中使用`!important`,因为它不能被覆盖。<sup>[【1】](https://www.painlesscss.com/guide-to-css-specificity.html#footnotes)</sup>如果你后来改变了主意，用一种不同的规则是无法制服它的；你必须删除`!important`,然后还要检查该元素的每个 *other* 规则，以确保没有其他规则被破坏，因为通常最初添加`!important`的原因是为了破解一些有问题的代码。

CSS 作者真正的噩梦是打开一个 CSS 文件，看到`!important`撒得到处都是！这种情况下的选择者被称为*超规格*或*超规格*。

这也是为什么我建议在 CSS 样式中避免使用 id，因为 id 也非常强大。无论你写多少 CSS 类，你都不能覆盖一个 ID。

锦标赛的类比给了我们一个直观的感觉，为什么强大的选择者是不好的。如果一个选择器在锦标赛中非常强大，它会在更早的回合中赢得更快，这意味着击败强大的选择器的唯一方法是编写另一种更加强大的风格。

这种特异性不断升级的趋势被称为*特异性战争*。就像储备核武器一样，这场战争没有谁会赢——随着特殊性的增加，降级只会变得更加困难。避免全面的特异性战争的唯一方法是首先不要储备高性能的选择器。【2】

### 并列直到最后

如果我们进入了锦标赛的最后一轮，仍然是平局怎么办？

如果比赛结束了，仍然有平局，那么最后的平局决胜就是 CSS 规则在 CSS 源文件中的位置。晚写的东西(靠近文件底部)将胜过早写的东西(靠近文件顶部)。

如果你将多个 CSS 文件导入到你的 HTML 文档中，(例如，你有多个`<style>`或`<link>`标签)，那么最后一个文件(靠近 HTML 文档的底部)会胜出。因此，锦标赛永远不会一直平局，因为最后的平局决胜是代码的物理位置，并且这总是唯一的。

这也是解决重复声明的方法；如果你写:

```
span {
  color: red;
} 
```

但是后来在同一个文件中你写了

```
span {
  color: blue;
} 
```

然后`color: blue`将被应用到你的`span`中。

因此，导入任何第三方 CSS 文件的顺序很重要，(例如，使用`<link>`标签，或`@import`)，因为改变顺序会改变最后一次加试的结果。实际上，这意味着我们导入的第一件事是标准化用户代理样式表的`normalize.css`或`reset.css`文件，然后是任何第三方 CSS 框架，如 Foundation 或 Bootstrap，最后是我们自己的特定于站点的代码。

平局决胜规则就是为什么我推荐用*代替*标签选择器，并鼓励你使用类选择器，因为标签选择器是*未指定的*。标签选择器的能力最低，因此它们对它们在 CSS 文件中的顺序过于敏感，如果您对导入进行重新排序，您的设计可能会中断，因为在重新排序时，平局决胜结果会发生变化。

### 继承

继承的属性总是被赋予一个特异性分数`000`，不管它们最初的特异性是什么。

```
#powerful {
  font-weight: bold !important;
}
.unpowerful {
  font-weight: normal;
} 
```

```
<div id="powerful" style="font-weight: bold;">
  <div class="unpowerful">CSS rules everything around me</div>
</div> 
```

因为`font-weight`是子对象的继承属性，所以父对象既是一个高性能的 ID 选择器又有一个`!important`并不重要，在这种情况下，`.unpowerful`选择器将会胜出。

[![](img/630085bc41e3b2ea2a7675628426cfa8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---74jD4QV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-090.png)

然而，如果我们改变我们的 HTML，使 ID `#powerful`变成非继承的，

```
<div>
  <div class="unpowerful" id="powerful">
    CSS rules everything around me
  </div>
</div> 
```

那么本我就如预期的那样赢了。

[![](img/3fd0c30311915867c538fd1203f3693e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AC_zOtS_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-091.png)

“世代距离”(属性是继承自父代还是祖父代)并不重要——只有直接选择元素的 CSS 选择器具有大于`000`的特异性。只有当有*没有* CSS 规则直接选择你的 HTML 元素时，父母才会拉拢爷爷奶奶:

```
<div id="powerful">
  <div class="unpowerful">
    <p>CSS rules everything around me</p>
  </div>
</div> 
```

[![](img/630085bc41e3b2ea2a7675628426cfa8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---74jD4QV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-090.png)

### 万能选择器

通用选择器`*`的特异性为`000`。

### 非参与者

请记住，锦标赛仅适用于在一个*选择的*元素上相互冲突的属性——如果两个选择器没有选择同一个元素，那么它们没有冲突。比如按钮背景会是什么颜色？

```
button {
  /* Specificity: 001 */
  background-color: orange;
}

button .submit-btn {
  /* Specificity: 011 */
  background-color: green;
} 
```

```
<button class="submit-btn">What color am I?</button> 
```

[![](img/37b4656ba6fcf668ce9d42c887162944.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bNvLcFHp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-093.png)

答案是会是`orange`！这是因为选择器`button .submit‑btn`没有选择我们的元素(它会选择带有`.submit‑btn`类的按钮的任何*子元素*),尽管它有更高的能力，它甚至不参与比赛！如果第二个选择器是`button.submit‑btn`(注意缺少一个空格)，那么按钮的背景颜色将确实是`green`。

请记住，当属性之间没有冲突时，就不需要比武了。例如:

```
.welcome-message {
  color: salmon;
  background-color: seashell;
}

#headline {
  margin-top: 0;
  background-color: lavender;
} 
```

```
<h1 id="headline" class="welcome-message">
  Welcome to my blog
</h1> 
```

`color`和`margin-top`谈论的是不同的事情，所以它们可以同时应用于相同的元素，因为它们并没有互相争斗。然而，`background-color`正在战斗，所以为了这个属性我们使用比武来寻找胜利者。`#headline`在与`.welcome-message`的比赛中获胜，因此该元素被赋予`lavender`的`background-color`。应用于我们的`<h1>`的最后一组属性是:

*   `color: salmon;`
*   `margin-top: 0;`
*   `background-color: lavender;`

[![](img/73829680fd59d277ab700c771e54cad5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VggGAPAk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-094.png)

### 组合子

组合子在我们的锦标赛中被忽略。为了决定一轮比赛的获胜者，我们只需遵循上面的评分规则。您可能会认为，从像(后代组合子)这样的宽泛的选择器改变为像`>`(子组合子)这样的更狭窄的选择器会改变锦标赛的结果，但是组合子只影响 CSS 规则是否有资格首先参加锦标赛(因为它改变了我们的元素是否被实际选择)，并且它们对特异性分数没有任何影响。

这里有一个例子:我们的文本将是什么颜色？

```
p > span {
  color: firebrick;
}
p span {
  color: aquamarine;
} 
```

<small>这些代码样本来自[无痛 CSS](https://www.painlesscss.com) ，这是一个从基本原理开始教你 CSS 的书籍和视频课程。你可以在[课程](https://www.painlesscss.com)中下载包含所有 60 个代码样本和解决方案的完整报告。</small>

```
<p><span>What color am I?</span></p> 
```

[![](img/3df8e1a44643fa31bb0ec8d301475167.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sZ7ACdCp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-095.png)

答案是文字会`aquamarine`！看起来似乎`p > span`应该赢，因为额外的子组合符`>`使它看起来“更具体”，因为它缩小到了*的直系*子代，而不是*的所有*后代，但是`>`不影响任何特异性分数，所以当我们运行锦标赛时，我们有一个分数`002`与分数`002`竞争。由于这场比赛的结果是平局，最后一个选择器`p span`获胜，因为平局决胜规则，我们的文本被赋予颜色`aquamarine`。

这个例子揭示了关于级联的传统思维方式的问题。在这个例子中，将 CSSOM 与 HTML DOM 的构造方式进行比较的流行解释会误导您，因为树的结构与我们的锦标赛结果没有任何关系。

## 无痛 CSS 锦标赛

下面是我们所学内容的总结，稍后我将称之为*无痛 CSS 锦标赛*:自底向上的模型，我们从 HTML 元素的角度，而不是从浏览器渲染引擎的角度来看:

1.  逐一查看每个元素，询问“什么样的 CSS 规则适用于您？”
2.  查看您在步骤 1 中找到的所有规则，并列出适用于这些规则的所有属性(例如，包括所有继承的属性，并放弃所有来自父项的非继承属性)。
3.  从步骤 2 中的属性列表中，立即应用所有没有冲突的声明。你只剩下一个较小的处于战斗中的财产列表。
4.  逐个属性，使用锦标赛规则解决每场战斗。
5.  现在你完了！

将此与本书第九章中描述的解释 CSS 级联的经典(流行)方式进行比较:

1.  解析 CSS 并构造 CSSOM。
2.  通过 CSSOM 将规则“级联”到所有元素，使用树跟踪哪些规则适用于哪些元素。
3.  从步骤 2 中生成的树中，将所有声明应用到树上与其相关联的节点。
4.  如果属性之间有任何冲突，通过比较特异性得分来解决冲突。

自顶向下模型的缺点是它不能帮助我们调试单个元素的 CSS 问题。在自顶向下的模型中，我们必须遍历整个 CSSOM，以确定什么样式与特定的 HTML 元素相关联，并且我们不能在头脑中轻松地可视化 CSSOM 树。

相比之下，关注一个单独的元素并问“什么样的 CSS 规则适用于你？”要容易得多那就去找那些。事实上，浏览器中的开发工具允许您使用“检查”或“检查元素”功能来完成这一任务，这将有助于找到与元素相关的所有规则，而无需浏览整个网站:

[![](img/b631e53e0394046022dad3a906ec4e6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--muRoBhn0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-096.png)T3】

```
a {
  color: orchid;
}
body a {
  color: seagreen;
}
.command {
  color: royalblue;
}
a.command {
  color: sienna;
}
a[href].command {
  color: aqua;
}
#lasers {
  color: hotpink;
} 
```

<small>这些代码样本来自[无痛 CSS](https://www.painlesscss.com) ，这是一个从基本原理开始教你 CSS 的书籍和视频课程。你可以在[课程](https://www.painlesscss.com)中下载包含所有 60 个代码样本和解决方案的完整报告。</small>

```
<body>
  <a href="/" class="command" id="lasers">
    Activate lasers!
  </a>
</body> 
```

适用于被检查元素的所有 CSS 规则都方便地显示在侧边栏的列表中:

[![](img/7c1e6a6558ff796e5bc462f89c01e864.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Le3gK5Nl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-097.png)

尽管它被称为“级联样式表”，但我喜欢忽略整个“级联”概念，只考虑每个元素的特异性分数，以及它如何影响锦标赛的结果。

## 比武后

### 淘汰的顺序

当你打开开发者工具面板时，你可能已经注意到了一些很酷的东西:我们看到的 CSS 规则列表是根据他们在锦标赛中的名次排列的！

您可以将此面板视为锦标赛的“排行榜”,锦标赛的第一名冠军显示在顶部，然后下方的选择器是第二名冠军，依此类推。<sup>[【3】](https://www.painlesscss.com/guide-to-css-specificity.html#footnotes)</sup>

[![](img/94b17e0707f2073efef13cbaa6a51037.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--or8nfM4A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-098.png)

当您使用面板禁用样式时，这一点变得很明显。您可以单击任何声明旁边的复选框来启用或禁用它。如果我禁用了赢得第一名的声明，那么第二名的声明就是锦标赛的“获胜者”,所以它成为应用于 HTML 元素的样式。

[![](img/d97f37033768730b0ee9b3e21c5e79f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AhOskrYL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.painlesscss.com/blog-images/diagram-099.png)

您可以在调试时利用这种顺序。要找到一个具体的宣言，你应该从顶部开始，然后一路向下，了解发生了什么，因为更有可能的是，在锦标赛中幸存的最终财产接近顶部。

一旦你找到你要找的声明，你可以停在那里，你不需要继续向下滚动到底部，因为任何接近底部的东西都已经在很久以前输掉了比赛，这意味着如果你继续滚动，你就是在浪费时间——特别是在调试一个有数千种风格的大型网站时。

如果您一直滚动到底部，仍然找不到您的声明，请记住检查灰色区域中的用户代理样式表！可能是您忘记了设置组件的样式，而应用了默认样式。

## 超越层叠:CSS-in-JS

如果你自己在想，“这看起来非常复杂，需要跟踪很多工作”，我同意你的观点！

为了解决这个问题，社区发明了 CSS-in-JS 库和内联风格框架。级联、选择器、继承和特异性是 CSS 中最难理解的一些概念，所以许多开发人员通过使用一个抽象掉所有这些的库来回避这个问题，所以你再也不用担心这个问题了。这些框架还为您带来了其他好处，比如代码模块化(通过移除 CSS 全局范围)和挂钩自动化测试的能力。

关于 CSS-in-JS 库的好处的更多信息，我推荐观看马克斯·斯托伊伯的演讲。

内联风格框架强调了为什么你不应该通过记忆教条中的知识来学习。一些有经验的开发人员对听到“内联样式”有一种本能的负面反应，因为他们在职业生涯早期就被教导“永远不要使用内联样式”的最佳实践。甚至我在这本书的[第六章告诉你不要做！](https://www.painlesscss.com)

事实上，“永远不要使用内联样式”是很久以前的最佳实践，当时它使得 HTML 难以阅读、难以调试和难以更改。然而，这项技术不断发展，最终颠覆了导致这些“最佳实践”的假设。新的假设导致新的最佳实践。

只有当您手工编写内联样式时，它们才是令人痛苦的——但是如果您使用 CSS-in-JS 库来管理您的内联样式，这些缺点就会消失，您只剩下代码模块化、自动化测试和与前端 JavaScript 框架(如 React)协同工作的简单管理的优势。

如果你坚持“永远不要使用内联样式”的教条，你就不会有好奇心去探究当最初的假设改变时意味着什么。这就是为什么我组织了这本书来鼓励你使用[无痛 CSS 的五个步骤](https://www.painlesscss.com/#toc)来学习自己推理。

在你学习了像 React、Angular 或 Vue 这样的框架之后，你可以回来学习一个 CSS-in-JS 库用于你的项目。截至本文撰写之时(2019 年)，我为 CSS-in-JS 推荐的最流行的框架是[样式的组件](https://www.styled-components.com/)。

因此，如果您不必通过安装 CSS-in-JS 框架来使用级联或特殊性规则，为什么还要学习级联或特殊性呢？我认为这些仍然是需要学习的有用概念，因为虽然你的 CSS-in-JS 框架可以在 99%的情况下保护你免受层叠的危险，但在 1%的情况下，当你遇到一个可怕的职业生涯终结错误时，你可以有信心修复它并挽救你的职业生涯(真实故事)。

请记住，不是每个网站都使用 CSS-in-JS 框架，对于较小的网站，您可能不想设置一个麻烦，特别是因为它需要集成到更大的 JavaScript 框架中，如 React 或 Vue。在这些情况下，普通的 CSS 工作得很好。

最后，我认为学习所有这些仍然有用的最后一个原因是，因为…嗯嗯…“层叠”就在名字中:*层叠样式表*。

我希望你喜欢这篇文章，它为你揭开了 CSS 特异性和层叠的神秘面纱。本文节选自*无痛 CSS* ，这是一个从基本原理开始教你 CSS 的书籍和视频课程。你可以在 www.painlesscss.com 的T3 了解更多