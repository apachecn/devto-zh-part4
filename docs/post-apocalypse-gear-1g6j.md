# 后启示录装备

> 原文：<https://dev.to/terceranexus6/post-apocalypse-gear-1g6j>

自从我发表关于硬件的文章已经有一段时间了。好吧，我们从头开始。不久前，我看了一个关于建立一个[后启示录背包](https://www.youtube.com/watch?v=2YbYfAH6ZRo)的视频，我很喜欢。这让我想起了我的一个大学项目。你看，我有一个非常酷的老师，一个网络教授，他喜欢电子游戏和角色扮演。他会为课程项目编一个故事，其中学生是人类工程学的最后希望，你必须黑掉邪恶的机器、坠落的无人机和其他物品，以建立一个后启示录网络并黑掉敌人。(我知道我知道，你是如此嫉妒我的网络课)我很久以前就已经学过了，但我想“嘿，为什么不写一个增强版，一个开放的硬件全套设备”？

好吧好吧，我们开始吧。我们已经想到了太多的场景，在这些场景中，人类被机器奴役，因为我们无法控制创造一个可能会杀死我们所有人的超级智能人工智能的意愿。可能是这样，但是我们有能力去破坏我们的世界，所以我首先想到的是“污染”。我甚至认为自己(就像在现实世界中一样)开始在早上戴某种口罩，因为我工作的城市*几乎*收回了一项空气污染预防计划。即使他们没有，污染水平率被官方标记为“中等”。在后启示录的场景中，我们想象着，空气已经相当糟糕了。所以第一个项目:

## 1。增强型防污染口罩

我发现[这篇文章](https://innotechfestival.com/best-anti-pollution-mask-reviews/)是关于今年最佳防污染口罩的评论(顺便说一句，这让我非常专注)，我认为我们需要一些东西，让我们可以轻松地在上面添加一些硬件，可以重复使用(所以，可清洗)，并最终选择了两个:Vogmask Chakra VMCV 介质(可重复使用，不可更换过滤器，容易缝在上面，使用 3 到 4 个月，有点贵)和军用级 N99 可清洗呼吸器(可重复使用，使用可更换过滤器，容易缝在上面，使用时间更长，比另一个更便宜)。现在，让我们来看看硬件。
e
我的选择是 [arduino Nano](https://thepihut.com/products/dfrduino-nano-v3-1-arduino-nano-compatible) 因为我喜欢它，我用它做几乎所有的事情，还有[这个带蓝牙的版本](https://thepihut.com/products/bluno-nano-an-arduino-nano-with-bluetooth-4-0)，我还没有试过，但我很想试试。其他不错的选择还包括 [Arduino pro mini](https://thepihut.com/products/adafruit-arduino-pro-mini-328-3-3v-8-mhz) (这最后一个只支持 3.3V)，或者，如果你喜欢冒险，我曾经尝试过的 [Arduino Beatle](https://thepihut.com/products/beetle-the-smallest-arduino) ，这是由于来自 DFRobot 的朋友为我提供了一个免费的样本来为他们编写；它的工作电压为 5V，非常小巧可爱。
我们要在这上面附加什么？首先是空气质量传感器。我以前有这个很重的空气质量传感器，但也有一个[很小的](https://thepihut.com/collections/adafruit-sensors/products/adafruit-ccs811-air-quality-sensor-breakout-voc-and-eco2)适合 Arduino，很容易贴在我们的面罩上。接下来，我们可以用传感器做什么？我发现这个[迷你风扇](https://thepihut.com/products/adafruit-miniature-5v-cooling-fan-for-raspberry-pi-and-other-computers)是为 RPI 制造的，但由于它只需要 5V 电源和 GND 插脚，我会选择它，它靠近呼吸器，如果污染严重，它可以被激活，以净化呼吸器周围的空气。如果我们感到有灵感，我们甚至可以附上这个 [GPS 模块](https://thepihut.com/collections/adafruit-sensors/products/adafruit-ultimate-gps-module-66-channel-w-10-hz-updates-mtk3339-chipset)，它有一个惊人的 Arduino 库[在这里](https://github.com/adafruit/Adafruit_GPS)，以及一个 [Micro Sd](https://thepihut.com/products/microsd-card-module-for-arduino) 中关于高污染地点的存储信息，以便稍后上传到您的设备，并为下次考虑不同的路径。

[![](img/644cc6d8241169ea253e8cd1803007f0.png)](https://i.giphy.com/media/l0HlzFckWXgqkUOPu/giphy.gif)

## 2。植物护理工具包

什么？你期望在末日后去 Lidl 或 Tesco 吗？你需要照顾好你自己的蔬菜。你需要注意温度和土壤湿度。我已经在尝试为此创建一个回购项目，因为即使在今天这也是一个可爱的项目。我得到了这个[土壤传感器](https://thepihut.com/products/soil-moisture-sensor)，我们可以使用我们的[经典温度基本传感器](https://thepihut.com/products/adafruit-ds18b20-digital-temperature-sensor-extras)或一个别致的[温度/湿度/压力/空气质量模块](https://thepihut.com/products/bme680)。我用的是 nano，但这个项目也可以用 Arduino UNO 或类似的东西，因为它不需要很小。哦！我们应该在哪里看到结果？我们还有这款[单色显示器](https://thepihut.com/products/spi-i2c-monochrome-60x32-0-5-oled-display-for-arduino)用于 Arduino，或者这款[电子纸显示器](https://thepihut.com/products/adafruit-1-54-tri-color-eink-epaper-display-with-sram-ada3625?ref=isp_rel_prd&isp_ref_pos=1)。你可能想要自动浇水，为此你可能想要在你的项目中添加一个[泵](https://thepihut.com/products/adafruit-peristaltic-liquid-pump-with-silicone-tubing)。

种植快乐！

[![](img/8c1fdcfb9576e88aef8947cdd4f9e3e6.png)](https://i.giphy.com/media/xGX3c3IQ1Gyfm/giphy.gif)

## 3。疯狂监控

哦，好吧，我们有危险，我们明白。但是如果你想让你的末日后的朋友和邻居认为你是最疯狂的幸存者，看看这个列表:

*   [阿达果盖革计数器套件-辐射传感器](https://thepihut.com/products/adafruit-geiger-counter-kit-radiation-sensor)及其可爱的[外壳](https://thepihut.com/products/adafruit-geiger-counter-kit-case?ref=isp_rel_prd&isp_ref_pos=1)。我真的很喜欢阿达果，就像…为什么。

*   物联网入门套件[粒子光子](https://thepihut.com/products/adafruit-particle-maker-kit-with-photon?ref=isp_rel_prd&isp_ref_pos=3)保护和监控你的地方，就像你是一个疯狂的幸存者。但可能互联网也是一团糟，所以在这种情况下没有。

*   因为你不知道你什么时候会需要它。后启示录太疯狂了，好吗？

*   一个 [Arduino 移动机器人套件](https://thepihut.com/products/pirate-4wd-arduino-mobile-robot-kit-with-bluetooth-4-0)，你可能想要附加一个 [RPI](https://thepihut.com/products/raspberry-pi-4-model-b) 和一个使用 OpenCV 进行图像处理的 cam。我不知道，搜索入侵者或作为一个同伴，因为每个人都会孤立你，因为你显然是疯了。还是你？？

[![](img/7e2b6b527cec7969a8504a88ace7039c.png)](https://i.giphy.com/media/hXG93399r19vi/giphy.gif)

## 沟通

万一你有朋友和家人想保持联系。为此，您可以使用 [RPI](https://howtoraspberrypi.com/create-radio-transmitter-raspberry-pi/) 创建一个广播电台，或者即使它不像列表中的其余部分那样简单，您也可以使用这个宝丰频率可编程微型无线电收发机。
T3![](img/d47a0464bdd15eb7f247a38f86cfeabb.png)T5】

[![](img/d347b6d61e5164a737fe4fa1ce18347e.png)](https://i.giphy.com/media/qKjggzIepAORO/giphy.gif)

我鼓励你分享你疯狂的后启示录硬件想法！