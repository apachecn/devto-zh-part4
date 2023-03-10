# 树莓派 4:它是什么，怎么用？

> 原文：<https://dev.to/jasonre42705235/raspberry-pi-4-what-is-it-and-how-can-you-use-it-n59>

Raspberry Pi 是由 Raspberry Pi 基金会在英国开发的一系列小型单板计算机，旨在促进学校和发展中国家的基础计算机科学教学。最初的模型变得比预期的更受欢迎，在目标市场之外销售，用于机器人等用途。不包括外围设备(如键盘和鼠标)和机箱。但是，有些配件是以几个官方和非官方捆绑包装的。

树莓 Pi 4 有很多改进。树莓派背后的组织由两部分组成。树莓 Pi 基金会开发了前两个模型。Pi 模型 B 推出后，基金会成立了树莓 Pi Trading。当时，Eben Upton 是首席执行官。此外，基金会开发了第三种模式，即 B+Raspberry Pi Trading 负责开发技术，而该基金会是一个教育慈善机构，旨在促进学校和发展中国家的基础计算机科学教学。

树莓 Pi 4 有什么新功能？双显示器(4K)支持:有了这个功能，你现在可以将两台显示器连接到这台台式电脑上，在 4K 也是如此。
更好的性能:新的 Raspberry Pi 4 配备了新的、更新的处理器和 RAM，保证您获得全新的桌面体验。
快速联网:Pi 4 配备了千兆以太网，以及板载无线网络和蓝牙。
RAM: Raspberry Pi 4 基于 RAM 有三种版本:1 GB、2 GB 和 4 GB。
USB 3:新的 Raspberry Pi 4 升级了 USB 容量:除了两个 USB 2 端口，你会发现两个 USB 3 端口，可以将数据传输速度提高十倍。
技术规格
博通 BCM2711、四核 Cortex-A72 (ARM v8) 64 位 SoC @ 1.5GHz
1GB、2GB 或 4GB LPDDR4-2400 SDRAM(视型号而定)
2.4 GHz 和 5.0 GHz IEEE 802.11ac 无线、蓝牙 5.0、BLE
千兆以太网
2 个 USB 3.0 端口；2 个 USB 2.0 端口。
Raspberry Pi 标准 40 针 GPIO 接口(完全向后兼容以前的主板)
2 × micro-HDMI 端口(最高支持 4kp 60)
2 路 MIPI DSI 显示端口
2 路 MIPI CSI 摄像头端口
4 极立体声音频和复合视频端口
H.265 (4kp60 解码)、H264 (1080p60 解码、 1080p30 编码)
OpenGL ES 3.0 显卡
用于加载操作系统和数据存储的 Micro-SD 卡插槽
通过 USB-C 连接器的 5V DC(最低 3A*)
通过 GPIO 接头的 5V DC(最低 3A*)
启用以太网供电(PoE)【需要单独的 PoE 帽】
工作温度:0–50 摄氏度环境温度

*   如果下游 USB 外设的总功耗低于 500mA，则可以使用高质量的 2.5A 电源。

设置 Raspberry Pi 4
您将需要什么

电源:设备中包含一个 USB Type-C 端口。你需要一个至少 3.0 安培的电源。

微型 SD 卡:你需要用它来存储文件和 Raspbian 操作系统。最低存储要求为 8 GB。许多卖家提供预装 Raspbian 操作系统的 micro-SD 卡，所以您可以随时使用。

键盘和鼠标:要开始使用 Raspberry Pi 4，你需要一个 USB 键盘和一个 USB 鼠标。这两项对于首次安装都是必需的。设置好之后，还可以使用蓝牙键盘鼠标。

电视/电脑屏幕:要显示电脑内部的内容，你需要一个电视或电脑屏幕，以及一根连接 Raspberry 和显示器的线缆。如果您的显示器上安装了扬声器，Pi 4 将使用它。这里需要的端口是一个微型 HDMI 端口。如果您有不同的电缆，如标准 HDMI、DVI 和 VGA 电缆，那么您将需要它们各自的微型 HDMI 转换器。例如，如果你的显示器支持 VGA，那么使用 VGA 转 micro-HDMI 适配器。这样你就可以使用你的 VGA 线了。

这些是设置 Raspberry Pi 4 之前需要的基本要求。您还可以安装保护套、耳机/扬声器和以太网电缆等附件。

设置 SD 卡

如果你的 SD 卡没有 Raspbian 操作系统，你可以自己做。你所需要做的就是使用一台安装了 SD 卡插槽的笔记本电脑。大多数最新的笔记本电脑都有这个功能。

通过 NOOBS 下载 Raspbian 操作系统

使用 NOOBS 安装 Raspbian 操作系统是最简单的方法。

最重要的是，想要安装特定操作系统的高级用户应该参考本指南来安装操作系统映像。

将 NOOBS 下载并传输到 micro-SD 卡的步骤

访问覆盆子下载页面。然后点击写有 NOOBS 的盒子。

有关本文的更多详细信息，请阅读这里:-[https://codersera . com/blog/raspberry-pi-4-overview-specs-uses/](https://codersera.com/blog/raspberry-pi-4-overview-specs-uses/)