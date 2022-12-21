# 为 MacOS/iOS 设置 Flutter 开发环境

> 原文：<https://dev.to/kenjmoj/setting-up-flutter-dev-environment-for-macos-ios-3pla>

更多详情请访问下面的网站，

[https://flutter . dev/docs/get-started/install/MAC OS # update-your-path](https://flutter.dev/docs/get-started/install/macos#update-your-path)

#### 概述

1.  收集所有需要的工具
2.  安装 Flutter SDK
3.  更新路径
4.  安装 Xcode，运行以下命令

```
sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer 
```

运行一个 shell，然后执行

`flutter doctor`

按照上面列出的步骤执行结果

##### 备注:

启动 iOS 模拟器

```
open -a Simulator 
```