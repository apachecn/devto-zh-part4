# AirBnB 克隆与 React 原生第 1 部分:主屏幕用户界面

> 原文：<https://dev.to/kris/airbnb-clone-with-react-native-part-1-home-screen-ui-3dnl>

### 分分钟做一个功能齐全的房产 app

通过在几分钟内发布 iOS 和 Android 的房地产应用程序，成为下一个 Trulia、Zillow 或 Airbnb。下载我们的功能性 [React Native Real Estate](https://www.instamobile.io/app-templates/real-estate-app-template-react-native/) 应用程序模板，与 Firebase 后端集成，立即启动您自己的应用程序。这个用 React Native 编写的漂亮的应用程序模板非常注重细节，代表了启动新移动应用程序开发的最佳方式。

#### 带有矢量图标、道具等的动态 UI 组件

本教程是一个项目的第一部分，旨在帮助我们用 React Native 构建 AirBnB 克隆。

首先，我们将学习如何使用 React Native 设置 Airbnb 样板克隆，并创建一个简单的主屏幕 UI。我们都知道 Airbnb 是一家以旅行、预订各种住宿等形式提供酒店服务的公司。

他们的服务被全球数百万人使用。这些网站和应用程序被广泛用于预订酒店、与主人联系、发现有整栋房屋的地点等。

在本教程中，我们只是实现了他们应用程序 UI 的一小部分——主屏幕。我们将在接下来的教程中介绍应用程序的其余界面和工作方式。

我们开始吧！

### 创建新项目

我们需要创建一个名为*airibcloneapp*的新 react-native 项目，并在我们的命令行中使用以下代码行在 iOS 上运行它。

首先，要初始化 React 本地项目，运行命令:

react-native init airbnbcloneapp

然后，要在 iOS 上运行项目，运行命令:

react-native run-ios

### 构建应用结构

在应用程序结构方面，首先，我们需要创建一个包含所有应用程序文件的源文件夹，即“ *src* ”文件夹。在其中，我们需要创建组件和容器。之后，我们继续创建屏幕和“*img”*和“*style”*文件夹，用于图像和 CSS 样式。

### 创建颜色配置文件配置

在这一步，我们需要为我们的克隆应用程序创建一个颜色配置文件配置。首先，我们需要在“*风格”*文件夹中创建一个“*颜色”*子文件夹，然后在里面创建一个“ *index.js* 文件。然后，我们需要打开文件并将颜色代码填充到数组键中，如下面的代码片段所示: