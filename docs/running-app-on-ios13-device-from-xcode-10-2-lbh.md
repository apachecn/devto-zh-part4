# 从 XCODE 10.2 在 iOS13/13.1.1 设备上运行应用程序

> 原文：<https://dev.to/szashik440/running-app-on-ios13-device-from-xcode-10-2-lbh>

iOS 13 和 XCODE 11 都在这里。万岁！！！！！！
[![](img/e7fbb8e5424cc74a02f3f8cdde81a8be.png)](https://i.giphy.com/media/11sBLVxNs7v6WA/giphy.gif) 
在 iOS 上有着令人兴奋的黑暗主题，手里拿着成熟的 [SwiftUI](https://developer.apple.com/documentation/swiftui) ，我只是兴奋地去拿 Xcode 11。但是等等！！你需要 macOS Catalina(10.15)并且是测试版。我不想让我的手下降到测试版。因此，我只是保留自己从这一点，回到我的老 Xcode 10.2。当我插入我的设备并构建我当前的一个项目时，Xcode 开始尖叫“找不到设备支持文件”。
[![](img/cd31304a93d0d3875f9ca5c7ee118238.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JpxDTv5j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Vt1KsQB.png) 
*嗯，真扫兴。*现在要不要下载 [Xcode 11 GM seed 2](https://developer.apple.com/download/) ？将近 7.1GB，用我的网速下载要几个小时。因此，我开始在 [SO](https://stackoverflow.com) 上寻找任何解决方案，并找到了一个可以在我的 iOS 13 设备上运行 Xcode 10.2 应用程序的解决方案。

*   从本报告的[下载 13.0 的设备支持文件。](https://github.com/iGhibli/iOS-DeviceSupport/blob/master/DeviceSupport/13.0.zip)

*   从本报告的[下载 13.1/13.1.1 的设备支持文件。](https://github.com/filsv/iPhoneOSDeviceSupport/blob/master/13.1%20(17A5821e).zip)

*   解压并用`13.0 (17A5508m)`(用`13.1`代表`13.1` / `13.1.1`)重命名文件夹。

*   进入 Xcode
    的 DeviceSupport 文件夹(打开 finder - >按 cmd+shift+G - > `/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/DeviceSupport`)

*   粘贴`13.0 (17A5508m)`文件夹在那里。

*   重启 Xcode。

现在运行您的项目，瞧！！它运行在你的 iOS 13 设备上。
T3![Yey](img/7049b98c0898fbdcc00ba611f59d473c.png)T5】