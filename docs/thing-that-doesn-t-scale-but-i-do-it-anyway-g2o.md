# 无法扩展的东西，但我还是做了

> 原文：<https://dev.to/thassio__/thing-that-doesn-t-scale-but-i-do-it-anyway-g2o>

[日志](https://getlifeyoudesire.com/20-bullet-journal-hacks/)。尝试了很多。我使用了各种开源项目、Android 应用程序，用 Javascript、Python 和 Go 构建了我自己的脚本，还有那些[小声音告诉我去做的](https://imgur.com/gallery/aXPas)的所有
好事情，比如“与那个
服务同步”、“使用那个数据库”、“你可以用这种方式让事情更解耦”等等。所有这些都奏效了。我的日志有多少条目？**无**。我实际上构建的东西一出现在文件中就变成了垃圾。

不过，这行得通:

```
#!/bin/bash
nvim + +put_ +'r!date' +put_ +startinsert /my/home/my/file.md 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将该命令翻译为:

*   转到文件的末尾，
*   新系列，
*   添加当前日期、
*   新系列，
*   让编辑器处于插入模式。

把它放在一个文件里，叫做`tj`，给它加了一点`chmod +x`酱，现在我的思想就很好地储存在一个文件里了。我实际上几乎每天都在使用它。`nvim`是给[尼奥维姆](https://neovim.io/)用的，顺便说一句。

随着时间的推移，`file.md`变成了这样:

```
Mon 24 Jun 2019 11:23:34 PM -03
this is the first

Mon 24 Jun 2019 11:26:45 PM -03
now the second

Mon 24 Jun 2019 11:26:56 PM -03
you get the idea 
```

Enter fullscreen mode Exit fullscreen mode

## 奖金-i3 中一个花哨的“弹出”缺口

我用了 [`i3-gaps`](https://github.com/Airblader/i3) ，想让事情“更有趣”，所以在我的`~/.i3/config` :
里加了两行

```
for_window [class="^floatingVim$"] floating enable
bindsym $mod+q exec "termite -e tj --class floatingVim" 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我按下`Super+q`时，一个新的 [`termite`](https://github.com/thestinger/termite) 窗口启动，作为一个浮动的
小方块在我的屏幕中间，有`tj`为我写
做好准备。