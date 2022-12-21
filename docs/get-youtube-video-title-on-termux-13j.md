# 在 termux 上获取 youtube 视频标题

> 原文：<https://dev.to/voyeg3r/get-youtube-video-title-on-termux-13j>

# [T1】简介](#intro)

这个教程的主要原因是分享我以前在 android 上的一个 akward 经验，并解决。有时候，当我在 twitter 上分享 youtube 上的视频时，它只分享视频的 url。我也想得到这个标题，所以我写了这个脚本

```
#!/data/data/com.termux/files/usr/bin/bash

URL=$(termux-clipboard-get)

TITLE=$(youtube-dl --skip-download --get-title --no-warnings $URL | sed 2d | awk '{print tolower($0)}')

echo "$TITLE' '$URL"  | tee termux-clipboard-set 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 termux 设置 pbcopy 和 pbpaste

打开您的 termux 并运行

```
apt install termux-api neovim 
```

Enter fullscreen mode Exit fullscreen mode

现在在你的 termux 上编辑你的~/。bashrc

```
alias pbcopy='termux-clipboard-get'
alias pbpaste='termux-clipboard-set' 
```

Enter fullscreen mode Exit fullscreen mode