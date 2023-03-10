# xtrlock

> 原文：<https://dev.to/nibalizer/xtrlock-1ebf>

展台是 devrel 生活的一部分。当我启动时，我喜欢把我的笔记本电脑放在那里，打开一个很酷的项目或演示。如果幸运的话，还会有一台电视或显示器供我连接。

人们会过来和我聊天，问我问题。那太好了，这也是我来这里的原因。但是当我离解锁的电脑一两步远时，我会感到不安。我也不想关闭或锁定我的电脑。卡座里的锁屏看起来怪怪的。如果我的笔记本电脑合上了，告诉别人“让我演示给你看”会很烦人然后我们都等了几秒钟，直到我重新连接上 wifi/ssh/什么的。

输入`xtrlock(1)`。这个实用程序至少从 1993 年就存在了，并且是已经被遗忘的旧 Unix/X11 工具的一个奇妙生态系统的一部分。

来自手册页:`Lock X display until password supplied, leaving windows visible`。这将锁定输入屏幕，但让我打开的任何内容都可见。这意味着幻灯片、终端或演示仍在显示，我放心了，我不会受到影响。

实际上，`xtrlock`立即生效，不能延迟。所以我实际上做的是键入`sleep 5; xtrlock`并使用五秒钟的睡眠来隐藏/最小化写这个命令的终端。

我展示了各种各样的东西，但有一件事我想做的是拥有基于终端的演示脚本，它可以在没有我的交互的情况下运行。与视频不同，它们是在真实的基础设施上运行的，如果需要的话，我可以`^C`脚本并与一名与会者一起深入研究演示。回购在这里是