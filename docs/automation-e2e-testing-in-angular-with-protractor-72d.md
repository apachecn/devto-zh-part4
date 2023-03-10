# 用量角器自动检测 E2E 角

> 原文：<https://dev.to/francisca80s/automation-e2e-testing-in-angular-with-protractor-72d>

本周，我开始用量角器进行自动化测试。由于构建分支最近由于超时和其他问题而失败了很多，我将尝试找出更多并解决这个问题。

#### 什么是自动化测试。

一旦设置正确且有用，自动化测试会让生活变得更容易:)自动化测试使用软件/工具来快速测试代码。您设置测试用例，并通过工具来执行。在您编写这些案例之后，不需要进行任何人机交互测试。它可以通过测试来比较、执行和输入数据。

#### 最常见的测试及其差异。

到目前为止，我遇到的两种最常见的测试方法是单元测试和 E2E 测试。

##### 单元测试

单元测试侧重于测试单个单元。把它想象成一个函数或者一小部分代码。你模仿(伪造)服务、网络和其他资源，孤立地做所有的测试。
在 Angular Karma 和 Jasmine 框架中大多是用来编写单元测试的。

##### E2E 测试

通过 e2e 测试，你可以像用户一样浏览 GUI。点击按钮，页面，菜单，垃圾。(不在生产中)
带角度的 E2E 测试最常用的是带量角器的 Selenium web-driver。因此，为了测试你的代码，使用单元测试是很有用的。如果你想测试网站的功能使用，那么你应该试试 E2E。

#### 为什么要测试？

目的是遇到问题快。对于 E2E 测试:你也不想通过点击屏幕来测试一个按钮是否仍然工作或者一个页面是否自己加载。如果由不同的开发人员来做，那将会有大量的工作，而且不一致。

#### 设置量角器。

##### 如何安装量角器？

要安装量角器，您可以从命令行运行以下命令:
您可以通过运行以下命令来安装量角器:

```
 npm install -g protractor 

```

Then you install webdriver manager for selenium.

```
 webdriver-manager update 

```

```
 webdriver-manager start 

```

#### 建立一个测试。

首先，您需要两个文件来创建测试，一个规范文件和一个配置文件。
在配置文件中，配置规格文件的位置。它还包含您想要用来测试的浏览器，默认为 Chrome。包含 selenium 服务器地址是为了进行通信。框架也搭建好了，这是茉莉。然后它看起来像这样:

```
 // conf.js
exports.config = {
  framework: 'jasmine',
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['yourtest.spec.ts']
}

```

要建立一个测试，你必须确定一个案例。您在规格文件中构建案例。首先，您确定测试必须做什么。在这种情况下，您需要检查您的登录页面是否显示欢迎消息。

```
 describe('Landing page', () => {
let page: YourApp

beforeEach(() => {
    page = new YourApp()
    page.navigateTo('/login')
  })

 it('should display welcome message', () => {
    expect(page.containsElement('#welcome')).toBeTruthy()
    expect(page.getElementText('#welcome')).toEqual('Welcome')
  })
}) 

```

首先，您描述您想要测试的页面或元素，在本例中是登录页面。Describe 是 testsuite 和 Jasmine 语法的名称。它包含从一端到另一端的流动。其中包括“it”测试用例。然后你决定“它”需要做什么。这是 Jasmine 的一个全局函数。流程中可以有多个“it”测试用例。(describe)
在这种情况下，您需要检查登录页面是否显示了欢迎消息，因此可以按预期使用。(true，toBeThruthy)您还要检查文本是否与登录页面上的文本相同(toEqual('yourText ')

#### 异步等待。

JavaScript 是异步的，你不知道哪个脚本会先执行。量角器流程是同步的，您的步骤是按照特定的顺序执行的。单击这个，然后单击那个按钮。
量角器流写着承诺。量角器可以处理承诺，等待它完成。

async await 的语法很像 TypeScript 语法。
异步函数可以用“async”和“await”包装。
Await 用在我们要等待的行之前。

```
 describe('Landing page', () => {
let page: YourApp

beforeEach(() => {
    page = new YourApp()
    page.navigateTo('/login')
  })

 it('should display welcome message',async () => {
    expect(await page.containsElement('#welcome')).toBeTruthy()
    expect(await page.getElementText('#welcome')).toEqual('Welcome')
  })
}) 

```

#### 我的构建的主要问题是什么？

我的测试因为超时失败了很多次。我使用了异步 await，仍然有一些问题。如您所见，语法与 TypeScript async/await 几乎相同。

我在 protractortest.org 网站上找到了这个。

不要忘记关闭 control_flow，不能混合使用 async/await 和控制流:async/await 会导致控制流变得不可靠(参见 github 问题)。因此，如果您在规范中的任何地方异步/等待，您应该使用 SELENIUM_PROMISE_MANAGER: false

我将它添加到配置中，并调试了遗留的错误。我在一些测试之间建立了一些等待/睡眠时间，它运行得很好。