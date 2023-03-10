# 一个用于暂时停止系统的 Linux 内核模块

> 原文：<https://dev.to/satorutakeuchi/a-linux-kernel-module-for-stopping-the-system-for-a-while-3i7n>

# 前言

本文介绍`stop-machine` Linux 内核模块。它用于暂时停止系统。这是一个[开源软件](https://github.com/satoru-takeuchi/stop-machine)，它的用法写在它的`README.md`文件中。

`stop-machine`模块停止整个系统五秒钟。这里的`stop`不仅指所有进程不能运行，还指所有内核函数不能运行。
所有的内核功能都包括设备驱动和相应的中断处理程序。所以从用户的角度来看，所有的存储 I/o 和网络 I/o 都停止了。像键盘和鼠标这样的用户界面设备也不能工作。

请注意，通过用户空间程序来模拟整个系统的停止是非常困难的。

# 用例

该模块可用于确认系统是否耐受意外冻结。这比重置机器要方便得多。

您可以通过修改 [`STOP_MSECS`值](https://github.com/satoru-takeuchi/stop-machine/blob/master/stop-machine.c#L9)来改变以毫秒为单位的停止时间。
例如，如果您将该值(默认为 5000)修改为 60000，则该模块会将整个系统停止一分钟。

# 确认本模块的效果

让我们确认这个模块是否可以停止整个系统。首先，请打开一个不同于加载此模块的终端模拟器。然后发出以下命令，在以前的终端模拟器上每秒打印一次当前时间。

```
$  for ((;;)) ; do sleep 1 ; date -R ; done
Sat, 04 May 2019 09:40:27 +0900
Sat, 04 May 2019 09:40:28 +0900
Sat, 04 May 2019 09:40:29 +0900
Sat, 04 May 2019 09:40:30 +0900
Sat, 04 May 2019 09:40:31 +0900 ... 
```

Enter fullscreen mode Exit fullscreen mode

然后在后面的终端仿真器上加载这个模块。

```
$ sudo insmod stop-machine.ko
$　　　　　　　　　　　　　　　　　# You can see this prompt after five seconds 
```

Enter fullscreen mode Exit fullscreen mode

在显示提示之前，您的输入被忽略，显示看起来冻结。但是，之后系统照常工作。

然后回到以前的终端模拟器。您可以看到，在加载该模块后，该消息被跳过了五次。在下面的输出中，它介于(1)和(2)之间。

```
... Sat, 04 May 2019 09:44:38 +0900
Sat, 04 May 2019 09:44:39 +0900
Sat, 04 May 2019 09:44:40 +0900
Sat, 04 May 2019 09:44:41 +0900
Sat, 04 May 2019 09:44:42 +0900
Sat, 04 May 2019 09:44:43 +0900 Sat, 04 May 2019 09:44:44 +0900 #  ... (1)
Sat, 04 May 2019 09:44:49 +0900 #  ... (2)
Sat, 04 May 2019 09:44:50 +0900
Sat, 04 May 2019 09:44:51 +0900 ... 
```

Enter fullscreen mode Exit fullscreen mode

最后，如下卸载这个模块并关闭两个终端模拟器。

```
$ sudo rmmod stop-machine
$ 
```

Enter fullscreen mode Exit fullscreen mode

# 本模块是如何实现的

这个模块使用了一个名为`stop_machine().`的内核函数。

*   运行调用者给出的处理程序，停止所有内核、用户空间进程和硬件中断。
*   第一个参数是上面提到的处理程序。
*   第二个参数在被调用时被传递给处理程序。
*   第三个参数意味着应该停止哪些 CPU。在这里设置 NULL 意味着这个函数停止所有可用的 CPU。

该功能最初用于运行非常重要的功能，不会被任何其他功能中断。CPU 热插拔特性就是该功能的用户之一。

在`stop-machine`模块中，`stop_machine()`的处理程序浪费了五秒钟的 CPU 时间。很简单！

如果你对`stop_machine(),`的实现感兴趣，请参考 Linux 内核的源代码。定义在`include/linux/stop_machine.h`中，实现在`kernel/stop_machine.c.`