# 使用 Smalltalk 从世界各地远程控制覆盆子 Pis！

> 原文：<https://dev.to/martinezpeck/remote-controlling-raspberry-pis-from-across-the-world-with-smalltalk-35nc>

你可能知道我和我的朋友 Gera 和 Javier [正在使用 Raspberry Pi、Python 和](https://github.com/gerasdf/carrillon) [VASmalltalk](https://www.instantiations.com/products/vasmalltalk/index.html) 开发一个自动化的钟楼(称为 Carrillon) 。

液体错误:内部

上周，Gera 在去布宜诺斯艾利斯机场乘飞机去拉斯维加斯的路上。通过聊天，他告诉我他忘记了他的 Pi:他想在旅途中的空闲时间做我们的项目。任何正常人都会在亚马逊上再订购一个或者等到回家。

但那是杰拉。所以他问我“你能让我用 SSH 访问你的 Pi 吗？”。当然，我周围有 3 或 4 个覆盆子，用我的 Eero 路由器很容易进行端口转发。我也没有 IP 主机名，客户端应用程序在我的 Mac 上刷新 IP。所以…对我来说，给他所有这些工具是相当容易的:外部 IP、SSH 访问和我的运行 Pi。

在这篇文章中，我写下了他想出的(并与我分享的)远程访问 Raspberry Pi GPIOs 的所有方法。需要特别注意的是，“偏远”并不意味着“拉斯维加斯”。从 Linux 开发机器上访问您的 Raspberry Pi GPIOs 甚至非常有用。

最后，作为这篇文章的先决条件，我建议你先阅读一篇解释 pigpio 和 VASmalltalk 基础的文章。

液体错误:内部

### 为远程 GPIO 设置树莓 Pi

第一件事是告诉 Raspbian 你允许远程 GPIO。您可以从命令行使用`sudo raspi-config`或使用如下所示的用户界面来完成此操作(请看底部):

[![](img/08fc9ac0420f9802e896811f39771312.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2e4_7VHj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-10-at-4.02.42-pm.png%3Fw%3D748)

最后，您必须在给定的端口上显式启动`pigpiod`守护进程。比如`sudo pigpiod 8888`。如果你想避免这种情况，你需要改变它，在启动时用类似`sudo systemctl enable pigpiod`的东西启动守护进程。对于这篇文章，我们开始手动操作，如上图所示。

### 设置您的 Linux 客户端机器

客户端机器也必须安装了`pigpio`库(就像 Pi 一样)。取决于你是哪个 Linux 发行版，它可能是一个简单的`sudo apt-get install pigpiod`类型的命令。如果没有这个包，那么你可以按照这里的[解释从源代码编译。您可以运行`sudo pigpiod -v`来验证库是否被正确安装。](http://abyz.me.uk/rpi/pigpio/download.html)

现在，在 VASmalltalk 使用的`abt.ini`文件中，在`[PlatformLibrary Name Mappings]`部分下，您必须添加这些行:

```
RaspberryGpio=libpigpio.so
RaspberryGpioDaemon=libpigpiod_if2.so
RaspberryGpioUltrasonicDaemon=libpigpioultrasonic.so 
```

Enter fullscreen mode Exit fullscreen mode

确保这些是正确的，并且操作系统可以“找到”。

### 将客户端 Linux 连接到远程 Pi

要从 Linux 机器连接到 Pi 机器，您基本上需要知道它的 IP 并有一个到它的连接。如果两者都在同一个内部网络中(并且中间没有防火墙或其他东西阻挡该端口)，那么您可能可以在 Smalltalk 中执行以下操作:

```
RaspberryGpioDaemonInterface defaultIP: 'XXX.XXX.XXX.XXX' andPort: '8888'. 
```

Enter fullscreen mode Exit fullscreen mode

其中`XXX.XXX.XXX.XXX`是您的 Pi 的内部 IP。

如果您想从本地网络外部连接，并且不想打开 8888 端口，您可以使用 SSH 隧道:

```
ssh -v -t -YC -p NNNN pi@'YYY.YYY.YYY.YYYY' -L 8888:127.0.0.1:8888 
```

Enter fullscreen mode Exit fullscreen mode

或者:

```
ssh -v -t -YC -p NNNN pi@'your.hostname.dns.whatever' -L 8888:127.0.0.1:8888 
```

Enter fullscreen mode Exit fullscreen mode

为了确认隧道正在工作，您可以在您的客户机 Linux 上运行`nc -v 0 8888`。您应该看到一条成功消息，然后在打开隧道的 SSH 控制台中看到一些调试信息。

一旦隧道稳定下来，你现在可以像这样从 Smalltalk 连接到 Pi:

```
RaspberryGpioDaemonInterface defaultIP: '127.0.0.1' andPort: '8888'. 
```

Enter fullscreen mode Exit fullscreen mode

### 看现场直播

下面是一段视频，展示了我和 Gera 之间的真实工作过程:

液体错误:内部

### 完整 Smalltalk 示例

下面是一个完整的例子，使用 SSH 隧道将客户端连接到远程 Raspberry Pi 并访问 IO 扩展器 I2C MCP23017 的 GPIO 引脚(我们将在以后的帖子中讨论这个问题:

```
| d bit0 bit1 bit2 bit3 |
RaspberryGpioDaemonInterface defaultIP: '127.0.0.1' andPort: '8888'.
pigpio := RaspberryGpioDaemonInterface raspberryGpioStart.
device := pigpio createI2cDevice: I2CDeviceGPIOMCP23017 slaveAddress: 16r20.

device allOutputs.

bit0 := GPIOPort on: device bit: 0.
bit1 := GPIOPort on: device bit: 1.
bit2 := GPIOPort on: device bit: 2.
bit3 := GPIOPort on: device bit: 3.

d := Delay forMilliseconds: 450.
[true] whileTrue: [
   bit0 pulseForMilliseconds: 500.
   d wait.
   bit1 pulseForMilliseconds: 500.
   d wait.
   bit2 pulseForMilliseconds: 500.
   d wait.
   bit3 pulseForMilliseconds: 500.
   d wait.
   bit2 pulseForMilliseconds: 500.
   d wait.
   bit1 pulseForMilliseconds: 500.
   d wait.
]. 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

能够从开发机器或世界上任何地方直接操作您的 Raspberry Pi 的 GPIOs，是一个非常有用的特性。谢谢 Gera 向我展示了如何做！