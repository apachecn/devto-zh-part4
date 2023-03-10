# 我建了一个自动化灌溉系统，因为我懒！

> 原文：<https://dev.to/mmphego/i-built-an-automated-irrigation-system-because-i-m-lazy-30he>

[![post image](img/ede7011d59f25a68b4f8cde892f596fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CUZvoWU6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/master/assets/2019-09-02-I-built-an-automated-irrigation-system-because-im-lazy%21.jpg)

*最初发布于[blog.mphomphego.co.za](http://bit.ly/2jUMEZU)2019 . 09 . 02。*

* * *

# 故事

室内植物可以给房间带来新鲜的生命，增加氧气，提高生产力，激励每个人。然而，由于经常需要工作，它们不仅被人造的、假冒的作物所替代，这些作物在它们生命中的某个时候被扔进工地。它们没有真正的作物有那么多好处。由于责任，年轻一代经常被视为有宠物，尽管植物可以充分利用房间，但它们经常被遗忘。

无论是太热、太冷、太干还是太湿，调节作物达到的水位都是非常必要的。给作物浇水是最重要的文化实践，也是最耗费劳力或最乏味的工作。

因此，一个自动植物浇水系统的想法将是有效的，只有当他们需要的时候才浇水，这减少了照看植物所需的时间。

用于连续监测土壤湿度水平并决定是否需要浇水以及植物土壤需要多少水的方法。

本质上，该系统被设计和编程为在特定时间监测土壤中的湿度。当湿度水平超过设定的阈值时，水泵被触发，并且将提供所需的水量，直到阈值被预先确定。

**TL；博士**

在这里找到代码

# 该如何如何

automagic 植物浇水系统运行在运行 Arduino
[uPython](http://www.micropython.org/) 的 [Wemos D1 esp8266](https://www.wemos.cc/) 上(不是打字错误，是的——微处理器上的 Python)，电容式土壤水分传感器读取土壤中的水分含量，并将数据上传到数据收集、分析和可视化工具 [ubidots](https://ubidots.com/) 。当土壤湿度达到阈值并发送水泵通知时, [Slack](//slack.com) 消息被发送到特定通道。

该系统全天候运行。每隔 15 分钟，它按以下顺序检查传感器，并据此采取行动:

*   土壤湿度传感器。如果花盆中的土壤湿度低于 70%，系统启动水泵 3 秒钟，
*   发送时差通知
*   上传数据到 ubidots。

> 本质上，这拯救了我的植物，使我能够专注于其他无聊的事情。

# 走查

在这一节中，我将详细介绍实现。

## 电路图

[![circuit](img/69795e9f6f7237f91acb0ae00f845ba0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eGxqsNE_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/uPython-Plant-Irrigation-System/master/assets/soilmoisture.jpg) 
用【fritzing.org】设计的[电路](//fritzing.org/)

### 零件清单

*   1x 威莫斯 D1 ESP8266
*   [1x 电容式土壤湿度传感器](http://bit.ly/2lF0Qqn)
*   [1x 单通道 5V 继电器模块](http://bit.ly/2kanNBH)
*   1 台小型潜水水泵 3-6 伏直流电
*   [1 根硅胶管(泵)](http://bit.ly/2lQ3mdE)

## 设置

### 校准土壤湿度传感器

校准传感器有两个部分，目标是确保传感器功能正常:

*   连接土壤湿度传感器，将其浸入一碗水中并读取读数。
*   擦拭传感器，将其放在干燥的表面上并读取读数

这些读数应输入到 [config.json](https://raw.githubusercontent.com/mmphego/uPython-Plant-Irrigation-System/master/config.json) 文件中。

### 设置节点 MCU &工具

阅读[文档](https://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/intro.html)

TL；速度三角形定位法(dead reckoning)

*   克隆[回购](http://bit.ly/2jTerdl)，
*   将设备插入您的电脑

    **注意:**安装假设设备的端口名为`/dev/ttyUSB0`，否则用端口名【提示:】修改`Makefile`。

*   在`config.json`添加您的 WiFi 设置

*   运行`make all`:引导程序，即擦除、刷新和上传脚本

**注:**这将安装 [`esptool`](https://github.com/espressif/esptool) 和 [`mpfshell`](https://github.com/wendlers/mpfshell) 用于与 ESP 芯片通信和与 MicroPython 板卡串行连接，用固件`esp8266-20190125-v1.10.bin`擦除和刷新芯片，并将所需文件上传到 ESP。

点击[这里](https://www.micropython.org/download)获取最新的 uPython 固件。

### 设置配置

[配置](https://raw.githubusercontent.com/mmphego/uPython-Plant-Irrigation-System/master/config.json)文件是不言自明的，填写缺少的部分。

```
{  "Pin_Config":{  "ADC_Pin":  0,  "Water_Pump_Pin":  12  },  "wifi_config":  {  "ssid":  "",  "password":  ""  },  "MQTT_config":  {  "Host":  null  },  "moisture_sensor_cal":  {  "dry":  841,  "wet":  470,  "Threshold":  80  },  "water_pump_time":  {  "delay_pump_on":  3  },  "slack_auth":  {  "app_id":  "",  "secret_id":  "",  "token":  ""  },  "ubidots":{  "token":  "",  "device":  ""  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 设置时差

我之前添加了一篇关于如何使用 Python 发送 [Slack](//slack.com) 消息的博文，你可以在这里找到[的博文](http://bit.ly/2K46XP8)

[![image](img/d92cee26bc782fb1d6a02cfaf15f71a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nu_3Er_V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/uPython-Plant-Irrigation-System/master/assets/slack.png)

样本代码:

```
class Slack:
    def __init__(self, app_id, secret_id, token):
        """
        Get an "incoming-webhook" URL from your slack account.
        @see https://api.slack.com/incoming-webhooks
        eg: https://hooks.slack.com/services/<app_id>/<secret_id>/<token>
        """
        self._url = "https://hooks.slack.com/services/%s/%s/%s" % (
            app_id,
            secret_id,
            token,
        )

    def slack_it(self, msg):
        """ Send a message to a predefined slack channel."""
        headers = {"content-type": "application/json"}
        data = '{"text":"%s"}' % msg
        resp = urequests.post(self._url, data=data, headers=headers)
        return "Message Sent" if resp.status_code == 200 else "Failed to sent message" 
```

Enter fullscreen mode Exit fullscreen mode

### 设置 Ubidots 数据即

谁不喜欢可视化数据，添加了对 Viz 的支持

[![image](img/89a40a0e24853bca4b1645992d9edafd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YZeQsvQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/uPython-Plant-Irrigation-System/master/assets/ubidots.png)

代码:

```
class Ubidots:
    def __init__(self, TOKEN, device_label):
        self.url = "https://things.ubidots.com/api/v1.6/devices/{}?token={}".format(
            device_label, TOKEN
        )

    def post_request(self, payload):
        """Creates the headers for the HTTP requests and Makes the HTTP requests"""
        print("[DEBUG] Uploading Payload: %s" % payload)
        assert isinstance(payload, dict)

        status = 400
        attempts = 0
        while status >= 400 and attempts <= 5:
            req = urequests.post(url=self.url, json=payload)
            status = req.status_code
            attempts += 1
            utime.sleep(1)
            print("[DEBUG] Sending data to Ubidots...")

        # Processes results
        if status == 200:
            print("[INFO] Request made properly, Updated Ubidots with %s." % payload)
            return True
        else:
            print(
                "[ERROR] Could not send data after 5 attempts, please check "
                "your token credentials and internet connection."
            )
            return False 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

在这篇文章中，您学习了如何在 esp8266 上设置和运行 **Python** ，以及如何使用模数转换器。然后你用一个传感器来监测花盆中土壤的湿度。该软件允许我们通过互联网和 slack 在 repl 和 chart 上看到这个传感器读数。

有很多东西需要学习，但现在你可以将这些技术用于各种不同的传感器来测量温度、湿度、光强等等。

总之，该项目按预期进行。然而，根据植物的类型，需要进行调整。我说的调整是什么意思，湿度阈值和浇水时间非常重要，因为你可能会淹死你的植物。

# 未来的工作

如果我在系统中加入一些人工智能就太好了。添加一些蓝色过滤器，并使用相机(OpenCV 库)监控来自叶子的白光，并利用 FFT 计算指示光合作用生产的植物“幸福”水平，并测量植物随时间的高度或未来高度预测。

# 最终实现

目前在办公室的设置，热胶，和 prestik 混乱，但工作。

[![post image](img/cb817b8f6c0fc0ff37881822d19fd46b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kBJK4lTx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/master/assets/IMG_20190902_073749.jpg)

[![post image](img/bcd51bfd1c852b126c62d86b3b029ece.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W8xXz2DP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/mmphego.github.io/master/assets/IMG_20190902_073801.jpg)