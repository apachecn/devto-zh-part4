# 使用 Chromebook 进行 Web 开发:第 2 部分

> 原文：<https://dev.to/angelarae63/using-a-chromebook-for-web-development-part-2-4e9f>

上周早些时候，我写了我对谷歌在 Chromebook 上支持 Linux 的兴奋，以及我在受支持设备列表上搜索二手 chrome book 的经历。我的承诺是让你们都知道我的计划是如何实现的。
于是，我从百思买买了一台“开箱”的联想 Chromebook，型号 ZA270025US，150 美元。充电器不见了，否则就要 100 美元了。我能够在启用 Crostini 的设备列表中找到它[这里](https://www.reddit.com/r/Crostini/wiki/getstarted/crostini-enabled-devices)。检查。把它带回家，按照承诺在设备设置中启用 Linux...激动和检查。开始在 Linux 终端下载 Atom，但是...错误接踵而至！哦，不！

> 处理 atom 时出错-amd64 . deb(--install)
> 软件包体系结构(amd64)与系统(arm64)不匹配

没问题。我会找一个 arm64.deb 文件...
小时后...我从[负责人](https://code.headmelted.com/)那里得到了一个 arm64 版本的 VSCode 来开发我的 Chromebook。
原来这个联想里面的处理器是一个 MT8173，也在名单上。但是架构是 Arm64。我不知道我需要关心这个架构，除了，它是被支持的，但是没有那么多可用的应用程序。哼。
现在，我很好。我有一台运行 Linux 的 Chromebook，还有一个我喜欢的代码编辑器。但我失望的是，我似乎不能跑太多别的。我已经在 r/crostini 上发布了一个关于这个设备可以做什么的查询，所以我会继续给你更新。
如果你正在寻找一台运行 Linux 的 Chromebook，我会说，在列表中寻找一台，*和*确保应用程序随时可用；Amd64 和 x86-64 似乎是不错的选择。