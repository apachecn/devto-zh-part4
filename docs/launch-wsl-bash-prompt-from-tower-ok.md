# 从塔上启动 WSL bash 提示符

> 原文：<https://dev.to/xpirit/launch-wsl-bash-prompt-from-tower-ok>

[![Launch WSL bash prompt from Tower](img/ee6f38c86c51b6ceaae1fa6d39b043bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4nnupk1p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jessehouwing.net/conteimg/2018/12/19340354430_4329b4584e_k-1-.jpg)

当您从 Tower 启动一个终端时，它会启动一个包含的 MingW bash shell。既然 Windows 10 附带了 Linux 的 Windows 子系统，那么使用 bash shell 就更好了。

[我第一次尝试推出 WSL 的尝试悲惨地失败了](https://superuser.com/questions/1380488/opening-a-wsl-shell-from-another-windows-application/1380499#1380499)。看起来 Windows 文件系统重定向导致看不到`wsl.exe`或`bash.exe`，因为 Tower 是一个 32 位程序。

经过多次尝试后，我发现可以使用`sysnative`文件系统重定向点来逃离 32 位世界:

标题:Bash (WSL)

路径:`C:\WINDOWS\sysnative\wsl.exe`

论据:

[![Launch WSL bash prompt from Tower](img/3bb9d3c4a6c98e0b2450fe06af48bf48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2ei3tGgC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jessehouwing.net/conteimg/2018/12/image.png) 

<figcaption>从塔上启动 Windows Linux 子系统</figcaption>

[![Launch WSL bash prompt from Tower](img/536fee129bacb247591d747f70d3661a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--blQtXDFc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://jessehouwing.net/conteimg/2018/12/launch-ps-wsl.gif) 

<figcaption>从塔上启动 Linux 的 Windows 子系统 bash 提示符</figcaption>