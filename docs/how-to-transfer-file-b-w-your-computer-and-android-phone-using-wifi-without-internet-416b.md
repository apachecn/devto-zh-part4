# 如何使用 wifi(无互联网)在您的电脑和 android 手机上传输文件

> 原文：<https://dev.to/coderoo7/how-to-transfer-file-b-w-your-computer-and-android-phone-using-wifi-without-internet-416b>

如果你使用旧的方法来传输文件，从你的智能手机到你的电脑，反之亦然，使用数据线，然后朋友，你需要停止了。今天我们将学习一个通用的方法，它适用于所有操作系统，无论是 Linux、Windows 还是 Mac OS。

## 概述

1.  在任何情况下，如果我们想在同一个网络上的两个或更多设备上传输文件，那么我们必须在任何一个设备上创建一个服务器，以便从其中复制/下载数据。
2.  现在，要使连接到同一个网络的安卓和 pc 之间能够进行数据传输，你必须在你的安卓手机上创建一个服务器。
3.  要创建服务器，有许多应用程序可用，如
    *   惊奇文件管理器 -开源文件管理器
    *   我用过的最好的文件管理器
4.  要传输文件，您可以选择 http、ftp 或 ssh 等协议。最常用的协议是 ftp(文件传输协议)来传输大文件，我们将在本教程中使用。
5.  一旦在 android 上设置了服务器，您就可以使用您在第一步中安装的 ftp 客户端(应用程序)提供的 ftp 地址( [ftp://192.168.43.1:2222](ftp://192.168.43.1:2222) )在您的设备上访问它的存储。

*注意-要传输数据，android 手机和您的设备应该在同一网络上，无论它们是使用路由器还是您的移动热点连接。*

### TLDR；让我们开始吧

1.  首先在你的手机上安装 ftp 客户端应用程序 [Amaze 文件管理器](https://play.google.c%0Aom/store/apps/details%20id=com.amaze.filemanager&hl=en_IN)
    *   对于 iphone，你可以在谷歌上搜索 ftp 客户端，并安装任何你想要的。
2.  打开您的移动热点，并将您的笔记本电脑或 pc 连接到它。
3.  接下来打开你的 ftp 客户端应用程序，你已经安装在第一步，点击创建 ftp 服务器。
    *   在 Amaze 文件管理器上看起来是这样的
4.  请注意应用程序上提供的 ftp 地址。
5.  在 linux 操作系统中打开您的文件管理器，或者在 windows 操作系统中打开**我的电脑**，转到网络选项卡并创建新网络，如下所示:
    *   连接类型:ftp ![ftp](img/2f0d6bfdc42f5c5705aa0f1c10245a53.png)
    *   名称:您想要的任何名称
    *   用户:如果未设置，请留空
    *   服务器地址:您手机的 ftp 地址( [ftp://192.168.43.1](ftp://192.168.43.1)
    *   端口:2211(无论你的 ftp 应用程序提供什么)
    *   密码:如果未设置，请将其留空

参考见下图截图:
[![addresspng](img/c3e19c8d7f26e90a21766191cf5ca53f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--WvfLO9Ky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/24404523/60288463-d8b99900-9931-11e9-9679-8189b7acafd4.png) [ ![address](img/5b122fd075148e2deb6ea7664d9d0680.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mjNnGoCc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/24404523/60288434-c7708c80-9931-11e9-90b1-b2256bbf2a36.png)

通过这种方式，我们可以在笔记本电脑上访问我们的手机存储，并在它们之间共享数据，而无需使用任何数据线或在我们的 pc 上安装任何第三方应用程序。