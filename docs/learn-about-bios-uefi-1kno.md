# 了解计算机 BIOS/UEFI

> 原文：<https://dev.to/hacback17/learn-about-bios-uefi-1kno>

询问某人是什么控制了他们的电脑？
通常，一个非 IT 人员会说是 Windows 控制了他们的系统。但是他们不明白的是，当我们启动一台机器时，在我们接触到那个软件之前，有一种叫做**固件**的东西在控制系统。

有两种类型的固件。

*   基本输入输出系统
*   UEFI(统一可扩展固件接口)UEFI 是 BIOS 的高级版本，拥有比旧 BIOS 更多的功能。

所以你的 BIOS 或者你的 UEFI，这些是关键的固件指令，当你的电脑启动时，它们将真正控制你的电脑。

BIOS/UEFI(程序)存储在称为 EEPROM(电可擦除可编程只读存储器)的芯片中。

[![](img/632d916834959519f9800d97eb212a08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CaAlFuFj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rk704t1vyp8fri8tckw0.jpg)

我们使用 CMOS(互补金属氧化物半导体)芯片与 EEPROM 交互。
CMOS 芯片有一个正在供电的电池。使用 CMOS 芯片，我们可以改变系统的行为，或者我们可能希望对系统进行一些诊断，可能是设备的温度，或者我们可能希望检查风扇速度，确保它们正常工作。

[![](img/6ed104ef9f390eb78f65d27f6e340a35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N9NnyUzv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ga2awuciqzoxmkoegt52.jpg)
[![](img/5012ad26faf42aa5c63676b38c51078a.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--j5c084_9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b210ctbwzwamf4itaurh.jpg)

现在，当我们进行一些更改时，这些更改需要存储在某个地方；它们存储在 NVRAM(非易失性随机存取存储器)中。这意味着它失去了电力，这些设置将被保存。

[![](img/13f6bd0f02891ab93ec9fb0727823466.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LCANoOGo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v7ipt6vdalecs0f119v9.jpg)

关键点:

*   BIOS/UEFI 驻留在 EEPROM 中。
*   我们可以使用 CMOS 芯片更改 BIOS 设置(也称为刷新 BIOS)。此后，更改会保存在 NVRAM 中。

BIOS 也可以做一些内置诊断；开机自检后；当你打开电脑以确保一切正常的过程。

BIOS 可以帮助监控我们系统的健康状况。比如:

*   温度
*   风扇速度
*   身份证明
*   速度
*   电压
*   时钟
*   总线速度(事物在系统电路中的通信速度；事物从一个组件转移到另一个组件的速度。)

点击阅读更多关于 UEFI [的信息。](https://www.ajsnetworking.com/uefi-unified-extensible-firmware-interface/)

暂时就这样吧！

那有帮助吗？请分享给你的朋友和敌人。