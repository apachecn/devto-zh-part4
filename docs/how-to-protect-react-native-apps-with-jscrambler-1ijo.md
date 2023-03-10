# 如何用 Jscrambler 保护 React 原生应用

> 原文：<https://dev.to/jscrambler/how-to-protect-react-native-apps-with-jscrambler-1ijo>

[React Native](https://facebook.github.io/react-native/) 是脸书开发的基于 React 的开源移动应用框架。它实际上是最受移动开发欢迎的 JavaScript 框架之一，正如 2018 年“[JavaScript](https://2018.stateofjs.com/mobile-and-desktop/react-native/)状况”调查所示。

通过遵循本教程，您将能够将 Jscrambler 无缝集成到 React Native 的构建过程中，并使用最强大和最具弹性的[混淆](https://jscrambler.com/products/code-integrity/javascript-obfuscation)技术、[密码锁](https://blog.jscrambler.com/jscrambler-101-code-locks/)、[自我防御](https://blog.jscrambler.com/jscrambler-101-self-defending/)和[自我修复](https://docs.jscrambler.com/6.1/code-integrity/documentation/transformations/self-healing)功能来保护您的应用程序。

## 先决条件

为了正确地将 Jscrambler 集成到 React 原生构建过程中，我们只需要**设置 React 原生应用**和**配置 Jscrambler** 。让我们在下面介绍这两个步骤。

### 创建 React 原生应用

要使用 React Native 创建移动应用，您有两种主要方法:使用 Expo 或使用 React Native CLI。通常，Expo 更适合具有 Web 开发背景的开发人员(它不需要 Xcode 或 Android Studio)，而使用 React 原生 CLI 最适合那些来自原生开发背景的开发人员(它需要 Xcode 或 Android Studio)。

要将 Jscrambler 集成到 React 本机构建过程中，我们必须使用 React 本机 CLI。然而，如果您使用 Expo，您也可以通过弹出项目来集成 Jscrambler，我们将在下面进一步探讨。

出于本教程的目的，我们将克隆一个简单的购物清单应用程序:

```
git clone https://github.com/jscrambler/Integration-Examples.git 
```

现在，让我们进入 React Native app 的目录，安装项目的依赖项:

```
cd Integration-Examples/ReactNative/Example-Apps/react-native-grocery-list/
npm i 
```

我们的 *React Native* 应用程序的简化基础项目结构如下:

```
react-native-grocery-list/
|-- app.json
|-- babel.config.js
|-- index.js
|-- metro.config.js
|-- package.json
|-- yarn.lock
|-- __tests__/
|-- .expo/
|-- android/
| |-- app/
| | |-- build/
|-- App/
|-- assets/
|-- ios/
|-- node_modules/ 
```

*   `package.json`包含所有与 npm 相关的配置，如依赖关系、版本和脚本。

*   `App`目录展示了应用程序的源代码。然后构建源代码并打包到`android`或`ios`目录中。构建完成后，我们受保护的 HTML 和 JavaScript 文件将放在这里。

我们已经成功设置了 React 本地应用程序。让我们用 Jscrambler 来保护它。

### 配置 Jscrambler

如果你还没有创建一个 [Jscrambler 账户](https://jscrambler.com/signup?utm_source=dev.to&utm_medium=referral&utm_campaign=protect-react)，请确保在继续下一步之前创建。

Jscrambler 的所有配置将驻留在一个文件中:`.jscramblerrc`，它指定了我们希望使用的转换。

获取我们的配置文件最快的方法是通过 [Jscrambler Web 应用](https://app.jscrambler.com/dashboard?utm_source=dev.to&utm_medium=referral&utm_campaign=protect-react)。在那里，创建一个新的应用程序。现在，在*应用模式*选项卡中，选择语言规范和应用类型。接下来，选择你想要的转换(检查*模板*和*微调*标签)。在本教程中，我们将选择*模糊*模板。如果您在这些步骤中需要帮助，请参考我们的[指南](https://blog.jscrambler.com/jscrambler-101-how-to-use-the-cli/)。

现在，我们只需下载一个包含所有配置的 **JSON 文件，它将只用于快速获得所需的设置。**

<center>![Download Jscrambler JSON](img/c1e6d44fa8b1a1e3ef9647735f85bbbb.png)</center>

现在，让我们在 React 本地项目的根文件夹中创建一个名为`.jscramblerrc`的新文件。打开刚刚下载的`jscrambler.json`文件，将其所有内容复制到`.jscramblerrc`文件中。你最终的`.jscramblerrc`文件应该是这样的:

```
{  "keys":  {  "accessKey":  <ACCESS_KEY_HERE>,  "secretKey":  <SECRET_KEY_HERE>  },  "applicationId":  <APP_ID_HERE>,  "params":  [  {  "name":  "objectPropertiesSparsing"  },  {  "name":  "variableMasking"  },  {  "name":  "whitespaceRemoval"  },  {  "name":  "dotToBracketNotation"  },  {  "name":  "stringConcealing"  },  {  "name":  "functionReordering"  },  {  "name":  "propertyKeysObfuscation",  "options":  {  "encoding":  [  "hexadecimal"  ]  }  },  {  "name":  "regexObfuscation"  },  {  "options":  {  "features":  [  "opaqueSteps"  ]  },  "name":  "controlFlowFlattening"  },  {  "name":  "booleanToAnything"  },  {  "name":  "identifiersRenaming"  }  ],  "areSubscribersOrdered":  false,  "applicationTypes":  {  "webBrowserApp":  true,  "desktopApp":  false,  "serverApp":  false,  "hybridMobileApp":  false,  "javascriptNativeApp":  false,  "html5GameApp":  false  },  "languageSpecifications":  {  "es5":  true,  "es6":  false,  "es7":  false  },  "useRecommendedOrder":  true  } 
```

因为我们是通过 Jscrambler Web 应用程序直接获得这些信息的，所以我们的`accessKey`、`secretKey`和`applicationId`字段已经被填充了。如果您希望手动检索它们，请参考我们的[指南](https://blog.jscrambler.com/jscrambler-101-first-use/)。

值得注意的是，`params`部分指定了将用于保护 React 本机应用程序的转换。**这些可以由您**亲自挑选，通过在 Web 应用程序中选择或手动设置。你可以在这里找到所有可用转换的文档[。](https://docs.jscrambler.com/code-integrity/documentation/transformations?utm_source=dev.to&utm_medium=referral&utm_campaign=protect-react)

## 在构建过程中集成 Jscrambler

**注意:**如果您已经使用 Expo 设置了 React 本地项目，为了继续 Jscrambler 集成，您必须首先使用`expo eject`将其弹出。

由于 React Native 使用自己的捆绑包 Metro，Jscrambler 的 React Native 集成是通过 Metro 插件实现的。

首先，安装 [Jscrambler Metro 插件](https://github.com/jscrambler/jscrambler/tree/master/packages/jscrambler-metro-plugin) :

```
npm install jscrambler-metro-plugin --save-dev 
```

接下来，在项目的根文件夹中创建一个`metro.config.js`文件(在我们的例子中，我们已经有了这个文件)。现在，我们需要在文件的顶部放置两行代码。这段代码导入了 Jscrambler Metro 插件，并将使我们的应用程序能够使用它。在我们的食品杂货应用程序中，我们可以简单地删除`metro.config.js`中的所有现有代码，并粘贴以下代码:

```
const jscramblerMetroPlugin = require('jscrambler-metro-plugin')();

module.exports = jscramblerMetroPlugin; 
```

就是这样！我们准备好构建我们的应用程序了！为此，Android 和 iOS 有不同的方法。

**安卓系统**

为了生成一个 APK 文件并将其安装在一个附加的设备或仿真器上，我们使用:

```
cd android && ./gradlew installDebug 
```

为了简单地生成 APK 文件而不安装它，我们运行:

```
cd android && ./gradlew assembleDebug 
```

**对于 iOS**

如果我们是为 iOS 开发，这个过程就有点棘手了。首先，我们需要捆绑源文件，并在创建 IPA 包之前做好准备:

```
react-native bundle --entry-file index.js --platform ios --dev false --bundle-output ios/main.jsbundle --assets-dest ios 
```

这将输出一个`main.jsbundle`文件，然后我们需要在 Xcode 中使用它来构建我们的 IPA。为此，你可以遵循[这个指南](https://medium.com/@tunvirrahmantusher/create-ipa-and-apk-from-react-native-72fe53c6a8db)。

我们会在`<android|ios>/app/build/outputs/<apk|ipa>/debug`找到 Android (APK)或 iOS (IPA)的移动应用包。

## 结论

React Native 通常是在企业中构建跨平台移动应用程序的首选 JS 框架。除了脸书，它还被彭博、特斯拉、优步、Wix 和 Discord 等公司使用。

现在，攻击者比以往任何时候都更积极地寻求利用 JavaScript 的暴露性质来窃取或篡改通常包含关键业务逻辑的 JavaScript 代码。这就是为什么用 Jscrambler 保护 React 原生源代码成为开发过程中的一个关键步骤。

通过使用我们的 Metro 插件，您可以在几分钟内将 Jscrambler 集成到 React 本机构建过程中。但是，如果您在集成过程中遇到任何问题，请务必查看我们的[文档](https://docs.jscrambler.com/code-integrity/frameworks-and-libraries/react-native)或[联系我们](//mailto:support@jscrambler.com)。