# 用 3 个命令从安卓 APK 中提取代码

> 原文：<https://dev.to/protium/extract-code-from-android-apk-with-3-commands-1o4a>

我是说，用三种工具

## 免责声明

这个帖子仅用于信息和教育目的

## 要求

*   [拉开拉链](http://www.info-zip.org/)
*   [dex2jar](https://github.com/pxb1988/dex2jar)
*   [jd-gui](https://github.com/java-decompiler/jd-gui)

## 终端时间

```
# extract classes.dex
unzip -j <apkfile.apk> classes.dex
# transform dex file to jar file
d2j-dex2jar.sh classes.dex
# read the sources
jd-gui classes-dex2jar.jar 
```

Enter fullscreen mode Exit fullscreen mode

## 奖金

你使用 native script/Ionic/Cordova/Phonegap/“插入一些基于 webview 的移动框架”吗？
这就是别人阅读你的 javascript 代码的简单程度

```
# find the bundled JS
unzip -l <apK file> | grep '.js'
# extract the bundle
unzip -j <apk file> assets/app.js
# find endpoint, or api keys
cat app.js | grep 'api*\|http*'
# DoS the endpoints
echo "just kidding" 
```

Enter fullscreen mode Exit fullscreen mode

## 编辑

如果你想避免命令，只需使用一个有用户界面的应用程序

*   [jadx](https://github.com/skylot/jadx)
*   [安卓二进制分析工具](https://github.com/google/android-classyshark)

希望你觉得有用，有教育意义。

封面图片来自[https://www.eff.org/issues/coders/reverse-engineering-faq](https://www.eff.org/issues/coders/reverse-engineering-faq)