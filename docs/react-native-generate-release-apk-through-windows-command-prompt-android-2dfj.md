# React 原生生成发布 APK 通过 Windows 命令提示符 Android

> 原文：<https://dev.to/skptricks/react-native-generate-release-apk-through-windows-command-prompt-android-2dfj>

来源: [React Native 通过 Windows 命令提示符 Android 生成发布 APK](https://www.skptricks.com/2019/06/react-native-generate-release-apk-file.html)

本教程介绍了如何在 react 本地应用程序中通过窗口命令提示符生成发布 APK 文件。APK 的完整形式是 Android 软件包。APK 文件是一个包含所有重要文件的生成包文件，建议在 android 移动设备上运行 android 应用程序。默认情况下，当我们创建 react 本地项目应用程序并将该应用程序安装在我们的设备仿真器上时，该应用程序将作为调试模式安装。但是我们不能得到 APK 安卓系统的签名版本，没有 APK 的原始文件，我们不能在谷歌 Play 商店上传这个文件。

[![](img/0a6df3abfd2eb1b9e0de3e941f064ba1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--znWbTAaB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-QD2NqQzjjuU/XRjNHFzD8oI/AAAAAAAADCs/M66YCeCxDUYJc79JvZEu8Iags8TwE6bPACLcBGAs/s400/11.jpg)

如果您想在谷歌 Play 商店上提交您的应用程序，或者想在其他 android 设备上运行您的最终应用程序，或者想在所有 android 设备上生成发布 APK 或在没有开发服务器的情况下运行您的应用程序，则您的应用程序必须使用证书进行数字签名。还有一个所有 react 原生初学者都面临的问题是，没有 JS 服务器，未签名的 APK 无法运行。因此，使用 Singed APK，我们可以在任何 android 移动设备上运行我们的 react 原生 android 应用程序，而无需运行 JS 服务器和上传。APK 把文件交给了谷歌 Play 商店。因此，在本教程中，我们将使用密钥库在 Windows PC 中生成无需 JS 服务器即可运行的签名版 android APK，并让我们一个接一个地遵循以下步骤:

总结简而言之:
首先我们要通过命令提示符生成一个签名密钥。
注意:命令提示符必须以管理员权限运行。
将生成的签名密钥放在 Android/app(React 本地项目下)文件夹中。
在 android/gradle.properties 文件和 android/app/build.gradle 文件中输入所需条目。
运行这个命令将目录切换到 android。使用的命令:
cd android
然后运行下面的命令来生成 APK 文件的签名版本。
命令使用:

gradlew 装配释放

点击此处阅读更多内容...