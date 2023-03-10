# 一起使用 tmux 会话、窗口、窗格和 Vim 缓冲区

> 原文：<https://dev.to/nickjj/using-tmux-sessions-windows-panes-and-vim-buffers-together-5djk>

* *本文最初发布于 2019 年 6 月 18 日:[https://nickjanetakis . com/blog/using-tmux-sessions-windows-panes-and-vim-buffers-together](https://nickjanetakis.com/blog/using-tmux-sessions-windows-panes-and-vim-buffers-together)T3】*

* * *

现在我已经使用 Vim 几个月了，我想这是一个很好的时机来分享我的日常工作流程，告诉我如何在 tmux(我已经使用了多年的东西)中使用它。

在弄清楚这一切的时候，我偶尔会在选择启动多个终端应用程序、tmux 会话、窗口、分割窗格、Vim 缓冲区和/或标签之间挣扎。这是我最后想出来的，到目前为止效果很好。

作为一名自由开发人员、开源爱好者和喜欢在自己的项目上做黑客的人，我有很多活跃的项目并不奇怪。

这个 10 分钟的视频展示了我如何在命令行上管理所有这些项目。

### 展示其工作原理的演示视频

[https://www.youtube.com/embed/hbs7tuwpgZA](https://www.youtube.com/embed/hbs7tuwpgZA)

#### 带时间戳的目录

*   1:14–列出几个 tmux 会话
*   1:43–每个项目有 1 次 tmux 会议
*   2:22–连接到特定会话
*   2:33–使用 Vim 缓冲区分割单个文件
*   3:35–使用 tmux 窗口将 Vim 从 web 服务器中分离出来
*   3:47–用 tmux 窗格将窗口分成两半
*   4:01–使用 Vim 中的选项卡将缓冲区分成不同的组
*   5:32–利用第二个 tmux 窗口运行其他进程
*   6:21–使用 tmux 放大和缩小特定窗格
*   6:53–概述开发和部署我的博客的工作流程
*   8:03–在不同项目/会议之间切换是 tmux 的亮点
*   9:43–使用 tmux-resurse 保存、保存和恢复会话

#### 参考链接

*   [对 tmux](https://nickjanetakis.com/blog/who-else-wants-to-boost-their-productivity-with-tmux) 的介绍
*   [https://github.com/tmux 插件/tmux-resurrect](https://github.com/tmux-plugins/tmux-resurrect)
*   [https://github.com/nickjj/dotfiles](https://github.com/nickjj/dotfiles)
*   [https://github.com/mintty/wsltty](https://github.com/mintty/wsltty)

**你最喜欢的 tmux / Vim 工作流程是什么？下面让我知道！**