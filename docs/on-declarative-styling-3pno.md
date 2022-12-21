# 论声明式风格

> 原文：<https://dev.to/lowmess/on-declarative-styling-3pno>

CSS 的核心有一个问题。这不是级联，或特异性，或渲染引擎之间的不一致-尽管这些事情可能会很烦人。不，比这简单得多:问题是我们可以写太多。

我不是在谈论[只追加样式表](https://css-tricks.com/oh-no-stylesheet-grows-grows-grows-append-stylesheet-problem/)(尽管这些也导致了它们的问题)。即使我们在重构 CSS 方面非常自律，并且我们只在绝对需要的时候才添加新的规则，某些东西仍然是错误的。问题是语言本身的灵活性。一个`padding`声明可以接受几乎无限的有效值，虽然极大地解放了这一点，但也在我们的设计中引入了不一致的表面区域。一致性是优秀设计的关键！它减少了最终用户的认知负荷，它(通常)看起来更好，并且它最小化了设计师&开发人员启动的工作量。

人为地限制我们可以在声明中使用的值的数量是避免这些不一致的关键。我们希望像`padding`这样的声明表现得更像`float`；我们应该只能设置我们在管理系统中定义的值。有许多技巧和技术可以帮助我们实现这一点(或者至少让我们接近这一点)。我称这些工具包含“声明式风格”的总体概念。

## 定义“陈述性”

这个术语——声明式风格——来源于计算机科学概念[声明式编程](https://en.wikipedia.org/wiki/Declarative_programming)。这意味着我们想告诉计算机绘制我们界面的规则，并让它为我们遵循这些规则。我们不再想写`padding: 1rem`，我们想写类似于`padding: 3`的东西，并让计算机用我们间距标度中的第三个值替换`3`。

这为我们完成了几件事。它通过允许我们使用一个引用而不是一个特定的值来确保我们设计的一致性。它还通过提供一种通用的交流语言来减轻利益相关者的认知负担。这些因素(以及其他因素)可以使设计和迭代更快，并且几乎消除了设计者-开发者交接中的固有摩擦。

## 设计令牌

那些熟悉[设计符号](https://css-tricks.com/what-are-design-tokens/)概念的人可能会发现这里有很多概念上的重叠。设计标记是声明式样式的重要组成部分:它们是我们定义样式选项的自定义子集的方式。如果样式表中的规则声明了一个`background-color`，那么应该可以在我们的令牌中找到这个样本。

有许多存储和解析设计令牌的技术。我偏爱基于 JSON 的[系统 UI 主题规范](https://system-ui.com/theme)，它将我们的令牌组织成各种尺度。下面讨论的几个工具依赖于这种或类似的技术，但是概念是相同的:这些值应该有一个真实的来源，而不应该是 CSS 规则本身。

## 技法

就像有许多方法来存储我们的令牌一样，也有许多方法来将它们应用到我们的样式中。

### CSS 变量

最常用的解决方案，也是您可能熟悉的解决方案，是尽可能使用变量。虽然 Sass 和 LESS 从一开始就有变量，但是 CSS 现在有了本地变量支持[和自定义属性规范](http://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)。

```
.box {
  padding: var(--spacing-3);
} 
```

与 Sass 和 LESS 等预处理程序的变量不同，自定义属性可以充分利用级联。这意味着我们可以在本地创建完全主题化的组件样式。

```
.btn {
  /* First we define our variables in the base component style */
  --button-padding-y: var(--spacing-2);
  --button-padding-x: var(--spacing-3);
  /* Then we apply our variables to our declarations */
  padding: var(--button-padding-y) var(--button-padding-x);
}

/* By updating our variables, the styles will change automatically */
.btn--large {
  --button-padding-y: var(--spacing-3);
  --button-padding-x: var(--spacing-4);
} 
```

为了充分利用这个系统，我们需要在`:root`选择器中将令牌转换成自定义属性。最简单的方法是手工复制和粘贴这些值，尽管有自动完成这个过程的工具。

当然，任何方法都有缺点。在这种情况下，最大的缺陷与其最大的吸引力相同:进入门槛低。没有护栏阻止你写`padding: 24px`而不是使用你的变量。不偏离系统需要很多训练，任何时候你写新的 CSS 都是制造差异的机会。但是结合严格的代码审查过程，这可能是在创建主题化系统时加强一致性的一种强有力的方法。

### 原子 CSS

原子 CSS(也称为功能 CSS，也称为实用优先 CSS)库，如 BassCSS、Tachyons 和 Tailwind CSS，根据定义是声明性的。像`p3`这样的类自动遵循我们上面的`padding`规则:我们告诉引擎使用间距标尺的第三步(`3`)将相等的填充应用到所有的边(`p`)。

```
<div class="p3">Hi</div> 
```

定制低音和超光速粒子可能会很难，[但这是可能的](https://www.lowmess.com/blog/customizing-tachyons/)。另一方面，顺风是[默认完全可定制的](https://tailwindcss.com/docs/configuration)。Tailwind 配置系统是一个从节点脚本导出的普通 JS 对象。这种方法的一个主要好处是，我们可以从 JSON 或 YAML 文件中读取令牌，并通过几行代码将它们应用到我们的自定义配置中。

我是原子 CSS 的超级粉丝。但我不是看不到缺点。学习曲线可能非常陡峭；我们不仅需要内化命名方案，还需要重新思考如何应用 CSS。因为我们还需要对我们的组件应用大量的类名，所以我也倾向于只为非常简单的项目或者具有强大模板系统的项目推荐这种方法。将原子 CSS 类应用于 React 组件或 Pug mixin 使得将类名应用于我们的元素变得更加容易接受。

### 样式系统

CSS-in-JS 的出现为我们提供了一个独一无二的机会:通过利用一种富有表现力、功能全面的编程语言，我们可以在 CSS 之上创建抽象，否则这是不可能的。例如，Brent Jackson 从原子 CSS 中吸取经验，并将其应用于 JSX 的 CSS-in-JS 系统，创建了几个专门构建的声明式样式库。

这些库中的第一个库是构建其他库的基础，被称为[风格系统](https://styled-system.com/)。该库提供了一个一致的接口，以便在定义或使用组件时引用您的设计令牌。风格化系统的真正天才在于[如何处理响应风格](https://styled-system.com/responsive-styles)。默认情况下，样式化的系统属性接受一个字符串，库解析该字符串并将其转换为由设计标记表示的值。如果找不到该标记，则将该文字值传递给底层 CSS-in-JS 库。但是，通过将 prop 传递给数组，默认情况下，它会将 0 索引值应用于组件，将 1 索引值应用于第一个断点处的组件，依此类推。

```
import styled from 'styled-components'
import { space, color } from 'styled-system'

const Box = styled.div` ${space}  ${color} `

const MyStyledSystemComponent = props => (
  <Box p={[2, 3]} color="primary" {...props}>
    Hi
  </Box>
) 
```

这种方法不仅允许我们创建与我们的系统一致的组件，而且还具有极高的可移植性。在设计组件样式时，我试图遵循的一条经验法则是，当组件在视图中实际使用时，只定义周围的垂直边距。通过将我们的 props 扩展到一个用 Styled System 定义的根组件，遵循这个规则就变得很简单了。

```
const MyView = () => (
  <main>
    <header>...</header>

    <MyStyledSystemComponent my="4" />

    <footer>...</footer>
  </main>
) 
```

因为 Styled System(在 React 中使用时)从您选择的 CSS-in-JS 库的`ThemeProvider`中读取放入上下文的主题，所以创建主题化组件是轻而易举的事情。然而，将`space`、`color`和其他标记引用附加到所有组件上可能会很烦人。幸运的是，杰克逊先生还创建了一个基于 Styled System 之上的原始组件库，名为 [Rebass](https://rebassjs.org/) 。这些组件像我们在`MyStyledSystemComponent`中使用的`Box`组件一样工作。

最近，Brent 发布了主题 UI ，这是这个想法的进一步发展。主题 UI 在 JSX 组件上公开了一个`sx`道具，允许我们引用我们的令牌，提供原始组件作为基础，用 MDX 处理 markdown 内容的样式，等等。这是这种方法的一个令人兴奋的发展，也是我打算在未来更全面探索的一个方向。

风格化的系统方法和其他方法一样，也有一些缺点。首先，它与原子 CSS 有着相同的学习曲线问题。设置起来可能有些麻烦，尤其是在不使用 Rebass 或主题 UI 的原始组件时。虽然它在技术上可以与任何支持 JSX 的框架一起工作，但 React 是唯一真正一流的系统公民。但是，如果可以选择的话，这仍然是我定义和消费风格的首选方法。

## 对未来的憧憬

网络一直是一个极其灵活和富有表现力的平台。这导致了一些令人惊讶的创造性的技术应用，新的媒体体验和美丽的互动。它也创造了大量的机会。通过在网络结构上设置自我约束，我们可以去除平台设计中更多的自我破坏因素。这些约束没有限制我们的创造力，而是为我们探索设计的极限提供了护栏，同时为我们的用户提供了一致且视觉和谐的界面。

声明式样式方法将会随着时间的推移而不断发展。随着设计令牌变得越来越普遍，设计软件将为它们添加一流的支持。更多的库和方法将会发展到将它们应用到我们的产品中，无论是在网上还是网外。当我们解决 CSS 的核心问题时，我们用来在构思和实现之间交流的语言将会融合为一。或许我们的工具也会如此。

* * *

*这篇文章最初出现在[我的个人博客](https://www.lowmess.com/blog/on-declarative-styling/)上*