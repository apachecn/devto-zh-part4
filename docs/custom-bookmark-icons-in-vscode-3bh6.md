# VSCode 中的自定义书签图标

> 原文：<https://dev.to/brianjdevries/custom-bookmark-icons-in-vscode-3bh6>

# 背景

我最近开始在 [FreeCodeCamp](https://www.freecodecamp.org/) 上学习 web 开发。我发现了 [VScode](https://code.visualstudio.com/) ，不知道没有它我是怎么活下来的！任何熟悉 VScode 的人都知道如何添加扩展，但有时定制选项并不广为人知。

我特别喜欢 Visual Studio 中的书签功能，并且希望在 VScode 中也有该功能，所以我安装了书签扩展。然而，我不喜欢默认的瘦蓝色书签在我的深色背景上的样子。我发现有一个设置可以让你使用自定义的 16x16 像素图标作为你的书签图像！

这篇文章是关于如何添加书签扩展并定制它，但我也创建了一个不同颜色书签的 [github repo](https://github.com/techCarpenter/custom_bookmarks) *，所以如果你不想创建自己的书签，就不必创建了。

* *注意:*回购中的书签图标是仿照 Visual Studio 2017 中的书签图标。

# 自定义 VScode 中的书签图标

1.  您需要为 VScode 安装[书签扩展](https://marketplace.visualstudio.com/items?itemName=alefragnani.Bookmarks)。
2.  在 VScode 中找到一个你想用作书签的 16x16 像素图标(或者在我的 github 上克隆/下载[预制图标](https://github.com/techCarpenter/custom_bookmarks)),并将该图像文件放在本地文件夹中。
3.  将此设置添加到 VScode:
    中的 settings.json 文件

    ```
    "bookmark.gutterIconPath":  "c:\\path_to_icon\\icon.png" 
    ```

4.  **(可选)**将*【书签切换】*的 keybinding 更新为*【Ctrl+K Ctrl+K】*(模仿 Visual Studio IDE 快捷方式)

5.  享受您定制的书签！

我希望你和我一样喜欢定制适合你风格的 VScode！如果你对我有任何问题或者只是想联系，请在下面评论或者在 twitter 上给我发消息！我喜欢和陌生人交谈😃

下次见，编码快乐！