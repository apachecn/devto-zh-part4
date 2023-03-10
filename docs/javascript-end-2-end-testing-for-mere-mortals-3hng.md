# JavaScript End 2 结束对普通人的测试

> 原文：<https://dev.to/vladimirbag/javascript-end-2-end-testing-for-mere-mortals-3hng>

我叫弗拉基米尔，我在乌克兰生活和工作。我是一名初级的 QA 工程师，迟早我将不得不面对“测试自动化”这样的概念，因为对我来说，这是我在这个专业领域进一步发展的必要条件。在实施这一阶段的过程中，我遇到了许多问题。在我看来，对于初学者来说，最重要的问题之一是不知道从哪里开始。
这大概是每个新人在开始学习测试自动化时面临的问题。这篇文章是写给那些梦想自动化测试但不知道从哪里开始的人的。
我将向您展示一个真实的例子，展示如何使用最少的 JavaScript 知识编写第一个测试。这将是一个简单应用程序的浏览器测试。为了运行和创建测试，我将使用 CodeceptJS 框架，因为对于初学者来说，这仍然是最简单和最容易理解的测试框架。

CodeceptJS 是一个现代的“端到端”测试框架，具有特殊的 BDD 风格语法。测试被写成一个用户在网站上行为的线性场景。CodeceptJS 是一个多后端测试框架。它可以使用 Webdriverio、木偶师、量角器、梦魇来执行测试(对于我们的测试，Google Chrome 木偶师将被用作浏览器的驱动程序)。这很简单，因为你只需要描述你在网站上的行为，然后执行它们。

**第一步。Node.js 安装**
首先，安装 Node.js v.12.4.0，我已经尝试在 10.16 运行测试——没有任何反应。所以我推荐 12.4.0 版本。
**！我在本教程中使用的是 Windows 7。但它应该也能在 Windows 10、Linux 和 Mac 上运行！**

**第二步。用傀儡师**
安装 codeceptjs 运行**“node . js 命令提示符”**，在命令行输入**“NPM 安装 CodeceptJS 傀儡师”**。开始安装带有木偶师的 CodeceptJS。

`npm install codeceptjs puppeteer`

**第三步。初始化当前目录下的 CodeceptJS**
运行命令**“npx CodeceptJS init”**(运行命令初始化当前目录下的 CodeceptJS)。

`npx codeceptjs init`

**第四步。回答简单的问题**
决定默认值，当要求选择助手时——选择木偶师。当要求指定 URL 时，指定[http://jspears.github.io/subschema](http://jspears.github.io/subschema)(我选择这个应用程序是因为它简单并且使用 React。).

**第五步。在根目录**
中创建第一个测试当所有的东西都安装好后，使用命令**“npx codeceptjs gt”**创建第一个测试，并输入测试的名称。

`npx codeceptjs gt`

在您的根目录(test.js 的名称)中会生成一个文件。比如——“log in . js”

**第六步。第一个测试代码**
测试是从用户的角度编写的。有一个 actor 对象(表示为“I”)包含从 helpers
获取的动作方法，或者:表示用户的动作。一个测试被写成由一个参与者执行的一系列动作。
在你最喜欢的 javascript 编辑器中打开一个生成的文件。例如，我正在使用 Visual Studio 代码。
您的第一个测试看起来会像这样:

`Scenario ('try react app', (I) => {
I.amOnPage ('/');
// This is command to open a webpage (accepts relative or absolute url)
I.click ('Login');
// This is command to locate a button or link and click on it
pause ();
// This command is prescribed when writing or debugging a test
});`

**第七步。验证编写的代码**
使用命令**“npx codeceptjs Run-steps”**
运行测试

`npx codeceptjs run --steps`

输出应该是这样的:
打开登录页面并打印值-
试试 react app
**我在页面”/**
**我点击“登录”**

**第八步。启动一个测试浏览器**
木偶师启动一个浏览器而不打开一个窗口。要查看浏览器，编辑“codecept.json config”并为木偶师:
设置**“show:true”**值

`exports.config = {
tests: './*_test.js',
output: './output',
helpers: {
Puppeteer: {
url: 'http://jspears.github.io/subschema',
show: true,
}
},
include: {
I: './steps_file.js'
},
bootstrap: null,
mocha: {},
name: 'User'
}`

**第九步。再次运行浏览器**
，输入命令**“npx codeceptjs run-steps”**。
在屏幕上，我们有浏览器和终端窗口。在终端中，测试已经停止运行并等待我们的命令。

**第十步。接收测试数据**
当 **pause ()** 命令在我们的代码中执行时，我们可以在终端窗口中输入任何命令，并在浏览器运行时观察它们是如何执行的。
为了获得测试数据，您需要在终端窗口中输入 **"I.fillField"** 命令，同时，您也可以在打开的浏览器中观察测试的执行。
事实证明这并不困难，对于这个测试，没有必要使用像 XPath 这样的定位符，codeceptjs 可以很好地识别带有“用户名”、“密码”、“重复密码”等标签的字段。与 Selenium 不同，在 Selenium 中，您需要花费额外的时间在“定位器”的帮助下识别字段，这样我们的测试才能工作。现在，您会看到我们的字段中充满了数据。
接下来，向下滚动页面，查看我们之前以脚本形式输入的值。

**第 11 步。代码获取数据的布局**
用我们的数据复制生成的脚本，粘贴到编辑器中你的 JS 文件中(比如我的文件名是‘log in _ test . JS’)。
一些变化，它应该是这样:

`Feature (‘open login page and print value’);
Scenario (‘try react app’, (I) => {
I.amOnPage (‘/’);
I.click (‘Login’);
pause();
I.fillField (‘username’, 'test@gmail.com')
I.fillField (‘password’, ‘test’),
I.fillField (‘confirmPassword’, ‘test’)
});`

**第十二步。最终测试和验证**
现在，我们使用命令**“npx codeceptjs run-steps”**
再次运行我们的测试。在测试结束时，必须检查不仅执行了操作，而且实现了结果。
例如，您可以检查浏览器底部输入字段下的表单是否包含文本。
为此，我们在终端窗口中添加以下命令:

`I.see (‘“username”: “test@gmail.com”’)
I.see (‘“password”: “test”’);
I.see (‘“confirmPassword”: “test”’)`

这些命令被称为断言，人们认为任何测试都应该包含它们。

我们创建的测试正在运行，现在已经过验证。恭喜你。作为一名初学者，你通过 12 个简单的步骤进行了一项测试，该测试会自动填写网站上的字段并进行“认证”。这是我第一次体验测试自动化，最后，测试不再让我害怕。我会思考如何提高我在测试自动化方面的技能。我希望 CodeceptJS 能在这方面帮助我。