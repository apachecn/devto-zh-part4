# 从 gnu-screen 切换到 tmux

> 原文：<https://dev.to/mrtnrdl/switching-from-gnu-screen-to-tmux-21ag>

在使用 linux(或任何一种类似 unix 的操作系统)几年后，大多数用户对命令行相当熟悉——并且不时地打开几个终端窗口/会话，只是为了避免不得不盯着另一个长时间运行的进程的输出。如果你发现自己经常处于这种状态，你会发现自己在寻找一种叫做[终端多路复用器](https://en.wikipedia.org/wiki/Terminal_multiplexer)的东西。这是一个工具，使您能够在 shell 会话中的多个终端窗格之间切换。太棒了，对吧？如果你需要一次看到几个东西，它们还可能提供像垂直或水平平铺这样的功能。

几年前，我开始使用`gnu-screen`作为我的复用器。不过最近我对此有点恼火。从一开始就困扰我的一件事是，`screen`这个名字让我很难通过在线搜索引擎找到相关信息。因此，正如我所期望的那样，找到我想要的信息几乎总是很乏味...我也越来越不喜欢的是，过一会儿我总是不得不打开同样的几个窗格。我通常有非常相似的工作负载，并习惯于将窗格标记为 *nmap* 、 *gobust* 或 *revshell* (例如)。
然后我在阅读[stack overflow](https://superuser.com/questions/440015/restore-tmux-session-after-reboot)上的这篇帖子时发现了`tmux-continuum`——是的。`tmux`。不是银幕——最终抛弃`gnu-screen`的冲动变得足够强烈。在读了几本切换指南后，我决定所涉及的工作并不太多，我会设法在午休时间内完成。我去争取了。

见下面我目前使用的`.tmux.conf`:

```
# set prefix to ctrl+a
unbind C-b
set -g prefix C-a

# toggling windows with ctrl+a ctrl+a
bind-key C-a last-window

# jump to the beginning of the line
bind a send-prefix

# don't rename windows automatically
set-option -g allow-rename off

# start with window number 1
set -g base-index 1

# Notifying if other windows has activities
setw -g monitor-activity on

# split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

# vim copy mode
bind P paste-buffer
bind-key -T copy-mode-vi v send-keys -X begin-selection
bind-key -T copy-mode-vi y send-keys -X copy-selection
bind-key -T copy-mode-vi r send-keys -X rectangle-toggle
bind -t vi-copy y copy-pipe "xclip -sel clip -i"

# statusbar
set -g status-position bottom
set -g status-justify left

# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'
set -g @plugin 'tmux-plugins/tmux-yank'

# tmux-continuum
set -g @continuum-restore 'on'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run -b '~/.tmux/plugins/tpm/tpm' 
```

这给了我(到目前为止)所需要的一切。从类似`vim`的复制和粘贴(如这篇博文中的[所示)到保存我打开的窗格，状态行和快捷方式，这些都深深根植于我的肌肉记忆中。](https://www.rushiagr.com/blog/2016/06/16/everything-you-need-to-know-about-tmux-copy-pasting-ubuntu/)

* * *

这篇文章以前发表在我的博客上。