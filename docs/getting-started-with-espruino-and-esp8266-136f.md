# Espruino 和 ESP8266 入门

> 原文：<https://dev.to/vorillaz/getting-started-with-espruino-and-esp8266-136f>

我写过一系列关于使用 Johnny-Five 使用 JavaScript 进行嵌入式开发的[帖子](https://www.vorillaz.com/iot/)。

虽然它是一个很好的使用 Arduino 或其他流行微控制器编程的库，但是它缺少一个基本的特性，可移植性。
对于大多数受支持的平台，JavaScript 在运行 Node.js 的主机上执行。Johnny-Five 通过 USB 串行将基本 I/O 指令传输到连接的主板，USB 串行充当瘦客户端。基本上，我们可以通过执行命令来控制电路板，因此创建一个独立的原型是不可能的。

## 向埃斯普鲁诺问好

Espruino 是一个集成了 JavaScript 解释器的固件。它是由 Gordon Williams 创建和开发的，同时它还附带了一个 IDE 作为 Chrome 的扩展，一个生动的社区和大量的教程和示例。Espruino 更像是一个解释器，这意味着我们可以使用在目标板上编译和执行的 JavaScript。

## ESP8266 和 Espruino

在 Espruino 电子商店里，有几块板只需几英镑就能买到。但是您也可以使用任何其他可用的选项。您可以通过 ESP 板使用 Espruino 启动并运行。ESP8266 是一个很好的选择，因为它价格实惠，可扩展，并且有许多示例、教程和项目是用它构建的。

## 闪烁的 Espruino

出于这个简单教程的目的，我们将使用 NodeMcu ESP8266 Lua 板，它带有一个微型 USB 端口，用于电源、编程和调试，以及一组用于快速原型制作的焊接引脚。刷新 Espruino 是一个漫长的过程，可能会非常棘手，尤其是对于嵌入式开发的新手。幸运的是，我们可以使用 Flasher.js，这是一个命令行工具，可以轻松地刷新 Espruino。你可以在 Linux、Mac 或 Windows 上运行 Flasher.js，除了 NodeMcu 之外，它还支持许多其他的板。

## 下载安装 Flasher.js

你可以在 Flasher.js [发布页面](https://www.espruino.com/Download)找到你的操作系统的二进制文件，你可以下载并安装。您可能需要下载并安装一些额外的驱动程序，以允许 Flasher.js 与您的设备通信。在文档[页面](https://guides.thingssdk.com/flasher.js/compatible_devices.html)中还提供了每个设备和操作系统所需的驱动程序列表。

## 闪现你的设备

打开 Flasher.js 应用程序，将您的设备连接到您的开发机器。对于 NodeMcu ESP8266 Lua 板，您需要使用 USB 转 Micro USB 电缆。闪光器将尝试自动检测可用端口。在 Windows 上，它们看起来像`COM3`，而在 Mac 和 Linux 上，它们更像`/dev/ttyUSB1`或`/dev/cu.usbmodem150`。仔细检查端口是否正确，选择最新的 Espruino 二进制文件并点击 Flash 按钮。

当它闪烁时，您应该会看到您的板载 LED 闪烁。该过程完成后，您可以立即开始使用 Espruino 设备。

## 设置 Espruino IDE

前往 Espruino Web IDE(页面)[[https://www.espruino.com/Web+IDE](https://www.espruino.com/Web+IDE)]安装 Chrome 扩展应用程序。连接您的设备并启动应用程序。点击左上角的“连接”按钮，选择合适的端口。你直接连接到你的董事会。您可以使用 Web IDE 的控制台来验证您的开发板以及 Espruino 版本，如下所示:

```
~ print(JSON.stringify(process.env)); 
```

## 结论

使用 Espruino 进行嵌入式开发可以为 web 开发人员打开一个全新的世界。ESP8266 已经出现了一段时间，您可以立即开始原型制作。在接下来的一系列教程中，我将向您展示如何使用 Espruino 来创建大量有趣的实验和项目。

## 进一步资源

*   [在 ESP8266 开发板上安装 Espruino](https://www.youtube.com/watch?v=j9xwgeE9u8E)
*   [Espruino IDE 文档](https://www.espruino.com/Quick+Start+Code)

你也可以在[vorillaz.com](https://vorillaz.com/getting-started-espruino-esp8266)上找到这个帖子