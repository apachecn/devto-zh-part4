# 创建可重用的 React 本机 OTP SMS 组件

> 原文：<https://dev.to/kris/create-a-reusable-react-native-otp-sms-component-3bpk>

#### 了解如何创建用于短信验证的 React 原生组件，并使其可重用、可共享。

在本教程中，我们将学习如何创建一个用于 SMS 验证的 React 本地组件，并使用 Bit 使其可共享。OTP 短信验证是在注册时通过发送验证码(OTP)来验证用户手机号码的过程。该验证方法检查移动电话号码的存在以及用户是否可以访问该移动电话号码。

这里，对于 OTP 短信验证的例子，我们将创建两个屏幕:第一个是电话输入，第二个是 OTP 检查。

那么，让我们开始我们的教程:

### 你会学到什么…？

*   如何使用[位](https://bit.dev/)。
*   如何将 Bit 与 React Native 一起使用？
*   如何使用 [React 导航](https://reactnavigation.org)。
*   如何使用 REST API？

### 问题陈述

如今，每个应用程序都使用双因素认证，第一个因素是电子邮件，第二个因素是发送到你手机的短信。当我们仅使用 SMS(即文本消息)作为唯一的认证形式时，问题可能会出现。这也可能导致用户的帐户被一个未知的团体所包含，该团体可以访问保存在一个人的帐户中的所有用户的详细信息，甚至以你的名义登录该帐户，等等。

### 要求

这里有一个插件、包和服务的完整列表，我们需要从本教程中获得一些东西:

*   Nodejs v8.x.x 或更高版本与 NPM/yarn 一起安装。
*   Android 或 iOS 模拟器或硬件设备。
*   一个首发[项目](https://github.com/krissnawat/pubnub-react-native-chat/)。
*   [位](https://bit.dev/)和[环验证码](http://my.ringcaptcha.com/docs/api#phone-verification?utm_source=devto-kris&utm_dev.to=article&utm_campaign=react-native-sms-verification)账户(自由层)。
*   Ringcaptcha app_key 和 api_key。

### 简短的总结

这里，我们总结了在 React 项目中实现 OTP 验证组件要执行的步骤:

*   按照 bit 组件指南创建 react-native 组件。
*   使它们与 RingCaptcha REST API 一起工作。
*   共享位上的组件。

### 创建 react-native 组件

在这一步中，我们需要首先克隆这个 Github [repo](https://github.com/krissnawat/pubnub-react-native-chat/) ，并签出到前面提到的[分支](https://github.com/krissnawat/pubnub-react-native-chat/blob/before_use_auth0)。然后，我们将开始我们的项目。您可以使用 git 命令将 starter 样板工程克隆到您的本地目录:

```
git clone [https://github.com/krissnawat/pubnub-react-native-chat](https://github.com/krissnawat/pubnub-react-native-chat/) 
```

为了检查 starter 分支，我们将在 git 控制台中使用以下命令:

```
git checkout before\_use\_auth0 
```

现在，我们有了 stater React 原生样板项目，如下面的 GitHub 克隆完成截图所示:

[![](img/8c971702195797fb587cd9ec927507a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8zptXpv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/938/1%2AFzPUPOOrjlwPd5LAiDQ1ZQ.png)

然后，我们需要安装 package.json 文件中的包，并使用以下命令运行 ios simulator】

```
[yarn](https://github.com/krissnawat/pubnub-react-native-chat/blob/before_use_auth0) //to install all the packages in out package.json file

[react-native run-ios](https://github.com/krissnawat/pubnub-react-native-chat/blob/before_use_auth0) //to run ios simulator 
```

接下来，我们需要创建一个名为 **OTP** 的组件，并创建所需的文件结构，如下图所示:

[![](img/7dd44d6dc2f99d7a621fc0c5cf1482ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FHPpGw8f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/355/1%2ASV_bhqQNtUMGtiUDf95xsA.png)

然后，我们需要使用 *PhoneInput.js* 文件来实现电话号码的输入，使用 *OTPChecking.js* 文件来检查 OTP。然后，我们需要使用 *index.js* 文件作为组件条目。在这里，我们需要在本章中工作的所有三个文件都在下面详细解释:

#### PhoneInput.js

在这个文件中，首先，我们需要导入所有样板类，创建一个简单的表单来接受电话号码，并添加一些样式来使表单看起来更好，如下面的代码片段所示:

然后，重新加载模拟器，我们可以看到以下结果:

[![](img/3e85f7dd8fa16741d84e4dd3a14667aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5IDzAyvo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/476/1%2A1g3b-tRFH8_N_5YPWt0XXQ.png)

接下来，我们需要获取输入值并将其赋给状态变量，如下面的代码片段所示:

然后，我们需要创建一个函数来处理来自 *RingCaptcha* 服务的 OTP 请求。在这里，我们将使用 Rest API，并且非常容易实现。下面的代码片段显示了该函数的实现:

这里，我们需要使用 *fetch* 方法来发送 POST 请求，并附上两个必需的参数 *phone_number* 和 *API key。*然后，我们需要显示一些通知，并重定向到 OTP 检查页面，发送 *phone_number* 作为参数，如上面的代码片段所示。

#### OTPChecking.js

在这一步中，我们将实现 OTP 检查。为了检查 OTP，我们需要从 *PhoneInput.js* 中复制代码，粘贴到 *OTPChecking.js* 文件中，然后更新。

首先，我们需要更新表单和一些样式，如下面的代码片段所示:

然后，我们重新加载模拟器，并看到以下结果:

[![](img/fb59d29b8d78d2f33d09d790bdc8c25b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yI7kUBFK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/476/1%2ASGCSq9-U_1pZN3lfslJjyg.png)

其次，我们需要更新用于验证 OTP 的函数。下面的代码片段提供了该函数的代码:

这里，我们从 parameter 接收到一个 *phone_number* ，从用户接收到 *OTP 代码*。验证成功后，我们需要将用户重定向到主页面。

#### Index.js

在这个文件中，我们将实现一个包含所有屏幕和导航结构的组件。在这里，我们实现了入口组件，包含了所有屏幕，并将其附加到导航堆栈，如下面的代码片段所示。

这里，我们需要从 *react-navigation* 包中导入我们之前创建的组件文件以及一个*createstacknaviator*模块。然后，我们需要将组件集成到导航器堆栈中，如上面的代码片段所示。

最后，我们需要在 *App.js* 文件中导入 OTP 组件，并将其包含到 *StackNavigator* 中，如下面的代码片段所示:

然后，我们需要更新到登录屏幕的重定向。然后，我们需要再次刷新模拟器，测试它是否正常工作:

[![](img/6021e22fac0c0de265fc1fd2b0f2d7e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C6zJoz0x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/364/1%2AXNdqeJjaF-lfU0gTrps0tA.gif)

因此，我们可以看到它像预期的那样工作。

下一步，我们需要制作一个组件来发布到 Bit 上。

### 利用。应用程序环境

在这最后一步，我们需要处理组件结构并使其可维护。重要的是它的 API 密钥，我们不能将凭证暴露给互联网，但是 React native 有一个包可以帮助我们做到这一点。我们需要使用下面的命令安装***react-native-dotenv***包:

```
npm install react-native-dotenv --save-dev 
```

之后，我们安装软件包。我们需要创建一个. env 文件并将所有凭证移动到该文件中。示例如下:

```
APP\_KEY=xxxxxxxxx
API\_KEY=xxxxxxxxx 
```

接下来，我们需要将包导入到我们需要的文件中，并使用来自*的凭证变量。env* 文件。 *APP_KEY* 和 *API_KEY* 是从我们之前设置的包中导入的。

```
import { APP\_KEY, API\_KEY } from "react-native-dotenv"; 
```

然后，我们需要用从包中导入的变量替换编码的变量，如下面的代码片段所示:

然后，我们需要对 *OTPChecking.js* 文件重复同样的过程。

现在，我们需要将我们的组件共享给 Bit。

### 使其可重复使用并与 Bit 共享

[共享可重用代码组件作为团队成员](https://bit.dev)

为了使我们的代码可重用和可共享，我们将进行一些重组，并将所有代码移动到“组件”中(这不是强制性的，但在使用[位](https://bit.dev/)时，这是一种更好的做法)。然后，我们将它(及其所有依赖项)导出到 [Bit 的 cloud](https://bit.dev/components) 上的一个组件集合中，以便与其他人共享和轻松重用。

在此步骤中，我们将把新组件(即 OTP 组件)推入 Bit。我们已经安装了我们的 Bit 包并连接到我们的 Bit 帐户。因此，我们需要创建一个集合，并从跟踪组件开始。

注意:第一步和第二步是安装 Bit account。如果您已经有一个 Bit 帐户，可以跳过这些步骤。

#### 1。在项目上创建一个集合并初始化 Bit

这里，我们在 Bit 上创建一个新的集合，我们将把所有的组件代码放入这个集合。我们使用 Bit“创建集合”配置页面创建一个名为“my-react-native-collection”的新集合，如下图所示:

[![](img/92a5af110d064199f4b885c8c7b6f391.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IEBcs-5D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/577/1%2ApaXS6yhzQ-rJkDaRoiUsmw.png)

为了成功地将整个项目推进到 Bit，您需要遵循以下页面上给出的说明:

[![](img/c9201a6bcc5ecc7c971912b487d57047.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_aF5aFkM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AL9txPgcBLfcb65Pr578i8A.png)

首先，我们需要安装 Bit CLI

> *使用 NPM，*
> 
> *npm 安装位-bin -全局*

然后，我们需要在 Bit CLI 中转至项目目录，并在 Bit CLI 上执行 bit init 和 run bit login 命令，如下图所示:

[![](img/ff54380dfc677fb5dddf6e29ec5c9cb3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZeQQ_wL6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/792/1%2A4RXs1K0qpV_CHV4AoUNWOg.png)

#### 2。为组件配置一个 React 编译器

当我们配置一个编译器时，我们告诉 Bit 用它封装组件。将组件和它们的编译器封装在一起给了我们在任何地方使用、构建和测试它们的自由。这包括能够在我们希望使用该组件的任何应用程序中运行代码，以及在云中运行代码以支持诸如 [live component playground](https://bit.dev/davidhu2000/react-spinners/pacman-loader) 之类的功能。

```
bit import bit.envs/compilers/react --compiler 
```

#### 3。轨道组件

我们需要在您的命令提示符或 Bit 控制台中使用以下命令来跟踪我们的组件:

位添加 src/元件/OTP

执行命令后的结果显示在下面的屏幕截图中:

#### 4。标记并导出到我们在 Bit 云上的收藏

这里，我们将为所有被跟踪的组件设置一个版本，并使用下面的位命令导出到这个集合:

位标签-所有 1.0.0

最后，我们需要执行下面的位命令，将组件代码推送到我们之前创建的位集合:

```
bit export krissnawat.my-react-native-collection 
```

现在，我们的软件包在[位](https://bit.dev/krissnawat/firebase-auth-collection/google-auth)上运行。这是我们的 react-native 集合中的第一个组件，如下图所示:

[![](img/f04f497fc11922dfd6b7199b9dd433c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L3932Fwm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/30/0%2AuFPCZlcE4OfEYDhL)

[![](img/47ebfff8feeb6d7a088f908855ad80e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l8NyJBqI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/991/1%2ArTzKIkDUlv6H04NFs47fug.png)

最后，我们需要替换我们的 _App.js _file 中的组件。

但是首先，我们需要使用下面的命令从位注册表安装我们的组件:

```
npm i @bit/krissnawat.my-react-native-collection.otp 
```

然后，我们需要替换 App.js 上的代码，如下面的代码片段所示:

最后，用 RingCaptcha 实现了一个简单的 OTP 组件，并将该组件上传到 Bit。现在，我们可以在本地运行项目来测试一切。

### 结论

在本教程中，我们学习了如何实现 React 本机 OTP 组件。我们得到了关于使用 RingCaptcha 实现 OTP 组件的详细指导。最后，我们成功地将 React 本机 OTP SMS 验证组件推送到 Bit。在下一章中，我们将继续添加更多的包和组件到我们的集合中。回头见！！

### 资源

*   [文档](http://my.ringcaptcha.com/docs/api#phone-verification?utm_source=devto-kris&utm_dev.to=article&utm_campaign=react-native-sms-verification)
*   [位为反应位](https://docs.bit.dev/docs/bit-react-tutorial.html)

### 了解更多

*   [如何构建和共享组件库](https://blog.bitsrc.io/how-to-build-and-share-a-component-library-8edbf1eae1f3)
*   [如何从 bit.dev 中找到并使用组件来构建您的应用](https://blog.bitsrc.io/how-to-find-and-use-components-from-bit-dev-to-build-your-app-56623ed27444)
*   [开发一个 React 原生手机 App 要多少钱？](https://blog.bitsrc.io/how-much-does-it-cost-to-develop-a-react-native-mobile-app-1e555f872d65)

* * *