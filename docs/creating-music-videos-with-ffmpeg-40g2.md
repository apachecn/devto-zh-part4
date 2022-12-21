# 使用 FFmpeg 创建音乐视频

> 原文：<https://dev.to/darkhist/creating-music-videos-with-ffmpeg-40g2>

这篇文章最初发表在[我的博客](https://qsalas.me/blog)上

## 简介

在对 YouTube 上的高保真嘻哈音乐视频产生兴趣后，我想学习如何创作自己的视频。你知道那种来自标志性动漫电影的无限循环的 gif，伴随着高保真的节奏来放松/学习。我有一些音频文件和一系列 gif，但我不知道如何组合它们。然后，我找到了 FFmpeg！

> *FFmpeg 是什么？*

根据 [FFmpeg 网站](https://ffmpeg.org/)的说法，FFmpeg 是“一个完整的、跨平台的解决方案，用于录制、转换和流式传输音频和视频”。今天，我们将使用它来创建一个来自 GIF 和 MP3 的音乐视频！

## 入门

在我们开始之前，我想指出我使用的是运行 OSX Mojave 10.14.5 的 MacBook Pro。如果你使用的是不同的操作系统，你可以通过[访问他们的下载页面](https://ffmpeg.org/download.html)来安装 FFmpeg。

今天，我们将使用 OSX 流行的软件包管理器 Homebrew 来安装 FFmpeg。

打开您的终端开始吧！

如果没有自制，可以通过运行
获得

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

Enter fullscreen mode Exit fullscreen mode

在你安装了 Homebrew 之后，你可以通过运行
来获得 FFmpeg

```
brew install ffmpeg 
```

Enter fullscreen mode Exit fullscreen mode

如果安装失败并出现奇怪的错误，“Xcode 在 Mojave 上是不够的”，运行建议的命令

```
xcode-select --install 
```

Enter fullscreen mode Exit fullscreen mode

并在出现提示时安装命令行开发工具。然后，运行

```
open /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg 
```

Enter fullscreen mode Exit fullscreen mode

并遵循安装指南。最后，重新运行 FFmpeg 安装命令。

要验证 FFmpeg 是否已正确安装，请运行

```
which ffmpeg 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到类似于
的内容

```
/usr/local/bin/ffmpeg 
```

Enter fullscreen mode Exit fullscreen mode

## 创建视频

现在有了 FFmpeg，就可以开始了！

确保在计算机的同一个文件夹中保存了 GIF 和 MP3 文件。我把我的文件放在一个`Documents/music`文件夹里，但是你可以把它们保存在你喜欢的任何地方。

使用
导航到文件所在的文件夹

```
cd path/to/your/files 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，这看起来像

```
cd Documents/music 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以使用下面的命令来创建我们的音乐视频！

```
ffmpeg \
-i song.mp3 \
-stream_loop -1 \
-i cool-gif.gif \
-codec:v libx264 \
-preset slow \
-tune animation \
-codec:a aac \
-pix_fmt yuv420p \
-shortest \
mv.mp4 
```

Enter fullscreen mode Exit fullscreen mode

用你的文件名替换`song.mp3`和`cool-gif.gif`！

我将我的输出文件命名为`mv.mp4`，但是您可以随意命名您的文件。视频创建后，会自动保存到当前文件夹。

> 好了，现在我们有了很棒的音乐视频，但是 FFmpeg 实际上在做什么呢？...

## 命令击穿

`ffmpeg`:调用 FFmpeg 程序

`-i [file]`:表示指定的文件将被用作输入

`-stream_loop -1`:设置输入应该循环的次数。在我们的例子中，我们希望 GIF 永远循环，所以我们将循环计数设置为-1

`-codec:v libx264`:指定我们想要使用 libx264 库来用 H.264 编解码器压缩我们的 GIF。H.264 是一种奇特的视频压缩工具，用于缩小视频的文件大小，同时尽可能保持高质量。H.264 使用了很酷的压缩技术，如色度二次采样和运动补偿，我们今天不会深入讨论，但如果你有兴趣了解更多，我强烈推荐锡德·巴拉的这篇博客文章

`-preset slow`:此设置改变视频编码速度，从而改变我们视频的压缩。视频编码是一项 CPU 密集型任务，因此较慢的编码速度需要较长的时间，但会给我们更高质量的视频。请随意探索其他预设选项！

`-tune animation`:此选项根据我们正在使用的输入类型更改视频编码设置。在我的例子中，我使用了一个动漫的 GIF，所以`animation`似乎是最好的选择

*有关 H.264 编解码器的更多信息，或探索其他预置和调谐选项，请参见下面的“资源”部分中的 H.264 编码指南*

`-codec:a aac`:指定我们希望使用 AAC 进行音频编码。AAC 是一种非常流行的音频编码器，与 MP3 文件相比，通常可以获得更好的音质

`pix_fmt yuv420p`:指定我们的输出视频应该使用 YUV 像素格式。YUV 是一种特殊的颜色空间，类似于 RGB，允许我们的视频用 QuickTime 和其他视频播放器播放。如果你想了解更多关于 YUV 的信息，维基百科页面是一个很好的起点

`-shortest`:告诉 FFmpeg 在最短的输入编码完成后立即结束编码。这确保了音乐视频的持续时间与输入音频的持续时间相匹配

`mv.mp4`:输出文件的名称

## 资源

[FFmpeg 文档](https://ffmpeg.org/ffmpeg.html)

[H.264 编码指南](https://trac.ffmpeg.org/wiki/Encode/H.264)

## 结论

在使用 FFmpeg 之后，我了解了更多关于音频和视频编码、像素格式等等的知识！我希望你也是！探索音频和视频编码，同时制作有趣的东西，这真的很酷。感谢阅读！如果你喜欢这篇文章，[在 Twitter 上告诉我](https://twitter.com/QuinnSalas) :~)

*感谢格雷格·哈里斯阅读了这篇文章的草稿*