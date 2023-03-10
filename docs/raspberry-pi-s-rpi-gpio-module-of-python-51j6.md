# python 的 Raspberry pi 的 RPI GPIO 模块

> 原文：<https://dev.to/suryasr007/raspberry-pi-s-rpi-gpio-module-of-python-51j6>

### 基本设置和使用 python 的 raspberry pi 的 RPI GPIO 模块

第一篇文章是关于基本设置和使用 python 的 raspberry pi 的 RPI GPIO 模块。

**树莓派简介:**

Raspberry Pi 是一款信用卡大小的计算机，专为教育设计。第一款于 2012 年推出，主要用于教育

[![](img/07d673de06dd70eeb60bd7e4e90e30ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SBIJ-2oO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AyEkMTqLi4ZzCcPd6Hl7yaw.jpeg) 
来源:[https://www.raspberrypi.org](https://www.raspberrypi.org/)

特点:

*   *小尺寸*
*   *低成本设备*
*   *完整的 linux 电脑*
*   *低功耗*

**GPIO 引脚:**

有 40 个功能强大的通用输入/输出(GPIO)引脚，它们是 Raspberry pi 的核心组件
的一部分。有两个 5V 引脚和两个 3.3v、接地
引脚和通用引脚。

[![](img/3c9eaf6b2e22a70fbc1f25aa66defc68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bzwjBc0A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2ABCO8L5pCB4LCak1y.) 
来源:
[https://www . raspberrypi . org/documentation/usage/gpimg/gpio-numbers-pi2 . png](https://www.raspberrypi.org/documentation/usage/gpimg/gpio-numbers-pi2.png)

* * *

**RPI GPIO 模块:**

Python 可以用来在 Raspberry pi 里写应用。它带有一个强大的软件包管理器，叫做 PIP，可以用来安装第三方软件包。

RPI GPIO 模块是一个第三方包，可用于配置和控制 GPIO 引脚。

基本的设置非常简单。设置完成后，我们定义一个引脚的值应该是高还是低。

#### 基本设置

*   确保在 raspberry Pi 中安装了 python>2.7。
*   使用 PIP 安装模块

```
 sudo pip install RPi.GPIO 
```

Enter fullscreen mode Exit fullscreen mode

*注意:*如果您在安装软件包时遇到任何问题，请安装 python-dev 并重试

*   将模块导入脚本
*   设置管脚编号方案(如下所述)

```
 GPIO.setmode(GPIO.BCM) 
```

Enter fullscreen mode Exit fullscreen mode

*   配置所需的引脚

```
 GPIO.setup(18, GPIO.OUT)

    # PIN 18(BCM) is now used as output 
```

Enter fullscreen mode Exit fullscreen mode

**引脚编号方案:**

有两种类型的端号编号方案

*GPIO。板:*板编号方案

该选项指定您通过插头的引脚编号(即印刷在电路板上的编号)来引用引脚。

*GPIO。BCM:* 博通芯片专用 pin 码。

该选项意味着您通过“Broadcom SOC 渠道”编号引用引脚。

* * *

一旦基本设置完成，根据要求，我们将引脚设置为高电平
或低电平

```
 # Import modules
    import RPi.GPIO as GPIO
    import time
    Import atexit

    #PIN setup
    GPIO.setmode(GPIO.BCM)
    GPIO.setup(4, GPIO.OUTPUT)

    # Loop to send HIGH and LOW to the mentioned pin
    While True:
        GPIO.output(4,GPIO.HIGH)
        time.sleep(1)
        GPIO.output(4,GPIO.LOW)
        time.sleep(1)

    #Garabage Cleaner
    def clean_up():
        GPIO.cleanup()

    atexit.register(clean_up) 
```

Enter fullscreen mode Exit fullscreen mode

上述程序向第 4 个引脚(BCM)连续发送一秒钟的高信号和下一秒钟的低信号，直到程序退出。为了验证过程的输出，我们可以连接 LED 并测试它。

程序的前 5 行是基本设置，随后的循环
持续向第 4 个引脚发送高电平和低电平信号，直到程序
存在。

`GPIO.cleanup()`函数用作垃圾清除器，建议在
每个使用 raspberry pi GPIO 引脚的程序中使用。

atexit.register()函数用于在退出前触发一个函数。

根据应用的要求，我们可以在不同的
模式下设置引脚。

例如:生成模拟输出

**执行:**

一旦程序完成，它可以通过在终端
中提供以下命令来运行

`sudo python <program_name>.py`

要退出程序

按下`ctrl+c`

来源:

Rpi。GPIO 模块:
[https://pypi.python.org/pypi/RPi.GPIO](https://pypi.python.org/pypi/RPi.GPIO)

树莓派文档:
[https://www.raspberrypi.org/documentation](https://www.raspberrypi.org/documentation/usage/gpimg/gpio-numbers-pi2.png)