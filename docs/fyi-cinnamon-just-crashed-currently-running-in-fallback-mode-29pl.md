# 仅供参考:肉桂刚刚崩溃。当前以回退模式运行

> 原文：<https://dev.to/vitalipom/fyi-cinnamon-just-crashed-currently-running-in-fallback-mode-29pl>

***在虚拟模式下运行真实的 VNC？Cinammon 刚刚在你最喜欢的 MintBox 机器上崩溃了？这是第一个适合你的教程！*T3】**

*嗨，
我有点紧张，因为我的试用期结束了，我联系了皇家 vnc 支持中心的杰克，他帮助我(更像是精神上的)调试我的 VNC 会话，但由于这通常是我能要求的最有帮助的一种帮助，我要说他给了我最多的帮助。
我正在调试我的 VNC 会议，因为我发现了 VNC 虚拟模式，我让它在我的本地局域网上工作，零滞后& &全分辨率！！！！！*

参考指南我写给 Jack 的下一个客户，我很想买那个，看看吧，订阅我的 dev.to 频道。(很简单，只是 twitter 作为登录):

```
Hi Jack,

You’re so much on time!

Actually I solved all. If you have further newbie customers with question like me, like I do here’s the solutions to everything:

Lags/delays - For lags, delays try free trial and buy enterprise. It’s 50/40EUR *a year* not per month. 

Use Virtual Mode once you do that, the default is not Virtual Mode. 

When using VIrtual Mode: 

All the guides on the internet states that Intel Drives do not work well. 

ERGHHH WRONG!!!

Just start $ vnc-virtual //***NOT ONLY THE DAEMON*** as sudo user. 

That will let you run your default windowing system such as Cinammon in normal mode. 

Mint users/XFCE with old XRandr Server drive: Do not worry, you can specify the resolution via same vnc-virtual -geometry flag.
Not stated in the help. 

When starting as root on display one say, unblock port 8901 on Firewall to access from your second computer. Block that port range on your router and forward only between your computers!!!

If you struggling with my same Access Denied question, just make AccessMethod as None or something like that (is within help of the vnc-virtuald (daemon) help cmd).

That’s it,
From now on when you connect you will have 0 lag as you connect directly via lan and you will enjoy full resolution. 

Thanks a lot Jack!!!!!

Regards,
Vitali 
```

Enter fullscreen mode Exit fullscreen mode

[https://youtu.be/7BBtHN6S22A](https://youtu.be/7BBtHN6S22A)