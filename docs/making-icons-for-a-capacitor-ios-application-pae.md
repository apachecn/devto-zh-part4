# 为电容器/iOS 应用程序制作图标

> 原文：<https://dev.to/walkingriver/making-icons-for-a-capacitor-ios-application-pae>

我最近把我的一款手游升级到了 Ionic 4 和电容。我发现的一个缺点是缺少一个简单的工具来创建图标和闪屏。我想 Ionic 团队最终会阐明这种情况，但同时我想找到一种替代方案。考虑到我有时会是一个吝啬的人，我想最好是建立自己的解决方案。

> 你可以在这里阅读我的 [Ionic 3 - 4 和电容体验。](https://walkingriver.com/ionic-3-to-4/)

我从一个 1280x1280 像素的 PNG 文件开始。我知道 Mac 有一些内置的命令行工具可以帮我完成繁重的工作，Xcode 为我提供了我需要生成的文件名和大小。有了这些信息，剩下的就容易了。

这是我公认的简单的 shell 脚本。

```
##################################################################
# Make a bunch of iOS App icons from a Master
# Given an image at least 512x512 pixels named icon.png, resize and create a
# duplicate at each specified size. Add new sizes as needed. When finished,
# copy the resulting images into your Xcode iOS project.

mkdir ./AppIcon.appiconset/
sips -z 20 20 --out ./AppIcon.appiconset/AppIcon-20x20@1x.png icon.png
sips -z 40 40 --out ./AppIcon.appiconset/AppIcon-20x20@2x-1.png icon.png
sips -z 40 40 --out ./AppIcon.appiconset/AppIcon-20x20@2x.png icon.png
sips -z 60 60 --out ./AppIcon.appiconset/AppIcon-20x20@3x.png icon.png
sips -z 29 29 --out ./AppIcon.appiconset/AppIcon-29x29@1x.png icon.png
sips -z 58 58 --out ./AppIcon.appiconset/AppIcon-29x29@2x-1.png icon.png
sips -z 58 58 --out ./AppIcon.appiconset/AppIcon-29x29@2x.png icon.png
sips -z 87 87 --out ./AppIcon.appiconset/AppIcon-29x29@3x.png icon.png
sips -z 40 40 --out ./AppIcon.appiconset/AppIcon-40x40@1x.png icon.png
sips -z 80 80 --out ./AppIcon.appiconset/AppIcon-40x40@2x-1.png icon.png
sips -z 80 80 --out ./AppIcon.appiconset/AppIcon-40x40@2x.png icon.png
sips -z 120 120 --out ./AppIcon.appiconset/AppIcon-40x40@3x.png icon.png
sips -z 512 512 --out ./AppIcon.appiconset/AppIcon-512@2x.png icon.png
sips -z 120 120 --out ./AppIcon.appiconset/AppIcon-60x60@2x.png icon.png
sips -z 180 180 --out ./AppIcon.appiconset/AppIcon-60x60@3x.png icon.png
sips -z 76 76 --out ./AppIcon.appiconset/AppIcon-76x76@1x.png icon.png
sips -z 152 152 --out ./AppIcon.appiconset/AppIcon-76x76@2x.png icon.png
sips -z 167 167 --out ./AppIcon.appiconset/AppIcon-83.5x83.5@2x.png icon.png 
```

Enter fullscreen mode Exit fullscreen mode

如我所说，非常简单。再多一点时间，我可能会想出一些更优雅的东西，但这种蛮力解决方案已经足够好了。它假设您有一个名为 icon.png 的 PNG 文件，该文件至少应为 512x512。

# 反馈赞赏

你有什么意见或问题吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。