# 不，打字稿不是浪费时间。

> 原文：<https://dev.to/resir014/no-typescript-is-not-a-waste-of-time-2hpk>

*标题图片由[克里斯·莱格特](https://unsplash.com/photos/AoEEtI6onH4)在 Unsplash 上拍摄。*

又是一天，又是一场关于 JavaScript 中的静态类型既是自切片面包*以来最伟大的事情，又是对整个人类来说最糟糕的事情的激烈“讨论”。*

让我们来看看最近的一篇[dev . to post](https://dev.to/parkroolucas/typescript-is-a-waste-of-time-change-my-mind-pi8)，这篇文章再次激起了这场辩论。我会试着澄清一些误解，并希望以冷静的态度对待事情。

在我开始之前，我想改变一下我使用的一些术语，尤其是标题中的那个。我更愿意使用术语“类型化 JavaScript”，而不是特指 [TypeScript](https://www.typescriptlang.org/) 。因为在类型化的 JS 土地上还有另一个工具，[流](https://flow.org/)，我不想遗漏流用户。毕竟，我们的目标是一样的，在我们的应用程序中加强类型的可靠性/安全性。

我想加入术语表的另一个术语是“动态类型”JS，简称“动态”。不管最初的帖子想让你相信什么，编写不带类型注释的 JS 代码并不意味着你的代码没有类型。用 dynamic JS 写的字符串还是有类型`string`。所以是一个`number`、`boolean`，你懂的。你不需要明确地表达这些类型。

## 是的，开始写静态类型的 JS 短期比较长…

我跟你说实话:编写动态类型的 JS 在短期内会更快。从一个打字稿倡导者那里听到这些你可能会感到惊讶，但是我是认真的。真的！

假设您正在编写 Node.js 库。如果你是用动态类型的 JS 编写的，你可以编写你的库并发布它，所有这些都不需要通过任何构建工具。就那么快！对于做一件事的微型库来说，因为速度的原因，这样写是最有效的方法。

但是现在，假设你正在 Node.js 中编写一个完整的后端，它只是一个很小的 API 服务，有几个端点。您已经用 JavaScript 编写了身份验证系统、中间件和控制器。因为它是一个具有次要功能的小端点，所以您使用普通的 ol' JavaScript。

现在，想象一下，这个小小的 API 服务被捆绑成一个拥有成千上万代码的成熟平台 API。大概上万行代码。然后你意识到你在你的一个端点发现了一个错误。哦亲爱的！单元测试没有发现这个问题，所以你不得不花几个小时在你的应用中寻找问题。甚至可能设置断点，或者甚至做老式的`console.log`驱动的调试。

然后，你发现了问题。还记得有一次你重构了那个中间件吗？您还更改了导出函数的名称。当然，您已经对中间件进行了单元测试，但是您的单元测试只针对该中间件。

然后，您看到了一个导入了中间件的文件。当然可以。您更改了导出的函数名，但忘记了重命名导入。

仅仅因为打字错误或文件丢失就损失了数小时的工作效率！

## …但是长期影响是真实的！

当然，你也可以用林挺工具检查不匹配的导入。但是，您可能还想重命名一个函数，以及更新导入该函数的所有文件的函数名，只需点击一个按钮。毕竟人类是会犯错的，像这样遗漏的事情并不少见。TypeScript 对[快速重构](https://code.visualstudio.com/docs/editor/refactoring)和查找并替换支持的支持可以帮助您处理这个问题。因此，您可以将更多的精力放在编写代码上，而不是手动进行无意义的查找和替换。

像 TypeScript 和 Flow 这样的静态类型检查器也有助于减少代码中的错误，方法是在编译时检测这样的错误。这也有一些统计证据。一般来说，在 JavaScript 代码[中使用静态类型可以帮助防止提交代码中大约 15%](https://blog.acolyer.org/2017/09/19/to-type-or-not-to-type-quantifying-detectable-bugs-in-javascript/) 的错误。

当然，这将使用 TypeScript 开始一个项目慢得多，因为你需要在应用程序的早期定义类型、接口等等。但我认为，让你以类型和/或接口的形式编写实现模型，会让你在一天的早些时候考虑应用程序的数据结构。

从长远来看，这极大地提高了你的应用程序的信心。当你很好地使用这些类型时，在许多情况下你甚至不需要类型，这要感谢 TypeScript 的[基于控制流的类型分析](https://mariusschulz.com/blog/control-flow-based-type-analysis-in-typescript)。TypeScript 在大型应用程序上的好处超过了启动 TypeScript 项目所需的较长时间。

这是你将来会做的投资吗？对我来说当然是，但是我不会对你的应用程序做任何预先的判断。这项投资是否值得，还是要由你来决定。

## 可以增量采用 TypeScript

也许你已经在维护一个用普通 JavaScript 编写的中大型应用程序。您想迁移到 TypeScript，但是害怕红色的曲线会在您睡觉时困扰您。你将如何着手移植你的代码？

迁移到 TypeScript 有各种指南。在巴萨拉特·阿里·赛义德的令人敬畏的[打字稿深度潜水](https://basarat.gitbooks.io/typescript/content/docs/types/migrating.html)手册中就有一个。我还写了一个全面的指南[在这里](https://dev.to/resir014/migrating-to-typescript-part-1-getting-started-n4l)。

TypeScript 的另一个巧妙之处是能够通过 JSDoc 注释推断普通 JS 文件的类型，所以如果您编写了有效的 JSDoc 注释，并且打开了 JS 类型检查，那么您就可以很容易地进行迁移。

[![infer-types-from-jsdoc](img/2223ffde056edfb73d66ef71d7435272.png)](https://i.giphy.com/media/fMzOUcjYX6k8GdC8a3/giphy.gif)

尽管不可否认，迁移体验是 TypeScript 的不足之处。我链接第三方指南的原因是——好吧——TypeScript*有[官方移民指南](https://www.typescriptlang.org/docs/handbook/migrating-from-javascript.html)，尽管它已经过时得可怕了。官方文档也做出了硬性的假设，即用户知道一些关于静态类型语言的*知识，所以我不会向新手推荐它们。**

 *尽管放心，打字团队已经在着手[修改文档](https://github.com/microsoft/TypeScript/issues/31983)，以及[一本新的手册](https://github.com/microsoft/TypeScript-New-Handbook)，它有望更渐进地教授打字。

## 但是动态的，运行时的值呢？

诚然，TypeScript 团队已经明确声明，将静态类型检查扩展到运行时是 TypeScript 编译器本身的[非目标](https://github.com/Microsoft/TypeScript/issues/1573#issuecomment-68374376)。但是在现实中，我们仍然必须处理这些运行时边界。一个常见的例子是从 API 读取 JSON 输出，或者使用 HTTP 请求负载。

由于社区对 TypeScript 有很强的支持，社区已经为这个问题开发了很好的解决方案。有像 [io-ts](https://github.com/gcanti/io-ts) 这样的工具，可以用来确定 ts 中的运行时表示。一个合适的流程替代方案是[流程运行时](https://gajus.github.io/flow-runtime/)。

## 静态打字和测试齐头并进！

到目前为止，我们已经做了很多工作来确保静态类型的应用程序的类型安全。尽管如此，还是有一些静态类型无法捕捉的错误。举个简单的例子，测试那个切换按钮在被点击时是否正确显示其相反的状态。

我是肯特·c·多兹的[测试奖杯](https://kentcdodds.com/blog/unit-vs-integration-vs-e2e-tests)模型的粉丝。在他的模型中，林挺/静态分析/静态类型检查和单元测试都位于奖杯的“底座”中。这意味着它们都是构建测试体验不可或缺的一部分，可以唤起对代码的信心。因此，我想说的是，静态类型和单元测试在帮助你编写错误更少的代码方面是齐头并进的。

让我们将上面的切换按钮示例编写成代码。我们使用 TypeScript 作为静态类型，Jest + react-testing-library 来测试我们的代码。

下面是 React 中实现的上述组件的一个示例。

```
import * as React from 'react'

interface ToggleButtonProps {
  enabledText: string
  disabledText: string
}

function ToggleButton({ enabledText, disabledText }: ToggleButtonProps) {
  const [toggle, setToggle] = React.useState(false)

  const handleToggle = () => {
    setToggle(!toggle)
  }

  return (
    <div>
      <span>{toggle ? enabledText : disabledText}</span>
      <button onClick={handleToggle}>Toggle</button>
    </div>
  )
}

export default ToggleButton 
```

Enter fullscreen mode Exit fullscreen mode

从表面上看，似乎静态类型已经完成了它的工作。然而，如果我们仔细观察，我们可以为切换按钮设置一个自定义的状态文本。当然，TypeScript 可以检查我们传递给`enabledText`和`disabledText`属性的字符串是否是一个字符串。但这只是战斗的一半。

毕竟，如果我们已经将按钮的启用和禁用状态分别设置为`'On'`和`'Off'`，我们希望它在禁用时正确显示`'Off'`，在启用时正确显示`'On'`。而不是相反。

因为我们已经通过 TypeScript 检查了组件及其 props 的类型，所以我们可以专注于测试按钮的行为。

下面的例子使用 [Jest](https://jestjs.io/) 作为我们的测试运行程序，使用 [react-testing-library](https://testing-library.com/docs/react-testing-library/intro) 作为我们的 react 测试工具。

```
import * as React from 'react'
import { render, cleanup, fireEvent } from '@testing-library/react'
import ToggleButton from './ToggleButton'

describe('ToggleButton', () => {
  afterEach(cleanup)

  test('correctly renders the state of button', () => {
    const { getByText, queryByText } = render(<ToggleButton enabledText="on" disabledText="off" />)
    // Test the initial state of the button.
    expect(getByText('Off')).toBeDefined()
    expect(queryByText('On')).toBeNull()

    // Fires a click event to the button.
    fireEvent.click(getByText('Toggle'))

    // Test if toggle state is correctly modified.
    expect(getByText('On')).toBeDefined()
    expect(queryByText('Off')).toBeNull()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了两件事。

*   静态类型通过检测类型错误并允许开发人员通过优秀的 IDE 工具自信地进行重构，提供了可靠性并改善了开发人员的体验。
*   单元测试提供了对我们的代码行为方式的信心。

[在 CodeSandbox 中试用](https://codesandbox.io/s/react-ts-unit-test-example-2u85p)

## 让我们理清思路

最初的帖子包含了很多主观的观点，这是一个遗憾，因为我喜欢一些客观的推理，为什么静态类型不值得花时间。

我对原帖的反驳…也包含了很多主观点。不过没关系！因为我写这篇文章的目的不是谈论一种技术如何比另一种技术“客观上更好”。我试图概述一种技术如何比另一种技术更有利于用户，反之亦然。并且找到他们之间的共同点。或者至少，[我尝试过](https://www.destroyallsoftware.com/talks/ideology)。

不要建立煽动性的、伪装成“客观”的“事实”的主观观点，让我们以冷静的态度对待事情，并理解某些工具的存在是出于某些原因。建设性的批评是提高我们所有人的巨大力量，不管你在这场辩论中站在哪一边。

因为我自己是前端开发人员，我想举的一个很好的例子是三大框架( [Angular](https://angular.io/) 、 [React](https://reactjs.org/) 和 [Vue](https://vuejs.org/) )之间无休止的争论，以及为什么一个比另一个好。

例如，Vue 和 React 的开发人员经常大动干戈，写一些毫无意义的关于一个比另一个更好的中型文章。我自己也是一个 React 的人，但我仍然明白[尤雨溪有他自己的问题要用 Vue 解决](https://www.youtube.com/watch?v=ANtSWq-zI0s)，因此他解决的问题是他的框架的关键卖点。其中最突出的是学习曲线和采用它的容易程度。

TypeScript 和 Flow 背后的人足够聪明，能够解决他们在编写 JavaScript 时的一个痛点。他们希望编写可以在大型项目中伸缩的 JavaScript 代码。他们的方法是提供一个静态类型超集，确保类型的可靠性和安全性，并通过开发人员工具提高生产力，这些工具得益于静态类型的强大功能。对某些人来说，这很有效。TypeScript 和 Flow 都在运行许多大中型项目(包括我工作的地方)，我可以想象它们让工程师编写代码时错误更少的所有方式。

TypeScript 对你来说可能是浪费时间，但对我来说绝对不是浪费时间。

不要误解我，写普通的 JS 也没有错！也许你想在项目的早期阶段迭代得更快，所以你选择了普通的 JS，而不是直接跳到 TS。也许你想从一开始就开始使用 TypeScript。这两个都可以。毕竟，只有*你*知道如何最好地开发你的应用。只有*你*知道如何提供更好的用户/开发者体验。

因为不管我们选择什么样的工具、语言和框架(或者缺少它们)，最终都会有一个可用的应用程序。

对吗？*