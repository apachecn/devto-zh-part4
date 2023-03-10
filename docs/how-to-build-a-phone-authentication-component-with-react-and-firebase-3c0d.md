# 如何用 React 和 Firebase 构建一个电话认证组件

> 原文：<https://dev.to/kris/how-to-build-a-phone-authentication-component-with-react-and-firebase-3c0d>

> 这个故事是克隆 FireBase 系列的第三部分
> 
> 第一部分:[构建可重用的 Firebase 脸书登录组件](https://blog.bitsrc.io/building-a-reusable-firebase-facebook-login-component-eaa098f58f17?source=user_profile---------2-----------------------)
> 
> 第二部分:[构建可重用的 React 登录组件](https://blog.bitsrc.io/building-a-reusable-react-login-component-cc824164ff36?source=user_profile---------1-----------------------)

在本章中，我们将继续使用 React 系列中的 FireBaseWeb-UI 克隆，并将带有 OTP 的电话认证集成到其中。为了正确理解本教程，建议浏览本教程系列的前几章。在这里，我们将使用 React 和 FirebaseWeb-UI 实现电话身份验证组件，并将代码推送到 [Bit 的云](https://bit.dev)中，在那里代码可以被其他人共享、发现和使用，甚至进行协作。

<figure>[![](img/dfb732dac86907f85aeda9b4ec291c1c.png)](https://bit.dev) 

<figcaption>一个关于比特直播操场集合的例子</figcaption>

</figure>

所以，让我们开始吧！

### 你会学到什么…？

*   如何使用[位](https://bit.dev)。
*   如何使用 Bit 和 React？
*   如何使用 [React](https://reactnavigation.org) 路由器 DOM(文档对象模型)。
*   如何使用 Firebase 电话认证？

### 要求

这里有一个插件、包和服务的完整列表，我们需要从本教程中获得一些东西:

*   Nodejs v8.x.x 或更高版本与 NPM/yarn 一起安装。
*   VScode 或者你最喜欢的 IDE。
*   [位](https://bit.dev/?source=post_page---------------------------)和 [Firebase](https://firebase.google.com) 账户(自由层)。
*   使用 create-React-app 命令对 starter 样板项目进行 react。

### 简短的总结

这里，我们总结了在 React 项目中实现电话身份验证组件要执行的步骤:

*   按照 bit 组件指南创建 React 组件。
*   为简单导航实现 React 路由器 DOM。
*   共享位上的组件。
*   从 Bit 导入组件并添加新功能，即重新发送 SMS，然后将组件推回 Bit。

### 创建反应组件

首先，我们需要在 src/components/PhoneAuth 中创建一个新组件。然后，我们需要添加 *App.css* CSS 文件，该文件是我们从本系列教程的前一部分获得的。接下来，我们需要创建三个文件，一个作为入口点的 index.js，处理电话号码配置的 InputPhone.jsx 和显示电话按钮的 *PhoneButton.jsx* ，该按钮具有导航到 ***InputPhone*** *组件*的功能。目录文件的整体结构如下所示:

[![](img/b3ec1d4dd8eedf1b4a60a6530271b8dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t0thrClw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/253/1%2Akq4wgHn08vIhodBBc8PDHQ.png)

#### 激活 Firebase 手机认证

这里，我们需要转到 firebase 控制台，然后导航到>身份验证>身份验证方法，然后激活**手机**身份验证，如下图所示:

[![](img/54b38a1f96c384d504eccaa7b22dd25a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E0Q4W_fO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1002/1%2A4Z2To5n-D7Qs2Lg_lR9ZHg.png)

现在，我们可以使用 Firebase 进行电话认证。

### 在组件上工作

在这一步中，我们将开始处理我们的身份验证组件。

#### 设置反应路由器 dom

这里，我们需要设置 **react-router-dom** ，以便导航到不同的文件。我们将使用 *index.js* 文件作为切入点。然后，我们注册一条路线，在 ***PhoneButton*** 到 ***InputPhone*** 组件之间进行导航。

首先，我们需要使用下面给出的命令安装 **react-router-dom** 包:

安装 react-router-dom

然后，我们需要通过从 react-router-dom 包导入 BrowserRouter 组件来激活主根 index.js 的 **react-router-dom** 包，如下面的代码片段所示:

这里，我们正在导入 ***BrowserRouter*** 组件，并使用它包装 ***App*** 组件。

然后，我们需要复制下面的代码，粘贴到 src/components/phone auth/index . js。

这里，我们从 react-router-dom 包中导入 ***交换机*** 和 ***路由*** 组件。

***路由*** 组件用于定义 URL 位置， ***开关*** 组件用作路由组件的包装器。

然后，我们需要为我们之前创建的两个组件定义一个路径，它们是 ***PhoneButton*** 和 ***InputPhone*** 组件。

对于 ***索引*** 路线，我们使用的是 ***PhoneButton*** 组件。

对于 ***input_phone*** 路由，我们使用的是 ***InputPhone*** 组件。

现在，我们可以在这两个组件之间导航。

#### PhoneButtton.jsx

在 ***PhoneButton.jsx*** 文件中，我们要实现一个电话按钮。然后，我们将使用该组件显示电话按钮并导航到 ***输入电话*** 组件。

要实现这一点，我们需要打开 ***PhoneButton.jsx*** 文件，并将代码粘贴到下面的代码片段中:

这里，我们使用 react-router-dom 中的 ***Link*** 组件来创建一个到 ***InputPhone*** 组件的链接。

然后，我们将组件导入 App.js 文件，并将其实现为 *render()* 函数，如下面的代码片段所示:

现在，我们需要启动服务器来查看结果屏幕。启动服务器后，我们将在屏幕上看到以下结果:

[![](img/c83d5529cd40857c3b4aca44f52d957e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8PkXRSjr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/395/1%2AlxGl-A8R_H1ORgHVU2io6Q.png)

#### InputPhone.jsx

我们要做的主要工作是在 ***InputPhone*** 组件中。我们将使用这个组件来处理发送 SMS 和验证 OTP。为此，我们需要打开 src/components/phone auth/phone input . jsx 并粘贴下面代码片段中给出的代码:

这里，上面代码片段中给出的代码用于在我们的项目中构造一个表单并初始化 firebase。

现在，如果我们重新启动服务器并测试屏幕按钮，我们将得到以下结果:

[![](img/6ad61c974a748f7aabfa96331796dafe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vbyvxQFh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/592/1%2AvgJKlujfeBIO4yafmldFFA.gif)

在这里，当我们点击用电话登录按钮，我们将得到一个表格和验证按钮，以验证与 firebase。

#### 获取表单值

在这一步中，我们需要获取表单值并将其设置为内部状态变量。

这里，我们需要创建一个名为 phone_number 的内部状态，然后用 this.state.value 将它附加到输入字段，以设置状态的值。

然后，我们需要创建一个名为***handle phone change()***的新函数，以便从输入字段中获取值，当 **onChange** 事件被触发时，我们为状态变量设置一个新值。

#### 用 Firebase 发送短信

接下来，我们将使用 firebase 实现“发送短信”功能。这里，在实现发送短信功能之前，我们需要安装 **Recaptcha** firebase 来防止欺诈和垃圾邮件。

为此，我们需要创建 componentDidmount 并粘贴下面代码片段中给出的代码:

这里我们需要设置名为 *Recaptcha-container* 的 CSS id 来放置 Recaptcha 并定义大小，回调函数有成功和错误。然后，我们需要在目标上渲染，最后将对象分配给窗口对象，使其成为全局对象，我们可以在函数中使用该对象。

然后，我们需要在想要呈现 Recaptcha 的位置放置一个新的元素:

```
<div id="recaptcha-container" /> 
```

因此，我们的表单将如下面的模拟器模拟所示:

[![](img/4a4b67cc6d15c5b9a918dd4c0878dc11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GMNF4W3r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/401/1%2AihYp7fNwxo9yUAokTCzauw.png)

为了更好地运行，我们需要禁用 VERIFYbutton，直到 **Recaptcha** 被成功验证。我们可以使用下面的代码来做到这一点:

这里，我们需要创建一个名为 isButtonDisabled 的新状态，并用 **Recaptcha** 回调来切换该状态。然后，我们需要将 isButtonDisabled 状态放在 Disabled 事件中的按钮上，如下面的代码片段所示:

因此，在 Recaptcha 通过验证之前,“验证”按钮将保持禁用状态，如下文仿真器仿真所示:

[![](img/6f2367898a3a25d4bf30be9805a6fdc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--05IkT2ec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/446/1%2AcQ3A6-d2JXzo1EcjA4mKdw.gif)

现在，我们有了火基需要的 **Recaptcha** 。

接下来，我们需要实现电话认证。

为此，首先，我们需要创建一个名为 handleLogin()的新函数，如下面的代码片段所示:

这里，我们需要获取 ReCaptcha 对象并调用 signInWithPhoneNumber，然后传递两个必需的参数 phone_number 和 Recaptcha 对象，即 appVerifier。成功后，我们需要打印结果，即构象结果。

#### 提交表单

接下来，我们需要实现提交表单的功能。但问题是，在我们点击提交按钮并提交表单后，页面会重新加载。为了防止表单重载，我们添加了函数名 handleSubmit()并将函数添加到

element wrapping the button we implemented before. The code for this is given in the code snippet below:

接下来，为了触发 handleLogin 函数，我们需要添加 onClick 事件来验证按钮。现在，我们需要测试它的功能。我们可以在下面的模拟中看到测试按钮的结果:

[![](img/53d90455f277d7c153dea859c93f4d60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3qPxp0h1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaYXmGxiwWoTscc__BUmzAg.gif)

在这里，我们收到了一个成功的结果。

接下来，我们需要实现验证过程。

### 验证 OTP

这里，我们需要使用 OTP 来验证身份验证。首先，我们需要将一个结果对象存储到窗口对象中，这使得它是全局的，我们可以跨函数使用该对象。然后，我们需要将一个状态变量设置为 sendOTP，如下面的代码片段所示:

这里，为了验证 OTP，我们需要在同一个组件中创建另一个表单，并使用 sendOTP 状态切换隐藏和显示。

对于验证过程，我们需要在发送短信的过程中做同样的事情。首先，我们需要创建两个新的状态变量。为此，我们可以复制下面代码片段中给出的代码，并粘贴到 src/components/phone auth/phone input . jsx。

这里，sendOTP 状态用于在“*输入电话号码”*表单和“*验证短信”*表单之间切换。sendOTP 状态的默认值为 false。

otp 状态用于处理我们从表单中获取的 OTP 数据。

handleOTPChange()函数用于获取表单值。

handleOTPCheck 函数用于检查来自我们之前提到的 handleLogin 函数的结果的 OTP 对象。然后，我们从 state 传递 OTP 值，并在控制台中显示结果。

接下来，我们使用 sendOTP 状态在 **PhoneInput** 表单和 **OTP verify** 表单之间切换显示。然后，我们需要将之前创建的函数添加到表单和按钮中，这与我们在 **PhoneInput** 表单中所做的相同。

现在，我们已经配置好了一切。接下来，我们需要检查一切是否工作正常。

[![](img/b7fff37748286f4a5e1c3f8b4b73ce76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JWp-bgzO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVuynQ4GjoZuKLbjPETX8kQ.gif)

正如我们所看到的，我们可以在浏览器控制台中得到预期的结果。接下来，我们还需要在 Firebase 控制台中检查结果。

[![](img/d0582de476ff64c51a5e4861751b04ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LuzasPtb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1012/1%2AEVpaYyi-xP5IPAutUUzhJQ.png)

正如我们在上面的截图中看到的，我们在 firebase 控制台中也获得了预期的结果。

最后，我们在 React 中成功实现了 Firebase 电话认证组件。

接下来，我们需要将我们的身份验证组件推到位。

### 使其可重复使用并与 Bit 共享

[共享可重用代码组件作为团队成员](https://bit.dev)

为了使我们的代码可重用和可共享，我们将进行一些重组，并将所有代码移动到“组件”中(这不是强制性的，但在使用[位](https://bit.dev/?source=post_page---------------------------)时，这是一种更好的做法)。然后，我们将它(及其所有依赖项)导出到 [Bit 的 cloud](https://bit.dev) 上的一个组件集合中，以便与其他人共享和轻松重用。

在这一步，我们将把新组件，即**电话认证组件**推送到 Bit。我们已经安装了我们的 Bit 包并连接到我们的 Bit 帐户。因此，我们需要创建一个集合，并从跟踪组件开始。

注意:第一步和第二步是安装 Bit account。如果您已经有一个 Bit 帐户，可以跳过这些步骤。

### 1。在项目上创建一个集合并初始化 Bit

在这里，我们需要在 Bit 上创建一个新的集合，我们要将所有的组件代码放入其中。我们使用 Bit“create collection”配置页面创建一个名为“firebase-auth-collection”的新集合。

首先，我们需要安装 Bit CLI

> *使用 NPM，*
> 
> *npm 安装位-bin -全局*

然后，我们需要转到 Bit CLI 中的项目目录，并在 Bit CLI 上执行 bit init 和 run bit login 命令

### 2。为组件配置一个 React 编译器

当我们配置一个编译器时，我们告诉 Bit 用它封装组件。Capsule 组件及其编译器让我们可以在任何地方自由地使用、构建和测试它们。这包括能够在我们希望使用该组件的任何应用程序中运行代码，以及在云中运行代码以支持诸如 [live component playground](https://bit.dev/davidhu2000/react-spinners/pacman-loader?source=post_page---------------------------) 之类的功能。

```
bit import bit.envs/compilers/react --compiler 
```

### 3。轨道组件

我们需要在您的命令提示符或 Bit 控制台中使用以下命令来跟踪我们的组件:

位添加 src/components/PhoneAuth

### 4。标记并导出到我们在 Bit 云上的收藏

这里，我们将为所有被跟踪的组件设置一个版本，并使用下面的位命令导出到这个集合:

位标签-全 0.0.1

最后，我们需要执行下面的位命令，将组件代码推送到我们之前创建的位集合:

```
bit export krissnawat.firebase-auth-collection 
```

现在，我们的软件包在[位](https://bit.dev/krissnawat/firebase-auth-collection/phone-auth)上运行。

### 使用位组件

要使用位组件，我们需要使用下面代码片段中给出的位导入命令导入位组件:

这里，我们需要打开另一个项目并使用命令:

```
bit import krissnawat.firebase-auth-collection/phone-auth --path src/components/PhoneAuth 
```

现在，所有导入的文件都应该放在组件目录中，如下所示:

[![](img/d74ab533d31f295da83b250bc26157ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--skinoHfD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/307/1%2AXLd67OUj_6hvhKEqxIMoMQ.png)

现在，我们可以使用从 Bit 成功导入的组件了。

### 更新组件并推回位

在本节中，我们将了解如何更新元件代码并创建一个新的位元件，该元件具有 20 秒后重新发送 OTP 的功能。

首先，我们需要使用以下命令安装 react countdown 包:

纱线添加反应-倒计时-现在

然后，我们需要创建一个带倒计时的条件渲染器函数。

然后，我们需要包含组件来验证表单页脚。

因此，我们将在屏幕上看到以下模拟:

[![](img/64ba1d35fcf42d393f9aa0987802e7fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lNheUy24--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/423/1%2ArOpGW7qx_mtWbo_BTx1umA.gif)

现在，一切都完成了。接下来，我们需要使用命令:
将新组件推回到 Bit

```
bit export krissnawat.test-collection 
```

现在，如我们所见，我们的组件在 Bit 上处于活动状态，如下所示:

[![](img/7e6e5c8e8fa058a525f94ba6fbb0a0c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U3afwd_X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AvH1fdgnqRO1ZsrEI2Hv7GA.png)

### 重述

在本教程中，我们学习了如何使用 react-router-dom 和 Recaptcha 在 firebase 中实现 firebase 电话认证。我们还学习了如何将 Bit 用于可重用组件，并最终实现了倒计时定时器。在下一章，我们将实现 Firebase 电子邮件链接认证。

### 相关故事

*   [在 React 中加快开发的 5 种工具](https://blog.bitsrc.io/5-tools-for-faster-development-in-react-676f134050f2)
*   [11 2019 年 React UI 组件游乐场](https://blog.bitsrc.io/11-react-ui-component-playgrounds-for-2018-eef5a87a1bf8)
*   [9 React 库和工具来掌握你的组件工作流程](https://blog.bitsrc.io/9-tools-and-libraries-to-boost-your-react-component-workflow-6ff4b49511c2)

### 用 Bit 封装组件，以便在项目和应用程序中的任何地方运行它们

[https://www.youtube.com/embed/E5lgoz6-nfs](https://www.youtube.com/embed/E5lgoz6-nfs)

[**Bit**](https://bit.dev?utm_medium=content&utm_source=bitsandpieces&utm_content=6&utm_campaign=bottom) 封装项目中的组件及其所有文件和依赖项，因此它们可以在应用程序中的任何地方运行。

通过使您的组件开箱即可重用来加快构建速度，并作为一个团队协作来共享和发现组件。无需重构或配置，只需共享组件并构建真正的模块化应用。

#### [了解更多](https://bit.dev?utm_medium=content&utm_source=bitsandpieces&utm_content=6&utm_campaign=bottom)

* * *