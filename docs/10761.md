# 发现 Jest 隐藏功能自动锁定

> 原文：<https://dev.to/slashgear_/discover-jest-hidden-feature-automock-2c9f>

这篇文章的主题是关于笑话和嘲笑。我不希望在这里提倡使用“单元测试”。每个人也可以自由定义对他/她来说什么是单一的。我只是想向你展示 Jest 如何管理它的*模拟*以及*自动锁定*如何在日常生活中对你有用。为了理解这篇文章的目的，了解笑话的基本知识是很重要的。

# TL；速度三角形定位法(dead reckoning)

Jest *automock* 速度很快，将帮助您轻松添加测试。当你总是想要隔离你的测试时，考虑使用*自动锁*。这将通过协调它们的创建方式来简化 mock 的使用。

## 上下文

几年来，我一直在允许时间和鼓励人们编写测试的环境中工作。两年多来，我一直在 M6 集团的技术团队工作。

我们为我们的网络平台使用了两个*【kind】*测试:

*   **“单元测试”**使用 Jest 和 *automock* :在隔离的环境中测试我们的服务和组件。每个测试将只关注一个特定的模块，考虑到所有其他的模块都被模仿。

*   **“特性/功能测试”**使用 CucumberJS 和 WebdriverIo:在半真实环境中使用用户操作来测试产品的功能。

测试环境非常具体。你可能没有同样的方法去做。但是这种情况很好地解释了我们对*自动闭塞*的兴趣。我们确实经常使用`mocks`，而 *Jest* 会自动为我们嘲讽一些事情，这很棒。

如果你对测试感兴趣，我可以推荐 Kent C. Dodds 在 last Assert(JS)举办的这个伟大的会议。

[https://www.youtube.com/embed/Fha2bVoC8SE](https://www.youtube.com/embed/Fha2bVoC8SE)

[![kentcdodds image](img/8721935c1404dd20b347a8ad45800fb8.png)](/kentcdodds)

## [肯特·c·多兹](/kentcdodds)

[My name is Kent C. Dodds and I'm a JavaScript engineer and teacher. I'm also active in the open source community. I like my family, JavaScript, and React.](/kentcdodds)

如果你听过 Kent 的演讲，他建议主要做集成测试。

然而，通过遵循 TDD 原则，测试对我来说确实是一个很好的开发工具，可以让我一步一步地构建我的设计并向前推进。今天，我仍然很难根据集成测试来设计我的开发。

此外，通过使用“单元测试”，我可以更容易地重构我的代码，从而在实现上获得更大的灵活性。在我的使用中，一个模块中的实现变化只会影响相关的测试，除非契约被修改。

## 如何？

要激活*自动锁*，非常简单。只需在 *jest* 配置中定义这一点。

```
module.exports = {
  automock: true,
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您在测试中进行的所有*导入*都将被自动嘲笑。因此，你需要*取下*你想要测试的模块。使用这种技术，您不再需要在文件开头定义 10 行*模拟*。

```
import dependency from 'dependency';
import { myMethod } from '../module/foo.js';

//the lines below are not necessary with automock activated
jest.mock('../module/foo.js');
jest.mock('dependency'); 
```

Enter fullscreen mode Exit fullscreen mode

在 *React* 组件测试的情况下，你所依赖的所有组件都将被嘲笑。修改单个组件时，不再有破坏多个测试文件的风险。

当你的模块依赖于*异步函数*时，你可以很容易地用`mockResolveValue`和`mockRejectValue`存根它们的错误或成功行为。

## 调用`jest.mock`会发生什么？

在解释我为什么喜欢使用 automock 之前，让我们看看当 Jest 被要求模仿一个模块时，他做了什么。

当你在模块上使用`jest.mock`时。每个导出的值将被转换成这样(递归):

*   `Function`将被改造为*间谍*功能做`noop`(就像，`jest.fn()`)
*   `Array`将被转换为空数组。
*   `ES6 Class`会像函数一样被转换
*   `Number`、`Object`、`String`不会受到影响。

总的来说，我们可以说，如果我们想将我们的测试与所有现有的模块隔离开来，缺省的 mock 做了我们所希望的事情。

`Automock`的工作方式和`jest.mock`一样，只是针对测试中所有导入的模块。这会节省你很多时间。

# *auto mock*有什么了不起的？

对我来说，激活 jest 的 automock 意味着充分利用 Jest 的能力来生成模块的模拟版本。

不需要怀疑你正在导入的模块或依赖项是否被模仿。绝对一切都是嘲弄。

测试中唯一的“噪音”来自于您想要测试的文件。

有些人可能认为启用这个选项会降低测试速度。别担心。在我从事的项目中，我们有 3000 多个测试在不到 4 分钟的时间内运行。

## 提示

*   在一个有很多测试的大项目上切换到`automock`将是困难和危险的。
*   当你的测试表现奇怪的时候，也许你忘记了你正在测试的模块。这会让你发疯的。

*   一旦你被迫复制一个模拟，尝试外包。

*   别忘了`jest`是被一个空的`Array`自动嘲讽`Array`。

如果你喜欢这篇文章，不要犹豫，分享它！如果你做测试的方式不同，请与我们分享你的建议。

请随意与 Jest 分享您的*技巧*。