# 为电容器/Android 应用程序制作闪屏

> 原文：<https://dev.to/walkingriver/making-splash-screens-for-a-capacitor-android-application-55lg>

这是我昨天的帖子的后续，关于为离子/电容应用程序制作图标和闪屏。事实证明，为 iOS 制作一个闪屏很简单。只需将三个 2732x2732 文件放入项目中，Xcode 会处理剩下的部分。同样地，Android Studio 将获取一个大的 png 文件，并为您创建一组完美的图标。但是制作 Android 闪屏更难，所以我从昨天开始重新创建了我的模式，并为我做了一个脚本。

> 你可以在这里阅读我的 [Ionic 3 - 4 和电容体验。](https://walkingriver.com/ionic-3-to-4/)

为了创建 Android 闪屏，我使用了与 iOS 项目相同的 2732x2732 PNG 文件。使用我昨天用过的相同的`sips`命令，以及 Android Studio 和 Capacitor 给我的文件大小，我想出了这个版本的 shell 脚本。

```
#!/bin/bash

###### 
# Make a bunch of Android splash screens Master image.
# Given an image at least 1920x1920 pixels named splash.png, resize and create a
# duplicate at each specified size. Add new sizes as needed. When finished,
# copy the resulting images into your Android project, under the res folder.

mkdir -p ./res
function resize {
  w=$2
  h=$3

  echo $1 into $4

  [ -d "./res/$4" ] || mkdir ./res/$4

  if [[ $w -ge $h ]]; then
    sips -Z $w -c $h $w --out ./res/$4/splash.png $1
  else
    sips -Z $h -c $h $w --out ./res/$4/splash.png $1
  fi
}

resize $1 480 320 drawable
resize $1 800 480 drawable-land-hdpi
resize $1 480 320 drawable-land-mdpi
resize $1 1280 720 drawable-land-xhdpi
resize $1 1600 960 drawable-land-xxhdpi
resize $1 1920 1280 drawable-land-xxxhdpi
resize $1 480 800 drawable-port-hdpi
resize $1 320 480 drawable-port-mdpi
resize $1 720 1280 drawable-port-xhdpi
resize $1 960 1600 drawable-port-xxhdpi
resize $1 1280 1920 drawable-port-xxxhdpi 
```

Enter fullscreen mode Exit fullscreen mode

就像昨天创建 iOS 图标的脚本一样，这个脚本非常简单。这是很多蛮力，并假设一个快乐的路径。与昨天的不同，这一个实际上允许你指定要转换的启动画面，然后它会建立适当的`res`文件夹。只需将这些文件放到你的 Android Capacitor 项目中。你可以在`./android/src/app/main/res`中找到它们。

请随意提出改进建议。

# 反馈赞赏

你有什么意见或问题吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。