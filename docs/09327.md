# 代码应该会呼吸

> 原文：<https://dev.to/mknepprath/code-should-breathe-336k>

*注:这是我的看法。如果你有想法或担忧，那很好-随时给我发信息。*

在编程中，人们可能会痴迷于简洁、紧凑和聪明(如魔法)。一行能做多少？我的组件中可以有几行？我可以把我的变量名缩写成多少？所有这些都导致了换气过度的代码等价。

如果我们让我们的代码喘口气呢？“呼吸空间”在这里被定义为代码中额外的行和空白空间。这里有几个方法可以做到这一点。

# 使用空行创建节

List Apart 有一篇关于空白在网页设计中的使用的很棒的文章，简单的标题是:空白。这篇文章提到了一个叫做**活动空白**的概念，“空白被添加到作品中，以更好地强调或组织信息。”在代码中，使用空白(空白行)来清楚地分隔导入、方法等等。这里有一个来自这个网站的例子:

```
 // External
  import parse from "date-fns/parse";
  import Link from "next/link";
  import { withRouter } from "next/router";

  // Components
  import Page from "core/page";

  // Data
  import { posts } from "../posts.json";

  class Writing extends React.Component {
    ... 
```

当然，在官方的 React 文档中有大量使用空格的例子。参见[将生命周期方法添加到类](https://reactjs.org/docs/state-and-lifecycle.html#adding-lifecycle-methods-to-a-class)一节中的代码示例。在这篇文章中，“hitespace 创造了呼吸的空间和平衡。这很重要。”

# 皮棉规则

如果存在强制添加更多行和空间的 lint 规则，就实现它。举个例子:[vue/html-结束-括号-换行符](https://github.com/vuejs/eslint-plugin-vue/blob/master/docs/rules/html-closing-bracket-newline.md)。

```
 <!-- Example A: -->
 <p
   id="foo"
   class="bar">
   baz
 </p>

 <!-- Example B: -->
 <p
   id="foo"
   class="bar"
 >
   baz
 </p> 
```

考虑到以上两个选项，**示例 B** 提供了更多的喘息空间。一看就比较好理解。将**示例 A** 乘以十几个具有不同数量属性的元素，会越来越难以看出属性的结束和文本/嵌套元素的开始。我建议在两个 lint 规则选项之间进行选择时，总是选择添加更多行和空间的那个。

# 关闭清晰区块

虽然我确实喜欢模板引擎 Jade 的简单性，但我确实认为由于缺少结束标签，人类的处理速度会有所下降。

```
<!-- Example A (Pug) -->
.fancy-link
    a(href="/")
    | Home
.fancy-link
  a(href="/about")
    | About

<!-- Example B -->
<div class="fancy-link">
    <a href="/">
        Home
    </a>
</div>
<div class="fancy-link">
    <a href="/about">
        About
    </a>
</div> 
```

虽然我喜欢帕格在这里的简洁，但我个人认为还有太多的想象空间。同样，将**示例 A** 乘以十几个不同的嵌套元素，就会变得密集而难以解析。

# 呼吸！

在一个名为 [Dwitter](https://www.dwitter.net/) 的有趣网站上，投稿人被挑战，看他们能用 140 个字符(或更少)的 JavaScript 创作出什么。虽然限制字符可能是一种有趣的约束，但这不是我想每天阅读的代码。相反，让我们尽情享受我们有空间呼吸的事实。

[![](img/fa2a0dc4c803bc14fc691b519e501aee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rKSbujTW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4navxlkxzsyyfea1n0nq.jpg)