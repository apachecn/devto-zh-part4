# 使用 React Native 的 AirBnB 克隆第 4 部分:登录错误通知

> 原文：<https://dev.to/kris/airbnb-clone-with-react-native-part-4-login-error-notifications-4nke>

本教程是我们在 React Native 中实现一个 [AirBnB 克隆的第四章。在前面的章节中，我们已经成功地用 Firebase 实现了电子邮件认证。如果您需要了解最新情况，以下是第 1-3 部分的链接:](https://www.instamobile.io/app-templates/real-estate-app-template-react-native)

*   [AirBnB 克隆版 React Native Part 1:主屏幕 UI](https://dev.to/kris/airbnb-clone-with-react-native-part-1-home-screen-ui-3dnl)
*   [AirBnB 克隆与 React 原生第二部分:登录屏幕 UI](https://dev.to/kris/airbnb-clone-with-react-native-part-2-login-screen-ui-3nn5)
*   [AirBnB 克隆与 React 原生第 3 部分:电子邮件认证与 Firebase](https://dev.to/kris/airbnb-clone-with-react-native-part-3-email-authentication-with-firebase-f8p)

在第 4 部分中，我们将从我们停止的地方继续，为认证过程实现一个通知特性。总之，我们将在底部创建一个警告栏，并更改显示 Firebase 验证通知的背景颜色。

***那么，我们开始吧！*T3】**

### 创建通知组件

首先，我们将在 Notification.js 文件中创建一个名为 Notification 的简单组件。这里，我们需要创建一个名为 Notification 的简单类，它扩展到 React 的组件模块。现在，我们将使用包装在视图组件中的文本组件来呈现文本，如下面的代码片段所示: