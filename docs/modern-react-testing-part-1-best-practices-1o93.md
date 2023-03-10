# 现代反应测试，第 1 部分:最佳实践

> 原文：<https://dev.to/sapegin/modern-react-testing-part-1-best-practices-1o93>

这一系列文章是 React 组件和前端测试现状的深入快照，解释了许多*为什么*，而不仅仅是*如何*。我们将讨论为什么要写自动化测试，写什么测试以及如何写。在实践文章中，我们将学习如何使用 Jest、Enzyme 和 React 测试库来测试 React 组件。

三年前我写了一篇类似的文章，现在我看起来就像在看一本关于不良行为的手册。几乎所有我当时推荐的东西，我都不再做了。

这是系列文章的第一篇，在这里我们学习了为什么测试自动化是有用的，写哪种类型的测试，以及测试最佳实践。

*   **现代反应测试:最佳实践(*本帖* )**
*   [现代反应测试:Jest 和酶](https://dev.to/sapegin/modern-react-testing-part-2-jest-and-enzyme-46kk)
*   [现代反应测试:Jest 和反应测试库](https://dev.to/sapegin/modern-react-testing-part-3-jest-and-react-testing-library-3n0i)

[订阅](https://tinyletter.com/sapegin)了解第二篇和第三篇。

## 为什么要自动化测试

自动化测试有用的原因有很多，但我最喜欢的原因是:*您已经在测试*。

例如，您正在向页面添加一个新按钮。然后你在浏览器中打开这个页面，点击这个按钮，检查它是否工作——这是一个*手动测试*。通过自动化这一过程，您可以确保过去正常工作的功能将始终正常工作。

自动化测试对于很少使用的功能特别有用:我们总是测试按钮是否提交了所有字段都正确填充的表单，但是我们往往会忘记测试隐藏在模态中并且只由你老板的老板使用的复选框。自动化测试将确保它仍然工作。

自动化测试的其他原因是:

**修改代码的信心**:写得好的测试可以让你放心地重构代码，不会破坏任何东西，也不会浪费时间更新测试。

文档:测试解释了代码如何工作以及预期的行为。与任何书面文档相比，测试总是最新的。

**bug 和回归预防**:通过为你的应用程序中发现的每个 bug 添加测试用例，你可以确保这些 bug 永远不会回来。编写测试将提高你对代码和需求的理解，你将批判性地审视你的代码并发现你可能会忽略的问题。

与手工测试相比，自动化测试可以在提交到存储库之前捕捉到 bug，而手工测试可以在测试甚至生产过程中发现大多数 bug。

## 考什么

由[迈克·科恩](http://www.mountaingoatsoftware.com/)介绍的测试金字塔，可能是最流行的软件测试方法:

[![UI < Service < Unit](img/20b6de933578ad1f6b86fccf773f8069.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5065oeYS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/6a272c062bae7d73b73d3a7e03755e6019d2563d/47aimg/testing-pyramid.svg)

上面说 UI 测试写起来最慢最贵，单元测试写起来最快最便宜，所以要写很多单元测试，很少 UI 测试。

单元测试是测试一个单一的代码单元，比如一个函数或者一个反应组件。你不需要浏览器或数据库来运行单元测试，所以它们非常快。 *UI 测试*测试在真实浏览器中加载的整个应用程序，通常使用真实的数据库。这是确保你的应用程序的所有部分协同工作的唯一方法，但是它们很慢，编写起来很棘手，而且经常不稳定。*服务测试*介于两者之间:它们测试多个单元的集成，但是没有任何 UI。

这在后端可能工作得很好，但是在前端 UI 细节经常改变而不改变更大的用户流，这导致许多单元测试失败。我们花了很多时间来更新单元测试，但是对更大的功能仍然有效没有足够的信心。

因此，也许前端需要一种不同的测试方法？

Kent C. Dodds 推出的测试奖杯、[越来越受前端测试的欢迎:](https://kentcdodds.com/blog/write-tests)

[![End-to-end < Integration > Unit < Static](img/460166b65a1f6b97258eb895a3754fd9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2mFHY-3E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/c4344c0b515b9dd841e4cbaab8aae1f7d422cc67/87fimg/testing-trophy.svg)

它说集成测试给你最大的投资回报，所以你应该写更多的集成测试。

*trophy 中的端到端测试*大多对应金字塔中的 UI 测试。*集成测试*验证大功能甚至整个页面，但没有任何后端、真正的数据库或真正的浏览器。例如，呈现一个登录页面，键入用户名和密码，单击“登录”按钮并验证是否发送了正确的网络请求，但不要实际发出任何网络请求——我们稍后将学习如何操作。

即使编写集成测试的成本更高，但与单元测试相比，它们有几个好处:

| 单元测试 | 集成测试 |
| --- | --- |
| 一项测试仅涵盖一个模块 | 一个测试覆盖整个特性或一个页面 |
| 重构后通常需要重写 | 在大部分时间里经受住重构 |
| 难以避免测试实现细节 | 更好地模拟用户使用你的应用的方式 |

最后一点很重要:集成测试给了我们最大的信心，让我们相信我们的应用程序能按预期运行。但这并不意味着，我们应该只写集成测试。其他测试也有它们的位置，但我们应该把精力集中在测试上，这是最有用的。

现在，让我们仔细看看每个测试奖杯级别，从最底层开始:

1.  **静态分析**捕捉语法错误、不良做法和 API 的不正确使用:
    *   代码格式化者，像[更漂亮的](https://prettier.io/)；
    *   棉绒，像[ESLint](https://eslint.org/)；
    *   打字检查器，像[打字稿](https://www.typescriptlang.org/)和[流程](https://flow.org/)。
2.  **单元测试**验证复杂的算法是否正确工作。工具:[笑话](https://jestjs.io/)。
3.  **集成测试**让您确信您的应用程序的所有功能都能按预期运行。工具: [Jest](https://jestjs.io/) 和[酵素](https://github.com/airbnb/enzyme)或[反应-测试-库](https://github.com/kentcdodds/react-testing-library)。
4.  端到端测试确保你的应用作为一个整体工作:前端、后端、数据库和其他一切。工具:[柏树](https://www.cypress.io/)。

我认为 Prettier 也是一个测试工具，因为它经常让错误的代码看起来很奇怪，所以你开始质疑你的代码，仔细阅读并找到一个 bug。

其他种类的测试也可能对你的项目有用。

## 测试最佳实践

### 避免测试内部构件

想象一下，您有一个订阅表单组件:一个电子邮件输入和一个提交按钮，您想测试一下，当用户提交表单时，会出现一条成功消息:

```
test('shows a success message after submission', () => {
  const wrapper = mount(<SubscriptionForm />);
  wrapper.instance().handleEmailChange('hello@example.com');
  wrapper.instance().handleSubmit();
  expect(wrapper.state('isSubmitted')).toBe(true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这项测试有几个问题:

*   如果您改变处理状态的方式(例如，用 Redux 或 hooks 替换 React state ),甚至重命名状态字段或方法，这个测试将会中断；
*   它没有从用户的角度测试表单实际工作的情况:表单可能没有连接到`handleSubmit`方法，当`isSubmitted`为真时，成功消息可能不会出现；

第一个问题叫做*假阴性*:即使行为保持不变，测试也失败了。这样的测试使得重构非常困难，你永远不知道测试失败是因为你破坏了什么还是因为测试不好。

第二个问题叫做*假阳性*:即使代码被破解，测试也是通过的。这样的测试不能让你确信代码确实在为用户做一些有用的事情。

让我们重写测试并解决这两个问题:

```
test('shows a success message after submission', () => {
  const {getByLabelText, getByText, getByRole} = render(<SubscriptionForm />);
  fireEvent.change(getByLabelText(/email/i, { target: { value: 'hello@example.com' } });
  fireEvent.click(getByText(/submit/i);
  expect(getByRole('status').textContent).toMatch('Thank you for subscribing!');
}); 
```

Enter fullscreen mode Exit fullscreen mode

请参阅 Kent C. Dodds 的[测试实施细节](https://kentcdodds.com/blog/testing-implementation-details)文章了解更多详情。

好的测试验证外部行为是正确的，但不知道任何实现细节。

### 测试应该是确定性的

[非确定性测试](https://martinfowler.com/articles/nonDeterminism.html)是指有时通过，有时不通过的测试。

一些可能的原因是:

*   不同的时区；
*   不同的文件系统(不同的路径分隔符)；
*   一个数据库，在每次测试之前不会被清空和重新填充；
*   状态，在几个测试用例之间共享；
*   依赖于测试用例运行的顺序；
*   测试异步行为的超时。

有许多方法可以处理非确定性测试，如轮询、假计时器或模拟。我们将在本文后面研究几个例子。

好的测试是确定性的，它们不依赖于环境。

### 避免不必要的期望和考验

经常看到这样的测试:

```
expect(pizza).toBeDefined();
expect(pizza).toHaveAProperty('cheese', 'Mozarella'); 
```

Enter fullscreen mode Exit fullscreen mode

第一个期望是不必要的:如果没有定义`pizza`，第二个期望无论如何都会失败。Jest 中的错误消息足以理解发生了什么。

有时甚至整个测试用例都是不必要的:

```
test('error modal is visible', () => {});
test('error modal has an error message', () => {}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们知道错误模式中的错误消息是可见的，我们就可以确定模式本身也是可见的。所以我们可以安全地删除第一个测试。

好的测试没有任何不必要的期望或测试用例。

### 不要追求 100%的代码覆盖率

完整的测试覆盖在理论上听起来是个好主意，但是实际上[并不奏效](https://ordepdev.me/posts/code-coverage)。

努力达到高测试覆盖率有几个问题:

*   高测试覆盖率给你一种虚假的安全感。“被覆盖的代码”意味着代码在测试运行期间被执行，但这并不意味着测试实际上验证了这些代码做了什么。低于 100%的测试覆盖率，你可以确定你没有测试某些代码，但是即使有 100%的覆盖率，你也不能确定你测试了所有的东西。
*   有些功能很难测试，比如在浏览器中上传文件或者拖放。你开始嘲笑或访问组件内部，所以你的测试不再像你的用户使用你的应用程序，并且很难维护。最终，你开始花更多的时间编写不太有用的测试——所谓的收益递减问题。

根据我的经验，100%的测试覆盖率在两种情况下是有用的:

*   在库中，避免现有 API 中的意外中断是至关重要的。
*   在开源项目中，大部分变更是由贡献者完成的，他们不熟悉代码库。

好的测试易于维护，并且给你信心去改变你的代码。

## 结论

我们已经讨论了编写前端测试的最重要的理论和最佳实践:

*   编写比任何其他类型的测试都多的集成测试。
*   避免测试内部构件。
*   测试应该是决定性的。
*   避免不必要的期望和测试。
*   不要追求 100%的代码覆盖率。

现在我们准备开始编写我们自己的测试。本系列接下来的两篇文章是相互交叉的，所以请随意阅读您感兴趣的那一篇，无论是酶还是反应测试库。如果您还在选择，两篇文章在开头都列出了每个库的优缺点:这将帮助您做出选择。

* * *

感谢 Joe Boyle、Kent C. Dodds、Patrick Hund、Monica Lent、Morgan Packard、Alexander Plavinski、Giorgio Polvara、Juho vepslinen。