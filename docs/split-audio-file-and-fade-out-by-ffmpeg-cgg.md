# 分割音频文件并通过 ffmpeg 淡出

> 原文：<https://dev.to/sanemat/split-audio-file-and-fade-out-by-ffmpeg-cgg>

使用前 60 秒。从每 3 秒 57 秒淡出。

```
ffmpeg -i source.mp3 -af "afade=t=out:st=57:d=3" -to 60 result.mp3 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

*   [想尽量理解的 ffmpeg -小龙虾看着...](https://zariganitosh.hatenablog.jp/entry/20150619/understand_ffmpeg)