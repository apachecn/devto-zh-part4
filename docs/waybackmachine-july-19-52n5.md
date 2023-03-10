# 时光倒流机 2019 年 7 月

> 原文：<https://dev.to/wideawakening/waybackmachine-july-19-52n5>

> ...因为回到过去并回忆起你那时学到的东西总是很有趣的...

# CLI

我从没想过我会更喜欢反向搜索，但 fzf 是一个很好的组合

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [朱奈根](https://github.com/junegunn) / [ fzf](https://github.com/junegunn/fzf)

### 🌸命令行模糊查找器

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![fzf - a command-line fuzzy finder](img/2f2f81accc635a6d47ba388ab12b1af0.png) ](https://raw.githubusercontent.com/junegunn/i/master/fzf.png) [ ![travis-ci](img/e11ae301d422b983e400e2668b5b49d5.png)](https://travis-ci.org/junegunn/fzf)

fzf 是一个通用的命令行模糊查找器。

[![](img/c44e1a87316eba497607ff3ad3af1f23.png)](https://raw.githubusercontent.com/junegunn/i/master/fzf-preview.png)

这是一个交互式的 Unix 命令行过滤器，可用于任何列表；文件、命令历史、进程、主机名、书签、git 提交等。

## 赞成的意见

*   可移植，无依赖性
*   惊人的快
*   最全面的功能集
*   灵活布局
*   含电池
    *   Vim/Neovim 插件、按键绑定和模糊自动完成

## 目录

*   [安装](https://raw.githubusercontent.com/junegunn/fzf/master/#installation)
    *   [使用自制软件或 Linuxbrew](https://raw.githubusercontent.com/junegunn/fzf/master/#using-homebrew-or-linuxbrew)
    *   [使用 git](https://raw.githubusercontent.com/junegunn/fzf/master/#using-git)
    *   [使用 Linux 包管理器](https://raw.githubusercontent.com/junegunn/fzf/master/#using-linux-package-managers)
    *   [窗户](https://raw.githubusercontent.com/junegunn/fzf/master/#windows)
    *   [作为 Vim 插件](https://raw.githubusercontent.com/junegunn/fzf/master/#as-vim-plugin)
*   [升级 fzf](https://raw.githubusercontent.com/junegunn/fzf/master/#upgrading-fzf)
*   [建筑 fzf](https://raw.githubusercontent.com/junegunn/fzf/master/#building-fzf)
*   [用途](https://raw.githubusercontent.com/junegunn/fzf/master/#usage)
    *   [使用取景器](https://raw.githubusercontent.com/junegunn/fzf/master/#using-the-finder)
    *   [布局](https://raw.githubusercontent.com/junegunn/fzf/master/#layout)
    *   [搜索语法](https://raw.githubusercontent.com/junegunn/fzf/master/#search-syntax)
    *   [环境变量](https://raw.githubusercontent.com/junegunn/fzf/master/#environment-variables)
    *   [选项](https://raw.githubusercontent.com/junegunn/fzf/master/#options)
    *   [演示](https://raw.githubusercontent.com/junegunn/fzf/master/#demo)
*   [例题](https://raw.githubusercontent.com/junegunn/fzf/master/#examples)
*   [`fzf-tmux`剧本](https://raw.githubusercontent.com/junegunn/fzf/master/#fzf-tmux-script)
*   [命令行的按键绑定](https://raw.githubusercontent.com/junegunn/fzf/master/#key-bindings-for-command-line)
*   【bash 和 zsh 的模糊完成
    *   [文件和目录](https://raw.githubusercontent.com/junegunn/fzf/master/#files-and-directories)
    *   [进程 id](https://raw.githubusercontent.com/junegunn/fzf/master/#process-ids)
    *   [主机名](https://raw.githubusercontent.com/junegunn/fzf/master/#host-names)
    *   [环境变量/别名](https://raw.githubusercontent.com/junegunn/fzf/master/#environment-variables--aliases)
    *   [设置](https://raw.githubusercontent.com/junegunn/fzf/master/#settings)
    *   [支持的命令](https://raw.githubusercontent.com/junegunn/fzf/master/#supported-commands)
    *   [自定义模糊完成](https://raw.githubusercontent.com/junegunn/fzf/master/#custom-fuzzy-completion)
*   [Vim 插件](https://raw.githubusercontent.com/junegunn/fzf/master/#vim-plugin)
*   [高级主题](https://raw.githubusercontent.com/junegunn/fzf/master/#advanced-topics)
    *   [性能](https://raw.githubusercontent.com/junegunn/fzf/master/#performance)
    *   [执行外部程序](https://raw.githubusercontent.com/junegunn/fzf/master/#executing-external-programs)
    *   [重新加载候选列表](https://raw.githubusercontent.com/junegunn/fzf/master/#reloading-the-candidate-list)
        *   [1。通过按 CTRL-R](https://raw.githubusercontent.com/junegunn/fzf/master/#1-update-the-list-of-processes-by-pressing-ctrl-r) 更新进程列表
        *   [2。按 CTRL-D 或 CTRL-F](https://raw.githubusercontent.com/junegunn/fzf/master/#2-switch-between-sources-by-pressing-ctrl-d-or-ctrl-f) 在信号源之间切换
        *   [3。交互式 ripgrep 集成](https://raw.githubusercontent.com/junegunn/fzf/master/#3-interactive-ripgrep-integration)
    *   [预览](https://raw.githubusercontent.com/junegunn/fzf/master/#preview-window) …

</article>

[View on GitHub](https://github.com/junegunn/fzf)

**2)** [nGrok](https://ngrok.com) + [使用 nGrok](https://diegocarrasco.com/how-to-create-a-ssh-tunnel-with-ngrok-on-ubuntu-server-aka-ssh-access-to-your-local-server-in-local-network-with-dynamic-ip/) 快速访问任何 ssh 机器

如果你需要从一个公共环境中公开/访问你的本地服务，快速且不需要太多的配置(动态 ip)，这就是你的工具。

> ngrok 提供了一个实时的 web UI，您可以在其中检查通过您的隧道运行的所有 HTTP 流量。只需一次点击，即可重放针对您的隧道的任何请求

编辑 2019/11/04:nGrok 还有更多选项，比如

*   [Serveo](https://serveo.net)

* * *

# 备忘单

[![Security attacks per layer](img/5c1d857fb3bb791c2ac5dae39bbf3232.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y50D3AFZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/EAgPA4NXYAEMBLk%3Fformat%3Djpg%26name%3Dlarge)

[![Unix performance tools](img/22976745ff6740dc5fa896e628c03d20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5p1vausi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/EAjqTXKXkAArD9D%3Fformat%3Djpg%26name%3D4096x4096)

* * *

# 代码/J2EE

**1)** 应该已经知道`Future`的存在了 [`CountDownLatch`](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/CountDownLatch.html) 的处理程序...让代码爱好者 x)

[![CountDownLatch threads explanation](img/fc06ee4dfd2b221acfa9cde0eff39809.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--APWXzt6i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.postimg.cc/YCypwwsk/Countdown-Latch-example.png)

**2)** 用`ShutdownHooks`
T1】优雅地关闭 Java 线程

* * *

# 手表

*   @flipper83(西班牙语)的一页产品设计如何让你的所有团队都保持同样的心态，以实现当前的目标
    *   使用可视化指南，如信息图、图表、大图片和通用术语表...让他们尽可能容易联系到，最好是在办公室里

[https://www.youtube.com/embed/9wVJHZEeTY0](https://www.youtube.com/embed/9wVJHZEeTY0)

* * *

# 采用

由于我的 TOC 本性，我很久以前就有了自己的`git commit message`约定，但是很高兴知道已经有关于它的文章[常规提交](https://www.conventionalcommits.org)——git 提交消息的标准模板

* * *

# w00t？

*   The shebang(！)

> 在 UNIX shell(用户界面)用户中，shebang 是“#”的代名词必须在脚本第一行开始的字符。在乐谱中，一个“#”被称为一个升半音和一个感叹号-"！"-有时被称为爆炸。这样，shebang 就变成了 sharp-ban 的缩写

* * *

# 月钩

[https://open.spotify.com/embed/track/3EK4tGkSiO5xvvB5sM4tln](https://open.spotify.com/embed/track/3EK4tGkSiO5xvvB5sM4tln)