# 为电容器/iOS 应用程序制作图标的 Shell 脚本

> 原文：<https://dev.to/walkingriver/shell-script-to-make-icons-for-a-capacitor-ios-application-14in>

这是我关于为离子/电容应用创建 iOS 应用商店图标的帖子的后续。我改进了这个脚本，使它更容易被任何人使用。

> 描述问题的[原帖在此](https://walkingriver.com/make-icons/)。

## 工作原理

该脚本通过使用内置的图像处理命令行实用程序 sips 来工作。对于所需的每个图标大小，该脚本调用 sips 来调整一个普通 png 的大小，并将结果复制到适当的文件夹和文件名中。

如果苹果将来改变文件名和大小，你所要做的就是更新列表。

这是我更新的 shell 脚本。

```
#!/bin/bash

##################################################################
# Make a bunch of iOS App icons from a Master
# Given an image at least 512x512 pixels named icon.png, resize and create a
# duplicate at each specified size. Add new sizes as needed. When finished,
# copy the resulting images into your Xcode iOS project.

OUTDIR=ios/App/App/Assets.xcassets/AppIcon.appiconset

# Make sure there is an iOS directory already
if [ ! -d ./ios ] 
  then
    echo Add iOS App first
    exit 1
fi

if [ ! -f icon.png ]
  then
    echo Please add a PNG image named icon.png to this folder and re-run this script.
    exit 1
fi

echo Making $OUTDIR if it does not already exist.
[ -d $OUTDIR ] || mkdir -p $OUTDIR

echo Generating Icons...

sips -z 20 20 --out $OUTDIR/AppIcon-20x20@1x.png icon.png
sips -z 40 40 --out $OUTDIR/AppIcon-20x20@2x-1.png icon.png
sips -z 40 40 --out $OUTDIR/AppIcon-20x20@2x.png icon.png
sips -z 60 60 --out $OUTDIR/AppIcon-20x20@3x.png icon.png
sips -z 29 29 --out $OUTDIR/AppIcon-29x29@1x.png icon.png
sips -z 58 58 --out $OUTDIR/AppIcon-29x29@2x-1.png icon.png
sips -z 58 58 --out $OUTDIR/AppIcon-29x29@2x.png icon.png
sips -z 87 87 --out $OUTDIR/AppIcon-29x29@3x.png icon.png
sips -z 40 40 --out $OUTDIR/AppIcon-40x40@1x.png icon.png
sips -z 80 80 --out $OUTDIR/AppIcon-40x40@2x-1.png icon.png
sips -z 80 80 --out $OUTDIR/AppIcon-40x40@2x.png icon.png
sips -z 120 120 --out $OUTDIR/AppIcon-40x40@3x.png icon.png
sips -z 512 512 --out $OUTDIR/AppIcon-512@2x.png icon.png
sips -z 120 120 --out $OUTDIR/AppIcon-60x60@2x.png icon.png
sips -z 180 180 --out $OUTDIR/AppIcon-60x60@3x.png icon.png
sips -z 76 76 --out $OUTDIR/AppIcon-76x76@1x.png icon.png
sips -z 152 152 --out $OUTDIR/AppIcon-76x76@2x.png icon.png
sips -z 167 167 --out $OUTDIR/AppIcon-83.5x83.5@2x.png icon.png 
```

Enter fullscreen mode Exit fullscreen mode

要使用它，首先确定你在 Mac 上。

1.  将上面的脚本复制到 Ionic 项目的顶层文件夹中。将文件命名为`make-icons.sh`。
2.  用`chmod +x make-icons.sh`使脚本可执行
3.  如果您还没有添加电容集成和 iOS 平台，请添加。关于这方面的更多信息，见[我关于增加电容](https://walkingriver.com/ionic-3-to-4/)的帖子。
4.  在同一个文件夹中添加一个名为`icon.png`的 PNG 图像。此图像至少应为 1024x1024，并且不应包含圆角或透明度。
5.  执行`./make-icons.sh`。
6.  打开 IOs/App/App/assets . xc assets/App icon . appiconset/来查看您的图标。
7.  尽情享受吧！

# 反馈赞赏

你有什么意见或问题吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。