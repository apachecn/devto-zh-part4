# 转换您的媒体文件

> 原文：<https://dev.to/c3phas/convert-your-media-files-456i>

### ffmpeg

所以你有视频但是因为格式的原因不能播放？你想要一个 mp3，但却有 mp4 或其他视频格式？
你想要没有声音的视频
你有一个图像，应该是你的 mp3 文件的背景

ffmpeg 根据手册页是一个**快速视频和音频转换器**
ffmpeg 库是跨平台的因此可以在任何操作系统中使用，
ffmpeg 可以用于一个撕裂的东西但是我们在这里只看基本的，这个库是由著名的视频编辑在后台使用的。

在基于 debian 的系统上安装 ffmpeg

**apt-get 安装 ffmpeg**

让我们从转换视频(mp4 到 mp3)开始

**注意:ffmpeg 是一个命令行程序。**

**ffmpeg-I input.mp4-VN 输出. mp3**

-i 用于指定输入文件，该文件可以是 mp4、webm 或其他视频格式
-VN–该选项用于禁用视频录制。它抓取视频并删除它
output.mp3 是输出文件名

上述命令会将 output.mp3 转换为 output.mp3
其他相关选项包括
-sn-禁用字幕
-an-禁用录音

示例
**ffmpeg-I input.mp4-sn output.mp4**
如果输入启用了字幕，则输出将没有字幕

**ffmpeg-I input.mp4-安 output.mp4**

将导致没有声音的视频

将视频从一种格式转换为另一种格式(比如从 flv 转换为 avi)

**ffmpeg-I input . flv-q:a 0-q:v 0-f avi output . avi**

-我指定输入文件
-q 用于设置音频和视频文件的质量，a 用于音频，v 用于视频
-q:a 0 表示输出音频质量应与输入音频质量相同(主要称为流
)-f-用于指定目标格式在这种情况下，我们需要 avi 输出

注意你唯一需要改变的是输入格式和输出格式

**给 mp3 文件添加一张图片**
这样你有了 mp3 却想用你的 pic 而不是盯着光秃秃的屏幕
**ffmpeg-loop 1-y-I input.jpg-I input . MP3-acodec FLAC-vcodec libx 264-shortest-preset fast VF scale = 1280:2 output . mkv**

好了，好了，现在我们有了一点进步，我们有了更多的选择，让我们来看看其中的一些

编解码器–这只是指媒体比特流(媒体将如何编码和解码)
acodec 用于设置音频比特流在我们的例子中，我们使用 flac
vcodec 设置视频编解码器
-y 如果存在与输出名称相同的文件，它将被删除，即不经询问即覆盖输出(-n 相反， 表示不要覆盖)
-循环 1 -将循环我们的图像，直到 mp3 结束
-最短-当最短的输入流结束时完成编码
-预设快速-压缩过程将进行多快。 可设置为快速、慢速、慢速等
当该值设置为快速时，输出质量可能较低，但过程将快速结束。这是速度与质量的权衡
下一个视频用于设置视频格式，在这种情况下，我们使用缩放选项设置输出视频的大小，输出视频的宽度将为 1280

注意:我们已经指定了两个输入文件，一个图像和一个音频文件
来抑制输出添加选项(-hide_banner)

如果你想预览你的输出，使用 ffplay 播放文件而不保存它
**ffplay -i 输入**

Ffmpeg 可以用于更高级的东西，这是不可能涵盖的..查看 ffmpeg 文档
获取更清晰的 bash 脚本访问我的 github**[https://github.com/peter-macharia/media-converter](https://github.com/peter-macharia/media-converter)**