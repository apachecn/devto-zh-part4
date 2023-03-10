# 在 tmux 中调整窗格大小

> 原文：<https://dev.to/michael/resizing-panes-in-tmux-2da7>

作为开发人员，我每天使用的工具之一是 tmux。能够在一个终端窗口中有多个窗格无疑改进了我的工作流程。

默认情况下，创建窗格时，tmux 会将窗口分割成 50%的部分。但是，如果你不需要某些窗格有这么多的房地产和重点呢？你只是想要一个小窗格来监视事物。

要调整 tmux 窗格的大小，您首先需要点击您的前缀——默认为`ctrl + b`——然后点击冒号键`:`。这样做的目的是在屏幕底部弹出一个提示。

现在你需要在提示中输入`resize-pane`，后面跟着一个连字符`-`和`D, U, L, R`。你可能会猜到它代表了下、上、左、右，也就是你想要调整窗格大小的方向。当使用`resize-pane`命令时，调整大小将应用于最后一个有焦点的窗格。

下面是整个 resize pane 命令的一个例子，它将窗格向左调整一个单元格的大小，这是 tmux 调整大小的单位:

```
// This assumes that you've hit ctrl + b and : to get to the command prompt
:resize-pane -L 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些额外的调整 tmux 窗格大小的例子:

```
:resize-pane -D (Resizes the current pane down by 1 cell)
:resize-pane -U (Resizes the current pane upward by 1 cell)
:resize-pane -L (Resizes the current pane left by 1 cell)
:resize-pane -R (Resizes the current pane right by 1 cell)
:resize-pane -D 10 (Resizes the current pane down by 10 cells)
:resize-pane -U 10 (Resizes the current pane upward by 10 cells)
:resize-pane -L 10 (Resizes the current pane left by 10 cells)
:resize-pane -R 10 (Resizes the current pane right by 10 cells) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

要了解更多 tmux 技巧，请查看我的网站。如果你对视频或文章中的内容有任何疑问，请随时通过 [Twitter @michaelsoolee](https://twitter.com/michaelsoolee) 联系我。