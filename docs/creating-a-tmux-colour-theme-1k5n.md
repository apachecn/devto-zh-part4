# 创建一个德古拉 tmux 颜色主题

> 原文：<https://dev.to/cassidycodes/creating-a-tmux-colour-theme-1k5n>

我最近决定在我的编码环境中使用 [Dracula](https://draculatheme.com/) 。不管喜欢还是讨厌，在过去的两年里，我一直在使用 Vim 和 [tmux](https://github.com/tmux/tmux) ，我从别人那里继承了我的 Vim 和 tmux 配置。我一头扎进这个环境，很快就习惯了，但是我从来没有花太多时间来配置它。

当我把 Vim 切换到 Dracula 时，我立刻意识到我也需要改变我的 tmux 颜色！

下面是我在 tmux 配置中编写主题的方法。但首先，这是它的样子！😍

[![a screenshot of a terminal with a tmux window](img/e2efa0440d0f57140d4f2a0c89151dfe.png)](https://cassidy.codimg/posts/tmux-theme/tmux-dracula.png)

## 通用设置

首先，我希望所有德古拉颜色的十六进制代码就在我面前，这样我就不必一直切换到浏览器窗口。然后，让我们告诉 tmux 使用支持颜色的终端。

你会注意到我用十六进制代码来表示这些颜色。tmux 支持将十六进制代码传入配置，但它会将十六进制代码转换为最接近的 256 色。

```
# Dracula Colours
# background_color '#282a36'
# current_line_color '#44475a'
# foreground_color '#f8f8f2'
# comment_color '#6272a4'
# cyan '#8be9fd'
# green '#50fa7b'
# orange '#ffb86c'
# pink '#ff79c6'
# purple '#bd93f9'
# red '#ff5555'
# yellow '#f1fa8c'

set -g default-terminal "screen-256color" 
```

Enter fullscreen mode Exit fullscreen mode

## 边框

tmux 会话可以有窗口，窗口可以有窗格。每个窗格是一个单独的终端会话，每个窗口可以有一个或多个终端会话。如果你熟悉 iTerm，可以把窗格想象成水平或垂直分割。

```
# pane border
set -g pane-border-style fg='#6272a4'
set -g pane-active-border-style fg='#ff79c6' 
```

Enter fullscreen mode Exit fullscreen mode

这里我们设置了两种风格。默认的边框颜色是德古拉的深紫色注释颜色。tumx 样式指令是前景、`fg`和背景`bg`的逗号分隔样式列表。

`fg`和`bg`每个都接受一种颜色和一个属性列表。tmux 文档列出了您可以使用的所有属性:

> 这些属性要么是无，要么是以逗号分隔的一个或多个列表:打开属性的`bright`(或`bold`)、`dim`、`underscore`、`blink`、`reverse`、`hidden`、`italics`或`strikethrough`，或者是以`no`为前缀关闭属性的属性。

这两种样式告诉 tmux 将所有的边框都设为深紫色，但是如果我的光标在一个窗口中，则将该窗格的边框设为粉红色。

## 消息样式

在 tmux 窗口的底部，你会看到状态栏。但是这被 tmux 命令行和来自 tmux 的任何消息所掩盖。

我把它和状态栏相似，灰色背景，蓝色文本。

```
# message text
set -g message-style bg='#44475a',fg='#8be9fd' 
```

Enter fullscreen mode Exit fullscreen mode

## 状态线

状态行由三部分组成。`status-left`、`window-status`和`status-right`。还记得我说过 tmux 有多个窗口吗？你可以把这些想象成 iTerm 中的标签。所有的`window-status`指令都是这些标签的样式。

让我们从一些基本配置开始。

```
set -g status-style bg='#44475a',fg='#bd93f9'
set -g status-interval 1 
```

Enter fullscreen mode Exit fullscreen mode

这里我们设置背景为深灰色，前景为紫色。虽然，紫色稍后会被覆盖。

下一行对我们稍后要做的`status-right`很重要。它告诉 tmux 每秒刷新一次状态行。

好了，现在开始`status-left`。

```
# status left
# are we controlling tmux or the content of the panes?
set -g status-left '#[bg=#f8f8f2]#[fg=#282a36]#{?client_prefix,#[bg=#ff79c6],} ☺ ' 
```

Enter fullscreen mode Exit fullscreen mode

这里的语法比我们设计状态行的时候变得更加复杂了。status 和 window 选项以单个字符串作为参数，但是您可以通过方括号传递样式，并且可以在花括号内调用 tmux 变量。

所以我的状态左边一行写着，让背景变成浅灰色，前景变成深灰色。然后我们有一个条件。是否按下了客户端前缀键？如果是这样，让我们实际上使背景粉红色，如果不是，不要做任何事情。最后字符串以`☺`结束。

这给了我一个快速查看我是否输入了前缀的方法。当你容易打字错误，或者当你在切换窗格或窗口时分心时，这很方便。

接下来，我想添加到这个状态左字符串。将`a`选项传递给`set`告诉 tmux 将这个字符串附加到前一个字符串，而不是覆盖它。

```
# are we zoomed into a pane?
set -ga status-left '#[bg=#44475a]#[fg=#ff79c6] #{?window_zoomed_flag, ↕ , }' 
```

Enter fullscreen mode Exit fullscreen mode

看起来和上面那个差不多，对吧。这一次，如果我缩放了一个窗格来占据整个窗口，我将显示一个箭头。否则，我会留下空白。

好吧！上至至`window-status`。

```
# window status
set-window-option -g window-status-style fg='#bd93f9',bg=default
set-window-option -g window-status-current-style fg='#ff79c6',bg='#282a36' 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我将默认的窗口状态设置为融合到状态栏的其余部分，并将活动窗口设置为紫色白字。事情变得越来越有趣。

```
set -g window-status-current-format "#[fg=#44475a]#[bg=#bd93f9]#[fg=#f8f8f2]#[bg=#bd93f9] #I #W #[fg=#bd93f9]#[bg=#44475a]"
set -g window-status-format "#[fg=#f8f8f2]#[bg=#44475a]#I #W #[fg=#44475a] " 
```

Enter fullscreen mode Exit fullscreen mode

这看起来类似于我们上面的，但是我们需要做一些样式技巧。我想用三角形来描绘我的窗户，就像 Vim Powerline 所做的那样。为了让这种风格发挥作用，我必须在``前后切换前景色和背景色。然后，为了显示窗口的名称，我使用了`#I`，它是`window_index`变量的缩写；以及`#W`，它是`window_name`的缩写

快好了！现在看起来应该很熟悉。

```
# status right
set -g status-right '#[fg=#8be9fd,bg=#44475a]#[fg=#44475a,bg=#8be9fd] #(tmux-mem-cpu-load -g 5 --interval 2) '
set -ga status-right '#[fg=#ff79c6,bg=#8be9fd]#[fg=#44475a,bg=#ff79c6] #(uptime | cut -f 4-5 -d " " | cut -f 1 -d ",") '
set -ga status-right '#[fg=#bd93f9,bg=#ff79c6]#[fg=#f8f8f2,bg=#bd93f9] %a %H:%M:%S #[fg=#6272a4]%Y-%m-%d ' 
```

Enter fullscreen mode Exit fullscreen mode

这里的区别在于，我使用括号来告诉 tmux 执行 bash 命令。第一个显示我的内存和 CPU 使用情况的摘要，第二个告诉我自上次重启以来有多长时间了。

## 结论

耶，德古拉主题！！！我仍在努力完善我的 tmux 配置。很多都是从其他人的 dot 文件中借来的，但我对我的德古拉主题很满意。现在我必须在我的命令提示符下做同样的事情！