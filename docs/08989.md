# ffmpeg:从电影中移除音频

> 原文：<https://dev.to/nabbisen/ffmpeg-remove-audio-from-movie-37li>

ffmpeg 是一个开源命令行工具，它使我们能够转换音频或视频格式。

[`-an`](https://ffmpeg.org/ffmpeg.html#Audio-Options) 选项禁用录音。
您可以删除电影中的音频:

```
$  ffmpeg -i example.mp4 -an example-nosound.mp4 
```

Enter fullscreen mode Exit fullscreen mode

此外，用[鱼壳](https://fishshell.com/)处理多个文件的即时一行程序是这样的:

```
$  for x in ./*.mp4; ffmpeg -i $x -an $x.no-sound.mp4; end 
```

Enter fullscreen mode Exit fullscreen mode