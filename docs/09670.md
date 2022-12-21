# UI 测试自动化框架摊牌:Test cafévs night watch . js

> 原文：<https://dev.to/christinepinto/ui-test-automation-frameworks-showdown-testcafe-versus-nightwatch-js-2e8h>

## 什么是 TestCafé？

[TestCafé](https://devexpress.github.io/testcafe/) 是一个端到端的测试框架，用 JavaScript (JS)或 Typescript 编写。它不像大多数其他测试自动化框架那样利用 [Selenium](http://www.seleniumhq.org/) ，而是利用了一个 [URL 重写代理](https://github.com/DevExpress/testcafe-hammerhead)。TestCafé通过代理注入的驱动脚本来模拟用户活动。

## 什么是 Nightwatch.js？

[Nightwatch.js](https://github.com/nightwatchjs/nightwatch) 也是一个基于 Node.js 的端到端测试框架，利用了 Selenium 的 WebDriver API。

## 设置

两个框架都是用一个 npm 命令安装的:

*   npm 安装夜间监视
*   npm 安装测试咖啡馆

但是，在执行该命令后，只有 TestCafé立即可用。由于 Nightwatch.js 通过 Selenium 服务器连接到不同的浏览器，您还需要安装 Java 开发工具包(JDK)——最低版本 7——以便启用该框架的功能。

## 移动测试

js 主要用于在桌面浏览器上运行，这一点在与该框架相关的其他文档中有很高的提及。为了利用框架进行移动测试，需要对框架进行定制配置，同时还需要一个额外的库。启用这种移动测试的过程可以参考这篇[文章](http://www.shaneofalltrades.com/2016/08/26/Mobile-Web-Testing-Using-Nightwatchjs-part-1/)。

相比之下，由于使用了前面提到的代理而不是 Selenium WebDriver，TestCafé可以默认运行在移动设备上，不需要定制配置。

## 编写代码

使用 TestCafé需要 JS 知识，但这并不一定允许用户快速切换到 Selenium WebDriver 或从 Selenium web driver 切换。

用 Nightwatch.js 写代码并不复杂，可读性也很强。此外，如果使用框架 Mocha 和 Chai，这些代码将类似于开发人员为 JS 应用程序编写的 JavaScript (JS)单元测试。

通过我多年的经验，我学习并编写了以下测试自动化框架的代码:Geb & Spock (Groovy)、Nightwatch.js (Node.js)、 [Menta](https://github.com/fbrnc/Menta) (PHP)、Selenium (Java)。虽然都使用 Selenium WebDriver，但它们都是用不同的编程语言编写的。因为它们都是基于 Selenium 的自动化框架，所以语言彼此非常相似，因为它们都使用 WebDriver API，这允许您快速地从一个框架过渡到另一个框架。这些框架的设置和配置各不相同，最明显的是完成这个初始步骤所需的时间。然而，编写代码本身在框架之间是相似的，因为 WebDriver API 用于与浏览器通信。

虽然这在考虑过渡到 Nightwatch.js 时是一个明显的优势，但这不应该阻止你选择在 TestCafé中编写代码。这只是为了引起注意，从 Selenium 切换到这个框架与仅仅在基于 Selenium WebDriver 的自动化框架之间切换是完全不同的。总之，尽管充分利用 TestCafé的功能可能需要一个学习曲线，但有许多宝贵的板载功能使编写代码更加简单快捷。

## 【等待】元素出现，元素可见，或页面“加载”

所有基于 Selenium WebDriver 的自动化框架都要求测试等待元素出现或页面加载，这对 QA 工程师编写自动化测试代码的日常工作来说是一个挑战。自动化测试被编程为点击按钮并填写指定的表格。只有当您将自动化测试编程为等待所述表单被完全加载，或者直到先前点击的按钮消失时，该测试才能工作。这些‘wait for’函数出现在大多数代码中，是使自动化测试稳定所必需的，例如，如果一个数据库的连接比正常情况多花了 10ms。

TestCafé不需要这些特定的“waitFor”函数，因为它会自动等待页面加载或更改。这改变了构建自动化测试的整体动力和结构。因此，自动化测试本身更加稳定可靠，不需要添加“等待”功能。

## 结论

如果密切关注 Selenium WebDriver 很重要，那么 Nightwatch.js 仍然是 QA 工程师应该使用的优秀框架。我的第一篇[文章](http://adventuresinqa.com/2017/09/19/nightwatch/)也讨论了这个框架的好处。然而，在发现并使用 TestCafé之后，我发现 Selenium 并不是编写自动化 UI 测试的唯一可用资源。TestCafé为 UI 测试自动化提供了一个全新的、令人兴奋的维度，任何 QA 工程师都应该利用它来进一步提高自动化测试的效率。

最初发布于 [www.pqa.ca](http://www.pqatesting.com/our_ideas/blog/u)