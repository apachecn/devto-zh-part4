# 用 Sparrow6 编写跨平台脚本

> 原文：<https://dev.to/melezhik/writing-cross-platform-scripts-with-sparrow6-4km0>

随着最近的变化[，Sparrow6](https://github.com/melezhik/Sparrow6) 允许创建与 Windows 和 Linux 兼容的脚本。

为什么人们需要它？

在我的日常工作中，我创建了许多脚本来节省我的时间。挑战在于我经常在 Linux 和 Windows 环境之间切换。
让一堆脚本在 Linux 上运行，并不意味着它们也能在 Windows 上运行。所以我最终要从头开始重写代码，这要花很多时间，是吗？

为什么不将这种**跨平台特性**引入**的初始设计**并编写兼容两种系统的脚本呢？

有了 Sparrow6，真的是小菜一碟。

* * *

# Hello Linux/Windows on sparrow 6

让我们创建一个简单的脚本，它只打印`Hello World`，但同时适用于 Linux 和 Windows。

Linux 版本:

`tasks/hello/linux/task.bash`

```
echo "Hello world, $(config name)" 
```

Enter fullscreen mode Exit fullscreen mode

我们基于这样一个事实，即 Bash 是大多数 Linux 系统上都可用标准 Linux shell。

Windows 版本:

`tasks/hello/windows/task.ps1`

```
Write-Host  "Hello world, $(config  name)" 
```

Enter fullscreen mode Exit fullscreen mode

我们再次基于 Powershell 是大多数 Windows 系统上可用的标准 Windows shell 这一事实。

这些例子太简单了，但是在现实生活中，您可以编写更复杂的脚本。我只想强调，您应该根据您的系统选择合适的 shell。Bash 或 Powershell

# 脚本切换

Sparrow6 擅长将事物粘合在一起，并编排脚本。我们只需要在 Perl6 上编写一个小的*钩子*，就可以在操作系统上有条件地运行我们的脚本:

`hook.pl6`

```
#!perl6

if os() eq 'windows' {
    run_task "hello/windows"
} else {
    run_task "hello/linux"
} 
```

Enter fullscreen mode Exit fullscreen mode

所以我们最终得到了一个可以运行的小型 Sparrow6 插件:

```
$ tree .

.
├── hook.pl6
└── tasks
    └── hello
        ├── linux
        │   └── task.bash
        └── windows
            └── task.ps1

4 directories, 3 files 
```

Enter fullscreen mode Exit fullscreen mode

在我的 Linux 机器上应该是:

`perl6 -MSparrow6::DSL -e 'task-run(".", %( name => "Raku" ))'`

```
02:32:16 09/25/2019 [.] Hello world, Raku 
```

Enter fullscreen mode Exit fullscreen mode

* * *

感谢您的阅读。如果你想要灵活而简单的自动化，试试[Sparrow](https://github.com/melezhik/Sparrow6)——我在日常工作中使用的工具。

一如既往地欢迎评论、问题和想法。