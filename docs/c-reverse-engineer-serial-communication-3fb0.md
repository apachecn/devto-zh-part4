# C# -逆向工程串行通信

> 原文：<https://dev.to/oscar37921395/c-reverse-engineer-serial-communication-3fb0>

我有一个设备，当一个特定应用程序运行时，它会向该应用程序发送数据。我需要欺骗这些数据，并检索某些没有显示的信息。该器件是定制的，由于某种原因没有数据手册。因此必须嗅探通信并重建从应用程序发送到该设备的命令。所以我想用 C#或 C++来实现这一点。现在，我已经尝试了每一个嗅探软件，是的，他们嗅探它，但我想能够知道和检索数据，并提出它的生活。

最简单的方法是把你的软件放在设备和其他软件之间。接下来，回顾所有的数据，捕捉你所需要的。

为此，请使用串行端口环回软件。外面有很多。参见[http://virtual-serial-port.org](http://virtual-serial-port.org)。
[![VSPD](img/842d0598413ff3de2f3e2331e91cc94f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wo38vYD0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/wWgnHVc/Eltima-Virtual-Serial-Ports-Driver-XP-1.png) 
更复杂(但更被动！)方法是监视 IOCTLs。我不能指导你如何做我不知道的事情，但那是值得一看的。https://www.com-port-monitoring.com/[的软件](https://www.com-port-monitoring.com/)使用这种方法。
[![SPM](img/0b4b4b6f68de356486cb6b229cddd943.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oERlIyS8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/6mjWcky/3.jpg) 
另一款提供虚拟串口的软件是 com 0 com([http://com0com.sourceforge.net/](http://com0com.sourceforge.net/))。不过，它还有一个集线器功能，可以将设备输出发送到许多应用程序，这可能对您有用，也可能没用，具体取决于您是只需要监控设备输出还是双向输出。

如果你沿着设备过滤的路线走下去，它至少可以给你一些开源代码，作为你自己的嗅探器的基础。但如果你走这条路，如果你需要它在 64 位版本的 Windows 10 上工作，你可能需要看看驱动程序的签名。