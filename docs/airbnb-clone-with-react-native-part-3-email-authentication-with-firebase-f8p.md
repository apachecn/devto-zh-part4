# 使用 React Native 的 AirBnB 克隆第 3 部分:使用 Firebase 的电子邮件认证

> 原文：<https://dev.to/kris/airbnb-clone-with-react-native-part-3-email-authentication-with-firebase-f8p>

### 分分钟做一个功能齐全的房产 app

通过在几分钟内发布 iOS 和 Android 的房地产应用程序，成为下一个 Trulia、Zillow 或 Airbnb。下载我们的功能性 [React Native Real Estate](https://www.instamobile.io/app-templates/real-estate-app-template-react-native/) 应用程序模板，与 Firebase 后端集成，立即启动您自己的应用程序。这个用 React Native 编写的漂亮的应用程序模板非常注重细节，代表了启动新移动应用程序开发的最佳方式。

本教程是我们在 React Native 中实现 Airbnb 克隆的第三章。在前面的章节中，我们已经成功地实现了主屏幕 UI 和登录屏幕 UI:

*   [AirBnB 克隆版 React Native Part 1:主屏幕 UI](https://dev.to/kris/airbnb-clone-with-react-native-part-1-home-screen-ui-3dnl)
*   [AirBnB 克隆与 React 原生第二部分:登录屏幕 UI](https://dev.to/kris/airbnb-clone-with-react-native-part-2-login-screen-ui-3nn5)

在第 3 部分中，我们将从第 2 部分停止的地方继续。这里，我们将在登录屏幕 UI 中实现电子邮件身份验证。基本上，我们将把用户认证机制与 Firebase 集成在一起。为了简单起见，我们将首先使用基本的电子邮件和密码认证。

> 如果你对使用其他认证机制感兴趣，可以看看我的教程[用 React Native](https://dev.to/kris/implementing-facebook-login-on-android-with-react-native-and-firebase-1bj2) 在 Android 上实现脸书登录。

我们开始吧！

### 设置 Firebase 包

首先，我们需要设置我们的 Firebase 包。为此，我们将把 react-native-firebase 包安装到我们的项目中，它只是将 firebase 集成到我们的项目中。它促进了在原生 Firebase SDKs 之上的轻量级层，同时尽可能地镜像 Firebase Web SDK 的 API。要安装这个包，我们可以使用 yarn 或 npm:

使用纱线: