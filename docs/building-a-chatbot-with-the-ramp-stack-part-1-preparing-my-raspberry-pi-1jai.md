# 准备我的树莓派

> 原文：<https://dev.to/aninditabasu/building-a-chatbot-with-the-ramp-stack-part-1-preparing-my-raspberry-pi-1jai>

你可能会问，什么是斜坡堆栈？是我对 LAMP stack 的恶搞，其中 RaMP = RaspberryPi，MySQL，Python。

为什么是斜坡堆栈？因为我正在为我的个人笔记本电脑寻找一种低成本、低空间、轻重量的替代品，因为我想让机器人实验与我的工作笔记本电脑或个人笔记本电脑上的东西完全分开，因为在某个时候，我梦想着建造自己的类似 Alexa 的语音机器人...还因为我懂基本的 Python，会拿 SQL。

这里有一张照片向你展示了一只裸骨树莓可以有多小。[![](img/28ce73b9c42dce1c02e5905bd7b0f9b6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YXayxXpz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/idk8wg8wkbhmrviqe1e8.JPG)

这些是我尝试性的步骤:

1.  设置我的 RaspberryPi。
2.  设计和创建数据库。
3.  设计和创建机器人应用程序。

我既不是极客，也不是程序员；这一旅程并不容易。这篇文章是我与大家分享我的学习、失败和成功的系列文章的第一篇。在这些帖子中，我计划更多地关注聊天机器人的设计考虑(为什么)，而不是代码方面(如何)。

这篇文章是关于为我的实验准备一个 RaspberryPi。

一个 RaspberryPi 只是一个 CPU(主板)。要使用它，你需要一个电源(电源线)、一个输入设备(键盘)和一个输出设备(显示器？电视？).人们还需要将操作系统加载到 CPU 上，因此需要一个包含操作系统的外部存储设备(microSD 卡)。

我买了一个初学者工具包，跟着 YouTube 上的一个视频，指导我如何将 RaspberryPi 放入其官方外壳，然后环顾四周，将东西插入端口并开始使用。

新手错误。

入门套件没有 VGA 转 HDMI 适配器(我的显示器没有 HDMI 端口，只有 VGA)。

像我买的这样的初学者工具包是善意的，但可能包含我不需要的东西(也可能不包含我需要的东西)。谷歌告诉我，购买组合框或初学者工具包比购买单个组件更便宜，但我不同意。我需要的最基本的东西是:主板、电源和一个装载了 Raspbian 操作系统的 microSD 卡。如果我只买了这些和一个 VGA 转 HDMI 接口，我会省下 800 卢比。

> 所以，这里有一个购买指南给你(我假设你已经有一个显示器，一个 USB 键盘和一个 USB 鼠标)。
> 
> 1.  Buy these 2 items: RaspberryPi 3B+ motherboard and a 2.4 amp power adapter with microUSB plug. I bought Model 3B+; If you want, there is also a model 4.
> 2.  If you already have a spare microSD card that can be dedicated to RaspberryPi, go to the next step. If not, buy one, preferably with Raspbian OS pre-installed. Or, you can load Raspbian operating system from raspberrypi.org [on the card.](https://www.raspberrypi.org/downloads/raspbian/)
> 3.  See what port your TV or monitor has, and buy VGA to HDMI port or HDMI to HDMI port (RaspberryPi has HDMI port).

我购买了正确的显示器连接器，并插入了键盘、鼠标和显示器。然后，我四处寻找可以插入 microSD 卡的端口。

又一个新手失误。

microSD 端口在主板的反面；要拿到它，需要把主板翻过来。 [![](img/e17be18c09caa7132ea74b96b99316db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q58RYkhq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fqwxyk7q36ys9y8pr5xw.jpg) 
这意味着，我不得不拔掉我之前插上的所有插头(因为，我害怕被电线或其他东西钩住其中的一个插头)。

> 所以，下面是正确的插入组件的顺序:
> 
> 1.  Turn the motherboard over and insert the microSD card into the slot very slowly.
> 2.  Plug the keyboard and mouse into the USB interface.
> 3.  Connect the monitor to the HDMI port.
> 4.  If you want to access the Internet by cable, plug the LAN cable into the Ethernet port (I was going to use WiFi).
> 5.  Plug the power adapter into the microUSB port.
> 6.  Turn on the display. Then, turn on the power adapter (Google told me that the power adapter should be plugged in last and turned on last).

瞧啊。我的微型计算机闪着红光启动了。在屏幕上，我选择了 Raspbian OS，点击 **Install** ，按照屏幕上的指示操作，然后等待安装结束(差不多花了 15 分钟)。

Googling 还告诉我在安装包后更新它们，所以在安装结束后，我运行了以下命令:
`sudo apt-get update`，大约花了 10 分钟完成。
`sudo apt-get upgrade`，耗时约 15 分钟完成。

看起来我已经安装好电脑了。下一篇文章，我会谈到聊天机器人后端(这将是一个 MySQL 数据库)。

* * *

同时，由于这应该是我的超便携电脑，我会给它买一个 LCD 触摸屏显示器。

此外，由于 RaspberryPi 官方外壳切断了对 GPIO 连接器引脚的所有访问，我将为它购买一个保持引脚可访问的外壳。这些引脚需要插入 LCD 显示器，以及所有的物联网东西(假设我后来走那条路)。

而且，由于我不喜欢每次需要使用 microSD 卡时都必须翻转主板的想法，我还会给它买一个 USB microSD 读卡器。

* * *