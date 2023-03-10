# 如何使用 Selenium、Mocha 和 Javascript 开始自动化测试

> 原文：<https://dev.to/vizushu/how-to-start-with-automation-testing-using-selenium-mocha-and-javascript-483c>

### 什么是自动化测试？

自动化测试是一种软件测试技术，用来测试和比较实际结果和预期结果。这可以通过编写测试脚本或使用任何自动化测试工具(如 Selenium)来实现！。测试自动化用于使测试过程更快，并检查其他难以手动执行的测试任务。

### 为什么要使用自动化测试？

因为我们是人，我们只有有限的精力和注意力去关注重复性的任务，所以我们会犯错。在软件数百万个功能中，不漏掉一个测试几乎是不可能的。

但是我们仍然希望使用人工测试，因为计算机不像人类那样有良好的判断力。自动化测试脚本不会发现视觉、听觉和人类感官的问题。

当测试人员像用户一样与软件交互时，人类测试人员可以快速识别什么时候看起来“不正常”,他们能够发现可用性问题和用户界面故障。自动化测试脚本不能测试这些东西。

并不是所有的事情都可以用自动化来完成，但是如果你能自动化那些重复无聊的任务或者测试，那就太好了。

[![](img/bd899b212b6a8c8825e263abc6ae8e90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YoNx0QFU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.makeagif.com/media/7-25-2019/-NUURV.gif)

### 硒是什么？

Selenium 跨不同浏览器和平台自动运行 web 应用程序。就是这样！T3】

Selenium 非常棒，它可以与许多不同的语言集成，今天我将向您展示如何用 JavaScript 实现这一点。这是一个正在进行的项目，我计划在未来的博客中更新更多的例子或测试案例。

### 摩卡是什么？

Mocha 是 Node.js 的测试库，是一个简单、可扩展、快速的测试套件。它是开发人员首选的测试框架。

1.  **安装**

您需要在机器上安装 Yarn 或 Node。在这个项目中，我将使用 Node 来安装软件包和运行脚本。首先，我们需要开始一个项目

`yarn init or npm init`

然后安装依赖项

`npm add chromedriver selenium-webdriver`
T1】

1.  **入门**

在那之后，我们可以开始编写我们的测试用例

[![](img/ecadc16751333e993ccbe61d0e98ad58.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ytBoxDIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6rfhjo3q046kcg5sbxco.png)

1.  **运行测试用例**

每当您想要运行测试时，只需使用以下命令:

`npm run test`

以下结果表明测试已成功完成。

`vizushu$ npm run test`
`npm run v1.9.4`
`$ mocha --recursive --timeout 10000 test.js`
`Checkout Google.com`
`✓ Search on Google (2058ms)`

`Done in 3.79s.`

这就是你开始自动化测试所需要的一切！我将在下一篇博客中继续介绍更多的测试案例，以及如何为您的自动化测试获得类似 xpath()、Css()的选择器！

### 测试愉快！

[![](img/d4fe87cd67f3c54c3992d6c271140369.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D4ZLJlmT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.giphy.com/media/gG6OcTSRWaSis/giphy.gif)