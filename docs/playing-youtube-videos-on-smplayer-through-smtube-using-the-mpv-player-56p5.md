# 使用 mpv 播放器通过 smtube 在 smplayer 上播放 youtube 视频

> 原文：<https://dev.to/djemos/playing-youtube-videos-on-smplayer-through-smtube-using-the-mpv-player-56p5>

*   使用 mpv 播放器通过 smtube 在 smplayer 上播放 youtube 视频

我尝试过 vlc，mplayer，smplayer 尝试在 ubuntu，slackel 和其他发行版中播放 youtube 视频。
但是将时间滑块移动到视频中的新位置时，Smplayer 会崩溃。日志文件消息读取...

MPlayer 被模块中的信号 11 中断:seek
ID_SIGNAL=11

我发现使用 mpv 媒体播放器的 smtube 和 smplayer 可以播放 youtube 视频而不会崩溃。

在 slackel 7.2 可以安装 smplayer，mpv 和最新的 smtube 加 libass 这是一个依赖。

```
 sudo slapt-get -u
    sudo slapt-get -i smplayer libass mpv smtube 
```

然后运行 smplayer 并从菜单选项中检查 mpv 播放器是否为默认播放器。