# Cypress.io:硒杀手

> 原文：<https://dev.to/bnevilleoneill/cypress-io-the-selenium-killer-3568>

[![](img/bc47221de6df21a36bbe57685566540c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YKRCJX-q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/emb78j90dhzr1l7ihvd2.png)

## 简介

在我开始之前，我想强调的是，这篇文章不是关于一个特定的项目或者我曾经工作过的任何自动化测试人员。我在最近的三个项目中看到了这种行为，几乎每个和我一起工作过的自动化测试人员都竭尽全力让这台有故障的机器工作。

我相当肯定的是，我最近参与的每个合同都有一份备忘录，规定需要一百万次自动化测试才能保证成功。我们不能停下来质疑这些测试的价值。我们必须像保护我们的孩子一样保护他们。

这些测试必须用 [selenium](https://www.seleniumhq.org/) 编写，尽管几乎每个人都有过相当糟糕的经历，原因是我将在后面陈述的固有的已知问题。

编写 Selenium 测试具有极大的挑战性，但是我们不会因此而止步不前，相反，我们会让那些编程较晚的测试人员或开发新手来编写这些困难的测试。

Selenium 测试可能很难编写，但是它们很容易复制和粘贴，这当然会导致各种各样的问题。

我们经常听到“如果它动了，就给它写个硒测试”。必须为 API、前端、后端、中端、快乐路径、悲伤路径、颠倒路径等编写自动化测试。

我们没有时间进行手工测试，怎么可能呢？我们要编写和维护所有这些古怪的硒测试。这个冲刺我们已经迟到了，每个故事都要有自动化测试。

经过一年左右的时间和一个疯狂漫长的建设，我们会认为这是有点傻，并删除它们，或者更糟的是，重新开始。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

### 为什么尽管有先天的问题，大家还是用硒？

如果我能回答上面的问题，我想我会更接近理解我们存在的真正本质，但玩笑归玩笑，为什么硒的使用如此广泛？这确实让我感到震惊，但这里有一些建议:

*   这是行业标准，有大量的在线资源和庞大的社区可以依靠
*   它可以跨多个操作系统和多种语言工作，您选择的语言和平台很可能包括在内
*   跨浏览器测试。Selenium [支持所有主流浏览器](https://www.seleniumhq.org/about/platforms.jsp),所以你可以在 Chrome、Firefox、Safari、IE、Edge 等等上面进行测试

公平地说，编写一百万个验收测试的突然激增并不是 selenium 的错。在我看来，自动化测试的正确数量是*一个*快乐路径测试，没有悲伤路径或颠倒路径。这个测试是一个冒烟测试，以确保我们的系统对业务开放。

单元测试和集成测试运行、实现和维护起来更便宜，应该是我们测试的主体，难道每个人都忘记了[测试金字塔](https://martinfowler.com/bliki/TestPyramid.html)了吗？

## 硒不符合目的，原因如下

硒的问题可以用一个词来表达，*时机*。

在我们开始编写代码来断言我们的测试是正确的之前，我们需要确保我们需要与之交互的任何元素都是可见的，并且处于接受模拟输入的状态。远程 API 调用需要解决，动画和微调需要结束。现在构成我们应用程序大部分的动态内容需要从 API 调用的当前检索数据中完成渲染。

那么，当这种可怕的异步哑剧发生时，我们该做些什么呢？我们如何阻止我们的测试刚刚完成或触底，因为一个特定的文本输入被禁用，直到一个 API 调用已经完成或一个美丽的 SVG 旋转覆盖已经把我们的虚拟世界的黑暗面纱？

通俗地说，我们等待 HTML 元素处于就绪状态，在 selenium speak 中，我们编写许多定制的`waitForXXXXX`代码助手，例如

`waitForTheFullMoonAndTheWereWolvesHaveFinishedEating`或者更现实一点……

```
wait.until(ExpectedConditions.visibilityOfElementLocated(By.xpath("//input[@id='text3']"))); 
```

Enter fullscreen mode Exit fullscreen mode

最糟糕的罪行之一就是使用`Thread.sleep`。这是一个令人发指的罪行，从空气中随机抽取一个数字，作为我们认为 UI 处于就绪状态时的胡乱猜测。请不要这样做。

以下是我一直以来最喜欢的 selenium 异常，是我在艰难地阅读 CI 构建报告时发现的:

*   继续前进，你不会在这里找到你的输入
*   这种厚颜无耻的行为意味着你已经非常接近了，但是还不够接近，它就在大教堂里，但是你却什么也做不了
*   `StaleElementReferenceException`–元素结束了一天的工作，去了酒吧。请明天再试一次
*   你可以等到时间的尽头，无论你想做什么都不会发生。你刚刚掷出了一个 7

## 看哪雪花

我经历过的最令人心碎的时刻之一是，由于自动化测试失败而导致构建失败，只是通过再次运行构建而神奇地通过。这种现象或僵尸自动化测试通常被称为*一个薄片*。*薄片*的主要问题是[的不确定性](https://martinfowler.com/articles/nonDeterminism.html)，这意味着在不同的时间用相同的输入执行一个测试会表现出不同的行为。随着非确定性测试数量的增加，您可以看到对回归测试的信心化为乌有。

一个古怪的测试很可能是由于计时、延迟和可怕的异步操作，我们正试图用我们的`Thread.sleep`和`waitForAHero`助手来驯服它们，我们需要继续写以保持理智。

试想一下，如果我们能够以某种方式让所有这些异步编程消失，如果我们的世界开始以线性或同步方式运行，这将会变得多么容易。我们将有一个多么自然的世界来测试。

Cypress.io 正着手这么做。

## cypress . io——机器中的幽灵

[cypress.io](https://www.cypress.io/) 和 [selenium](https://www.seleniumhq.org/) 的主要区别之一是 selenium 在我们测试的浏览器或设备之外的进程中执行。Cypress 在浏览器中执行，并在与被测设备相同的运行循环中执行。

Cypress 在浏览器内部执行绝大多数命令，因此没有网络延迟。命令尽可能快地运行并驱动应用程序进行渲染。为了处理具有复杂 UI 的现代 JavaScript 框架，您使用断言来告诉 Cypress 您的应用程序的期望状态是什么。

Cypress 会自动等待您的应用程序达到这个状态，然后再继续。您完全不必担心手动等待或重试。Cypress 会自动等待元素的存在，并且永远不会产生已经从 DOM 中分离出来的陈旧元素。

这是主要的收获，cypress 通过在与设备相同的运行循环中执行，消除了 selenium 的主要问题。Cypress 负责等待 DOM 元素出现。我再说一遍，赛普拉斯会处理好所有等待的事情。没有`Thread.sleep`，没有`waitForTheMoon`帮手。你不明白这意味着什么吗？

要知道这有多好，你得经历过痛苦。

下面是一些柏树测试的例子。他们缺席的同义词是任何时机或淫秽的`waitFor`帮手:

```
context("Login", () => {
  beforeEach(() => {
    cy.visit("localhost:8080/login");
  });

  it("can find and type in email", () => {
    cy.get("#email")
      .type("fake@email.com")
      .should("have.value", "fake@email.com");
  });

  it("can find and type in password", () => {
    cy.get("#password")
      .type("fakepassword")
      .should("have.value", "fakepassword");
  });

  it("will fail when type invalid user credentials", () => {
    cy.get("#email").type("fake@email.com");

    cy.get("#password").type("fakepassword");

    cy.get("input[type=submit]").click();

    cy.get("#login-message").should("have.text", "Login failed");
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这些测试；它们清楚地陈述了自己的目的，并且不会被弥补平台局限性的代码混淆。

下面是我写的一些测试，通过 cypress:
运行 [axe 辅助工具](https://www.deque.com/axe/)

```
import { AxeConfig } from "../support/axeConfig";

describe("Axe violations", () => {
  beforeEach(() => {
    cy.visit("/");
    cy.injectAxe();
  });

  it("home page should have no axe violations", () => {
    cy.configureAxe(AxeConfig);
    cy.checkA11yAndReportViolations();
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里是一个使用`webdriver` :
的类似测试

```
// in e2e/home.test.js
import assert from 'assert';
import { By, until } from 'selenium-webdriver';
import {
    getDriver,
    analyzeAccessibility,
} from './helpers';

describe('Home page', () => {
    let driver;

    before(() => {
        driver = getDriver();
    });

    it('has no accessibility issues', async () => {
        await driver.get(`http://localhost:3000`);

        // The dreaded wait until.  Abandon hope
        await driver.wait(until.elementLocated(By.css('h1')));

        const results = await analyzeAccessibility();
        assert.equal(results.violations.length, 0);
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，主要的显著区别和令人担忧的事情是延迟，有两个`await`调用和可怕的`wait(until.elementLocated)`。这是一个简单的测试，但是你的互动越多，你就需要越多的`waitFor`助手，并且这种剥落开始蔓延。

## 一路 JavaScript 下来

Cypress 显然是针对前端开发人员的，安装 cypress 是一件轻而易举的事情，可以通过您最喜欢的包管理器选择 npm 或 yarn 来完成。

```
npm install cypress --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

这真的再简单不过了。与下载 chrome webdriver 和 selenium 世界中的朋友相比。

没有 selenium 这样的多语言支持。你可以有任何你喜欢的编程语言，只要是 JavaScript 或者 TypeScript。

## 柏弊

当然，也有缺点，其中一些是显而易见的，所以我不能不列举这些。

*   柏树是相对较新的物种，它没有硒那样庞大的群落
*   没有跨浏览器测试，这是一个巨大的问题，在它被解决之前，将会导致更少的采用
*   如前所述，要么是 JavaScript，要么是 bust。你不会用 C#和 java 这些陈旧的静态语言来编写 cypress 测试
*   因为它在浏览器中运行，所以你不能支持多个标签
*   除了 [Chrome 和电子](https://docs.cypress.io/guides/guides/launching-browsers.html#Browsers)没有跨浏览器支持
*   本文写作时，有[无影子 DOM 支持](https://github.com/cypress-io/cypress/issues/144)

上述项目在某些情况下是无法克服的，也不会被克服。

## 柏树会取代硒吗？

虽然我很想说是，但我有疑虑。有一大批自动化测试人员，除了 selenium 之外，他们还不知道任何其他世界，可能很难很快离开。

## 结论

正如我在本文开始时所说的，我对自动化测试的体验并不好，大量的金钱、时间和痛苦都花在了维持数以千计的难以维持的测试上，却没有很大的回报。根据我的经验，自动化测试只能保证长时间的 CI 构建。

作为开发人员，我们需要更好地进行自动化测试。我们需要编写更少的测试来做更多有用的事情。我们把一些最难写的代码留给了一些经验最少的开发人员。我们已经让手工测试看起来过时了，在我看来，这仍然是发现真正错误的地方。

我们需要对自动化测试能够达到的目标有一个清醒的认识。

Cypress 很棒，因为它使事情同步，这消除了整个世界的痛苦，为此，我坚定地支持它。然而这并不是开绿灯写千柏考。我们的大部分测试是单元测试，在我们进行一些愉快的自动化测试之前有一层集成测试。

当然，这是一个永远不会发生的非常明智的策略。

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子 [Cypress.io:硒杀手](https://blog.logrocket.com/cypress-io-the-selenium-killer/)首先出现在[博客](https://blog.logrocket.com)上。