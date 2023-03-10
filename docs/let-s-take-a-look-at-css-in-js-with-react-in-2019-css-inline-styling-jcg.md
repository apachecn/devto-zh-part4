# 让我们来看看 2019 年 JS 中的 CSS 与 React-CSS &内联样式

> 原文：<https://dev.to/phizzard/let-s-take-a-look-at-css-in-js-with-react-in-2019-css-inline-styling-jcg>

# 让我们一起来看看 2019 年 JS 中的 CSS 与 React-CSS&内联样式

JS 中的 CSS 并不是 React 所独有的，但是，我是 React 的一个小粉丝，而且它恰好是我最喜欢用来编写前端应用程序的 JS 库之一，所以我将专门用 React 来谈论 JS 解决方案中的 CSS，以及我对它们的看法！

### 介绍同常规 CSS

在我们深入研究任何东西之前，我认为我们应该看看在 React 应用程序的上下文中，我们可以用一些好的 CSS 来完成什么。

```
// Button.js
import React from 'react'
import './Button.css'

const Button = () => {
  return(
    <button className="button button-green">
      I think I'm green
    </button>
  )
} 
```

```
/* Button.css*/
.button{
  border-style: solid;
  border-width: 2px;
  border-radius: 2rem;
}

.button-green {
  background-color: green;
  border-color: white;
  color: white;
} 
```

这看起来很正常，对吧？除了`className`之外，它看起来像一些常规的 HTML 和 CSS。如果你对 React `class === className`不熟悉，因为`class`是 JS 中的保留字，而 JSX 是嵌入 JS 的 HTML，所以这是不允许的。

### 我在使用 CSS 和 React 时遇到的问题

在我们开始之前，我需要声明我绝对不是 CSS 的专家或大师。我可以用它来踢我的脚，制定看起来不错的响应规则。我无法说出任何疯狂的 CSS 招数，也无法用纯 CSS 创造一个动画皮卡丘。

正因为如此，我甚至不会假装谈论 CSS 的所有缺陷或任何旨在修复这些缺陷的 CSS 新功能，所以我将从 2014 年开始放弃这个优秀的[资源](https://speakerdeck.com/vjeux/react-css-in-js?)。我会让你决定它是否仍然成立！；)

#### 全局命名空间❌

##### 你是说我可以使用 BEM 吗？

[![Gif: Morpheus smiling](img/d0a638a8f03200c4997e0cd83dbd215b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--otnw5G2J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u9un0nw1tgt8jfiths5n.gif)

> 到时候就不用了。

如果你看一下我在上面放的链接，你会注意到全局名称空间是它涉及 CSS 的第一个问题。然而，我们都感受到了这种颤动，这就是像 BEM 这样的命名约定存在的原因。

CSS 示例中的`.button`和`.button-green`已经是我们引入的两个全局名称空间。

随着应用程序的增长，我发现 CSS 规则也在不断增长。创建不必要的重复样式和不使用的样式会使应用程序变得臃肿。虽然有一些配置可以确保未使用的 CSS 不会包含在您的包中，但是它们不会从您的代码库中消失，这很糟糕。

#### 松耦合❌

虽然您可以构建您的 react 应用程序，以便您的组件样式与您的组件存在于同一目录中，但是在您的组件文件中没有任何东西将它们严格地捆绑在一起。相反，您引用了您为选择器指定的规则。不管文件结构如何，这些样式都可以在其他地方引用；这只是另一件需要考虑的事情。

#### 笨拙的控制着❌

当只使用 CSS 时，你或多或少会因为改变一个元素的类而控制所有的样式变化。虽然使用普通的 Javascript 或 JQuery 看起来更自然，但在使用 React 时，我总是感觉很别扭。您可以在单独的组件中直接访问您的视图，但是我们将所有这些逻辑都用于引用 CSS 类的不同组合。

### 我知道内嵌式造型

[![gif: Morpheous leans close and says "Show me"](img/e7f4608f981db27cab71cc885b2cc208.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1xb8fWQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5sveo66dn3up9pj51yv8.gif)

另一种不用任何模块来设计 React 应用程序样式的方法是内嵌样式。现在，不要理会坐在后面叫嚷内联风格有多糟糕的人，因为那实际上是伪装的我。我们都被告知 HTML 中的内联样式不好，基于类的样式很棒。然而，这是 JSX。

```
// Button.js
import React from 'react'

const Button = () => {
  const buttonGreen = {
    backgroundColor: "green",
    border: "2px solid white",
    borderRadius: "2rem"
    color: "white"
  };

  return(
    <button style={buttonGreen}>
      I think I'm green
    </button>
  )
} 
```

这看起来没那么糟，对吧？

您可能会注意到，这里的 CSS 规则看起来不太一样。我们用骆驼案代替烤肉串案作为我们的规则。它们被映射到一个 Javascript 对象中，它们的值以字符串的形式包含在引号中。

#### 全局命名空间✅

[![Neo and Morpheus in a dojo, ready to fight](img/08f2cd74fd6d908c73b60bac0bd0a988.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x24vHb16--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/np3unopr4wpvoak8q7vr.jpg)

在我们的内联样式示例中，`buttonGreen`是该文件的本地变量，因此我们可以在整个应用程序中拥有任意多的`buttonGreen`变量，而不会遇到任何冲突或遵循任何特定的命名约定！

#### 松耦合✅

[![Gif: Morpheus tells neo to "Free your mind" and leaps across a rooftop](img/e6c8d19651734853d2e43e681dd52392.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K3xWnJ-m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wkcgib6mlhowx9w257ff.gif)

由于这些样式是本地定义的，所以您不能使用这些样式，除非您努力导出和导入它们；理想情况下，有足够的步骤来阻止不好的事情发生。我认为这也促进了开发人员以更有目的的方式使用 React 来创建可重用的通用组件。

#### 笨拙的控制着✅

```
// Button.js
import React from 'react'

const Button = ({backgroundColour, colour, children}) => {
  const buttonStyles = {
    backgroundColor: backgroundColour,
    color: colour,
    border: "2px solid white",
    borderRadius: "2rem"
  };

  return(
    <button style={buttonStyles}>
      {children}
    </button>
  )
} 
```

```
// SomePage.js
import React from 'react';
import Button from 'Button';

const SomePage = () => (
  <Button backgroundColour="blue" colour="white">I'm going to be blue</Button>
) 
```

现在，这是一个超级简单的例子，但是我们可以看到，我们已经通过传递到组件中的一些道具，对颜色进行了特定的控制。我喜欢这种方法，因为它是自文档化的，并且保留了组件中的所有逻辑；我确切地知道组件中的道具在控制什么，并且组件的用法非常清楚，它将背景颜色设置为蓝色，将颜色设置为白色。

### 我不喜欢内嵌样式的地方

[![Neo getting kicked by Morpheus](img/ea1f7e669bf32d108b2dfb83ec234c0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i74E0vqk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/56h60uf14dme5syvmx85.jpg)

#### 将对象用于样式

我不喜欢这种样式语法。这种背离足以导致大量繁琐的工作:将 CSS 转换成 Javascript 对象，用`,`替换`;`，在`""`中包装规则，将键打包成 camel case。对于你的设计师来说，这种方法一看到就会被关闭，这也是很可怕的。

#### 内联样式仍然是内联

说到底，这仍然是内联样式，你的 HTML 仍然会以那些难看的样式属性结束。

### 我个人会在 React 中使用内嵌样式吗？

没有。

但这并不是 JS 中亲爱的老 CSS 的终结！有一些非常酷的软件包，它们做了一些很酷的事情，试图用 JS 中的 CSS 来解决各种关于可读性、可伸缩性和性能的问题。

在这个系列中，我将尝试查看这些解决方案的不同组合，无论它们是被高度评价还是被埋没。

在撰写本文时，我已经使用了样式组件、情感和 JSS。如果你认为其中一个真的很酷，请随意发表评论！

如果你想关注我或者在那里聊天，我也在 Twitter 上。我有时在那里张贴东西！