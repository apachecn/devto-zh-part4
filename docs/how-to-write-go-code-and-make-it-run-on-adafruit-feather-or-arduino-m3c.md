# 如何编写 Go 代码并使其在 Adafruit Feather 或 Arduino 上运行

> 原文：<https://dev.to/jimutt/how-to-write-go-code-and-make-it-run-on-adafruit-feather-or-arduino-m3c>

您是否知道可以将 Go 用于嵌入式系统(如 Arduino Uno 或 Adafruit Feather)？TinyGo 是一个轻量级的 Go 编译器，专门为这个目的而创建。尽管这个项目还很年轻，所以还存在一些尚未实现的怪癖和缺失的功能。

TinyGo 支持 Go 编程语言的子集，更详细的描述请参考[本页](https://tinygo.org/lang-support/)。尽管如此，该语言的许多部分已经得到了支持，例如 Goroutines 似乎工作得相当好。[文档](https://tinygo.org/)仍然不是很全面，AVR 编译(例如用于 Arduino Uno)在撰写本文时似乎还处于试验阶段。因此，我将在这篇文章中使用一个带 ARM 微控制器的[阿达果羽毛 M0](https://www.adafruit.com/product/2772) 板。

本指南将告诉您如何为 TinyGo 开发设置您的计算机，以及如何将一个简单的闪烁 LED 程序闪烁到板上。这些指令已经在 **Adafruit Feather M0** 板上进行了测试，但也适用于这两种板(如果编译`target`被更改为相应的板):

*   M0 ( `build -target=itsybitsy-m0`)
*   阿达果饰品 M0 ( `build -target=trinket-m0`)

我们将使用 UF2 引导加载程序，它使羽毛在引导加载模式下看起来像一个 USB 驱动器；允许对编译后的二进制文件进行简单的复制粘贴。然后羽毛将重启并加载程序。

### 先决条件

*   非常基础的围棋知识
*   知道如何使用 Arduino IDE 和羽毛板(安装 UF2 引导程序)
*   兼容的开发板

## TinyGo 安装

### Linux

下载并安装 TinyGo:

```
$ wget https://github.com/tinygo-org/tinygo/releases/download/v0.7.1/tinygo_0.7.1_amd64.deb

$ sudo apt-get install ./tinygo_0.7.1_amd64.deb 
```

将 TinyGo 添加到`PATH`(注销并再次登录以应用更改):在您选择的编辑器中打开`~/.profile`，并将下面一行添加到文件的末尾:

```
export PATH=$PATH:/usr/local/tinygo/bin 
```

### 窗口

如果你已经为 Linux (WSL)启用了 [Windows 子系统，你应该能够遵循上面的 Linux 说明。否则我推荐看 TinyGo 团队提供的 Docker 图片:【https://tinygo.org/getting-started/using-docker/](https://docs.microsoft.com/en-us/windows/wsl/install-win10)

### macOS

不幸的是，我不是 Mac 用户，所以我不能在这里提供任何帮助，但在 macOS 上的安装当然包括在官方文件中:[https://tinygo.org/getting-started/macos/](https://tinygo.org/getting-started/macos/)

## 配置 UF2 引导程序

UF2 引导加载程序消除了安装单独的闪存工具的需要(在这种情况下是 BOSSA cli)。相反，闪存是通过将文件放到可移动驱动器上来完成的。

### 1。进入引导程序模式

*   将羽毛 M0 板连接到计算机。
*   快速双击重置按钮-主板现在应该进入引导模式，红色内置#13 LED 应该开始淡入淡出。

现在检查您的计算机是否检测到了一个新的 USB 驱动器(在 Linux 上应该出现在`/media/{your_user}/`中)。如果是这样的话，你已经准备好了(双关语)，可以跳过下一部分！如果没有，你需要先安装 UF2 引导程序。

### 2。如有必要，安装引导程序

使用标准 Arduino IDE，将正确的 UF2 引导程序刷新到您的主板。这一步请参考 Adafruit 官方文档:[https://learn . Adafruit . com/installing-circuit python-on-samd 21-boards/installing-the-uf2-boot loader](https://learn.adafruit.com/installing-circuitpython-on-samd21-boards/installing-the-uf2-bootloader)

*如果你懒得看文档:在羽毛 M0 板上，你需要下载[这个草图文件](https://github.com/adafruit/uf2-samdx1/releases/download/v3.7.0/update-bootloader-feather_m0-v3.7.0.ino)，并通过 Arduino IDE 上传到板上*

**重要！确保为您的主板下载了正确的引导程序文件，否则您将面临崩溃的风险。**

## 构建并刷新/上传一个简单的程序

微控制器的经典“闪烁 LED”例子可能会被过度使用，也不是很花哨。由于这篇文章的主要目的是引起人们对 TinyGo 的注意，并展示如何开始，所以您将不得不等到以后来看更多有趣的用例。

### 1。创建目录和一个空的 Go 文件

创建一个名为“羽毛闪烁”的新目录。导航到目录并创建一个空的`feather-blink.go`文件。

### 2。让 LED 闪烁

将以下骨架添加到 feather-blink.go 文件:

```
package main

import (
     "machine"
     "time"
)

func main() {
} 
```

编译时将自动解析的“machine”导入允许访问硬件。`machine`封装中可用的 API 因所用的电路板而异。要确切地知道哪些类型、常量和方法是可用的，可以查看 [TinyGo 库](https://github.com/tinygo-org/tinygo/tree/master/src/machine)中的源代码。我认为这种类型的信息很可能会包含在以后的书面文档中。

*   通用 [machine.go](https://github.com/tinygo-org/tinygo/blob/master/src/machine/machine.go) 带有“Pin”类型的定义和方法
*   [board_feather-m0.go](https://github.com/tinygo-org/tinygo/blob/master/src/machine/board_feather-m0.go) -包含用于引脚绑定、UART、SPI 等的特定于电路板的常数。

为了使板上 LED 闪烁，我们可以通过已经为我们声明的`machine.LED`常量(`LED = D13`)来访问它。然后，我们将其配置为输出。在主循环中，我们使用`time`包中的`Sleep`函数在切换引脚之前添加一个延迟。

```
package main

import (
     "machine"
     "time"
)

func main() {
     led := machine.LED
     led.Configure(machine.PinConfig{Mode: machine.PinOutput})

     for {
          led.Low()
          time.Sleep(time.Millisecond * 1000)
          led.High()
          time.Sleep(time.Millisecond * 1000)
     }
} 
```

就是这样！现在，我们只需要构建程序并将其闪存到板上。

### 3。建立程序，并将其闪现在羽毛上

1.  将羽毛板连接到计算机，然后双击重置按钮将其置于引导模式。
2.  检查新 USB 驱动器的路径(在 Linux 上很可能是`/media/{your username}/FEATHERBOOT`)
3.  构建程序并将输出指向羽化设备

```
$ tinygo build -target=feather-m0 -o=/media/{your_user}/FEATHERBOOT/flash.uf2 feather-blink.go 
```

如果构建成功，Feather 现在应该会自动重启并运行程序。您应该能够通过检查 LED 是否闪烁来验证它，如代码中所指定的，而不是像在引导加载程序模式中那样渐强渐弱。

如果你想在主板上刷新一个新版本，你需要首先再次将它置于引导模式。

## 资源与延伸阅读

*   [TinyGo 网站](https://tinygo.org/)
*   TinyGo 在地鼠 slack 有自己的频道。在这里获得您的邀请:[https://invite.slack.golangbridge.org/](https://invite.slack.golangbridge.org/)
*   购买一个羽毛 M0 板:[https://www.adafruit.com/product/2772](https://www.adafruit.com/product/2772)(它有不同的版本，例如有一个带有微型 SD 卡插槽)
*   如果你是新手，你可能想看看这个互动的漫游:[https://tour.golang.org](https://tour.golang.org)