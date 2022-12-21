# 带有 Gmail 通知的“您有邮件”物联网电子邮箱

> 原文：<https://dev.to/andrewberry391/you-ve-got-mail-iot-e-mailbox-with-gmail-notification-5264>

# 电子邮箱

在本教程中，我们将学习如何使用 Wia Dot One 和 Grove 伺服电机，让伺服电机在你有新邮件时在邮箱上升起一面旗帜。

[![<br>
](img/1f778bcef422521623f9d4155467f202.png "Figure 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--B4QIOUgX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/0t-_rK0HwhOPFlDhXYNrbpLNuz6GE_TQQ38YwDpnhXNiNlm7CkqzwsjgC_fgo_NhIQtEuFTBF6Gs) 
*图 1*

## 要求

*   Wia Dot One [在此购买您的](https://store.wia.io/products/dot-one)
*   伺服电机[在这里买你的](https://www.amazon.com/Longruner-Helicopter-Airplane-Controls-KY66-10/dp/B01NA80LUR?th=1)
*   MicroUSB 线缆[在此购买您的产品](https://store.wia.io/products/micro-usb-cable)
*   在 [Wia](https://www.wia.io/) 上的账户

## 准备

1.  **Wia 账户**您需要在 https://www.wia.io/[注册和/或登录您的 Wia 账户](https://www.wia.io/)。
2.  **设置你的 Dot One** 你需要有一个设置 Dot One，你可以在这里[找到如何做的教程](https://wia.developerhub.io/dot-one/setting-up-a-dot-one)。
3.  **设置您的代码项目**现在您需要在您的 [Wia 仪表板上创建一个空间，然后创建一个块项目](https://dashboard.wia.io/spaces)

## 创建区块项目

*   现在，您需要建立一个代码块项目。您可以按照图 4 所示进行操作。
*   在这样做的时候要小心，记住在给变量命名的时候大写字母是有区别的，这在给你的状态取相同的名字的时候特别重要。
*   如果你想这样的代码版本，你可以复制和粘贴代码，而不是做块项目感到自由；也在下面。
*   最后，如果你想尝试，使自己在代码中尝试，然后匹配我的代码，如果你有任何问题！

[![<br>
](img/b0e2579ce1507e264c9a560b419e1413.png "Figure 2")](https://res.cloudinary.com/practicaldev/image/fetch/s--OV9feCaL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/oWF_vRwgTKX_u9h4a_JFahW_27pxUM58VzkEEcE40GNuxzafcdcqVyiY0XXzY_L8bAaHXDV2Pbo_) 
*图二*

```
#include <WiFi.h>
#include <Wia.h>
#include <ESP32_Servo.h> 
String currentState;
String previousState;

Wia wiaClient = Wia();
Servo myServo19;

void setup() {
  WiFi.begin();
  delay(2500);
  myServo19.attach(19);

  currentState = "";
  previousState = "";

}

void loop() {
  currentState = (wiaClient.getDeviceState("emailReceived"));
  delay(1000);
  if (currentState != previousState) {
    myServo19.write(180);
    delay(150000);
  }
  myServo19.write(20);
  previousState = currentState;
  delay(60000);

} 
```

## 设置 Zapier

*   我们将使用带有 webhooks 的 Zapier 平台来检测您何时有新邮件，这将在 Wia 云平台上触发一个事件。

1.  要开始，去 Zapier 并创建一个帐户，一旦你到达如图 3 所示的页面，你需要开始填写你的 Zap 的选项
2.  对于**部分，连接此应用程序...**输入 **Gmail** 然后用这个进入**版块！**进入**扎皮尔**的 Webhooks。
3.  然后对于**段当这种情况发生时...**输入**新电子邮件**或者如果你想在你的电子邮件中有其他选项来触发电子邮箱，那么选择它。最后为**接着做这个！**选择 **POST** 并按下**使用 Zap** 按钮！如图 3 所示！
4.  ![
    ](img/ff361ba53e0f5f74b98c648c869838eb.png "Figure 3") *图 3*
5.  然后按照指示连接您的帐户，按**继续**，并按照指示选择您希望触发 Zap 的电子邮件类型。
6.  接下来当你看到**选择和动作 app** 搜索 **Webhooks** 并选择 **Post** 和**继续**如图 4 和图 5 所示。![
    ](img/8fc13ba5500c292bb2f05ab6550c77da.png "Figure 4") *图 4 * ![
    ](img/789c1c863b1e548a9469896743d3ad57.png "Figure 5") *图 5*
7.  现在，对于下一部分，我们将需要从 Wia 平台填充一些信息，如下所述，如果需要，请参考图 6、图 7 和图 8。
8.  对于**的网址**部分放入【https://api.wia.io/v1/events】
9.  将**有效载荷**留空。
10.  对于**数据**,在左侧框中输入**名称**,在右侧框中输入您的活动名称，在本例中为 **emailReceived**
11.  在**包请求数组**中选择**否**
12.  将文件留空。
13.  在**中选择**是****
14.  将**基本授权**留空。
15.  对于左侧框中的**标题**，输入**授权**，在右侧框中，我们需要我们的设备 ID。
16.  您可以通过进入 [Wia 仪表板](https://dashboard.wia.io/spaces)找到您的设备 ID，并进入您的 Dot One 所在的空间。
17.  然后转到**事物**标签，选择你的点一，并转到**配置**部分，你应该看到**你的设备密钥是:**复制那个密钥，如图 8 红色框中所示。
18.  现在回到 Zapier，在右边框的 **Headers** 部分输入**不记名(你的密钥)**，这样粘贴你复制的密钥，这样如果你的密钥是“d_sk_712yhbbasd8192yesn”，例子看起来就像“不记名 d_sk_712yhbbasd8192yesn”。

[![<br>
](img/560ed6a949a98a78271cc22e5779afe3.png "Figure 6")](https://res.cloudinary.com/practicaldev/image/fetch/s--aoHIypCi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/yHEMVMhgzb7slp7sOoxMWIMA3WuSSY7Ut_FD8C-M-k_T7Y5HCp1QUWsqSgDOLgMPDMHoRma8q1Hk) 
*图 6*
[![<br>
](img/2993040444a41f461fb92ea4fd511d68.png "Figure 7")](https://res.cloudinary.com/practicaldev/image/fetch/s--mXu-yWXi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/c6J0amCOL0UhKpcZu5CoVzH67QLp5AafjDCnTgNDRYZKBODWsCB5wTm5YwQd9pcvq52Ty37Umtxx) 
*图 7*
[![<br>
](img/0f02a49591c559bd92a65e2c42b0793e.png "Figure 8")](https://res.cloudinary.com/practicaldev/image/fetch/s--XFr1P7ir--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/EJBWLmTiOtcLTlZNxY9Y-3Qmxq_MsukpHuXtrWIwTCVJljfjv0uQ4-NKyjgb3FFcH-1t2dX5VRFk)
*图 8*

1.  然后在下一页点击**继续**和**完成**，你就完成了 Webhook 的设置！
2.  你可以通过在 Zapier 上的测试来测试这个，点击**动作**部分下的**测试这个步骤**
3.  一旦 Zapier 提示您已完成测试，请转到 [Wia 仪表板](https://dashboard.wia.io/spaces)并在**事物**选项卡中选择您的 dot one
4.  然后转到 **Events** 部分，您应该会看到一个带有您在 Zapier 上输入的名称的事件。正如您在图 9 中看到的，我有许多事件进来！

[![<br>
](img/c3f5152065e286cac8e825bccbe07383.png "Figure 9")](https://res.cloudinary.com/practicaldev/image/fetch/s--7cKMPjMO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/ug4TXH6-d-BURiG6Yu2L8rfyqMM6fzNuRvhbYIiDMa2Xjv_rHiNXVE6c6HGNO1XiPsBHAO7yVT2V) 
*图 9*

## 设置你的流程

*   现在我们已经用 webhooks 设置了 Zapier 来触发我们的 Dot One 上的事件，我们需要设置一个流程来识别该事件并告诉您的 Dot One 做什么。

1.  首先，转到 [Wia 仪表板](https://dashboard.wia.io/spaces)，使用您的 Dot One 进入空白处，选择**流程**选项卡到**创建流程**。
2.  现在，在您的新流程中，将一个创建的节点的**事件拖到工作区中，该工作区位于 **Wia** 下的**触发器**中。**
3.  然后命名事件，并从列表中选择您的 Dot One。
4.  **注意:**请务必将您的活动名称与您在 Zapier 的 Webhook 中输入的活动名称相同，在我们的示例中是 **emailReceived** ，如图 10 所示。

[![<br>
](img/137dfbe00babad2b47392077f76506a4.png "Figure 10")](https://res.cloudinary.com/practicaldev/image/fetch/s--RnuSLLaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/7S7tAEH0Yd3EFWPyrFrFoIZX7xiYry9aW2w10MK4uxUGq78SRE9lJHQbGqf6-ha0YR6WXIiRBNlO) 
*图 10*

1.  现在拖动一个**运行函数**节点到 **Wia** 下**逻辑**中的工作区。
2.  在此运行函数中，将以下代码复制并粘贴到编辑器中:

```
output.body = device.state.emailReceived ? (parseInt(device.state.emailReceived) + 1) : 1; 
```

*   这段代码获取当前状态，如果该状态存在，则递增 1，如果不存在，则将当前状态设置为 1。
*   这是我们之前的代码用来改变状态的，这样代码就可以检查状态是否改变了，如果改变了，就意味着有一个事件从 webhooks 发出，这样你的邮箱里就有一封新邮件了！
*   现在我们需要更新状态，所以将一个**更新状态**节点拖到位于 **Wia** 下的**逻辑**中的工作区。
*   在此节点中，我们需要填写一些关于状态的信息，因此选择您的设备，对于 **State** 输入我们正在处理的状态，对于我们的示例，它是 **emailReceived** ，对于 value put **${input.body}** ，它获取 run 函数的输出并用其值更新状态。
*   然后如图 11 所示连接节点，并打开流，您应该已经设置好了！

[![<br>
](img/460660ae570a7bac628beada7117b865.png "Figure 11")](https://res.cloudinary.com/practicaldev/image/fetch/s--Y6-lJDi4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/nA5Dma6djZatql7aezc1lQfkjPg1lFJFt2gD78VRL1C8QMCEfdWBJipQrUyYE17-XUZOjT-QeyMg) 
*图 11*