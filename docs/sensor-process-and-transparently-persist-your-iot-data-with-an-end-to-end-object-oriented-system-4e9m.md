# 利用端到端面向对象的系统，检测、处理并透明地持久存储您的物联网数据

> 原文：<https://dev.to/martinezpeck/sensor-process-and-transparently-persist-your-iot-data-with-an-end-to-end-object-oriented-system-4e9m>

让我们假设一个典型的物联网/边缘计算开发者/部署堆栈:

1.  一种(可能是低级的)语言，有自己的语法和 IDE 来读/写来自传感器或任何 GPIO 相关的数据。
2.  另一种(可能是高级别的)语言/ IDE，它将复杂程度不断增加的业务逻辑应用到收集的数据中。
3.  一个数据库。

很可能语言 1)和 2)是不同的。因此，您可能需要不同的 ide，开发人员可能需要了解几种语言，代码可重用性不再那么简单，等等。这些语言甚至可以来自不同的范例，比如过程化和面向对象。

然后，您必须在一个可能是关系型或 NoSQL 型的数据库上持久化。为此，您需要将对象映射到表或键/值类型的存储中。又名[阻抗不匹配](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch)。

现在想象一下，如果你有一个范例，一个简单的语言和 IDE 来管理它们，会怎么样？在之前的一篇文章中，我表达了自己对为什么 Smalltalk 语言非常适合物联网和边缘计算的想法。

在这篇文章中，我将展示这一点。设置将是一个树莓派 3B+运行 Raspbian 和 [VASmalltalk](https://www.instantiations.com/products/vasmalltalk/index.html) 。后者将使用 [pigpio Smalltalk 包装器](https://dev.to/martinezpeck/accessing-raspberry-pi-gpios-with-smalltalk-4blh)使用 DS18B20 温度计传感器获取温度，将这些测量具体化为一级对象，应用一些业务/领域逻辑，最后将这些对象透明地持久存储在远程[宝石](https://gemtalksystems.com/)面向对象的数据库中。

顺便说一句，这个帖子是由一个非常好的 Smalltalk 社区驱动的发起的。

### 感知和处理物联网数据

在之前的帖子中，你可以看到[如何使用 Smalltalk](https://dev.to/martinezpeck/accessing-raspberry-pi-gpios-with-smalltalk-4blh) 的`pigpio`包装器。在这种情况下，我们所要做的就是连接一个 DS18B20 温度计传感器，使用我们包装器中的类`OneWireDS18B20ThermometerDevice`，然后发送消息`#temperature`。[电线连接非常简单](http://www.circuitbasics.com/raspberry-pi-ds18b20-temperature-sensor-tutorial/)因为你只需要将 3 根电缆连接到 GPIOs。我将让这些细节为另一个职位。我们已经准备好了传感器部分。

液体错误:内部

下一步是具体化温度测量并将它们存储在一个集合中。为此，我们(实际上是来自 GemTalks Systems Inc .[的 Richard Sargent](https://twitter.com/RSargent5x5) )创建了这个[简单模型应用](http://vastgoodies.com/maps/Simple%2520GBS%2520Temperature%2520Sensor%2520Example)。它包含一个单独的类:

```
Object subclass: #RjsTemperatureSensorReading
    classInstanceVariableNames: ''
    instanceVariableNames: 'whenTaken temperature '
    classVariableNames: 'Readings '
    poolDictionaries: '' 
```

Enter fullscreen mode Exit fullscreen mode

读数列表存储在一个名为`Readings`的类变量中，该类提供了一些帮助方法来创建新的实例以及记录和报告它们:

[![](img/27aaa11526bff818563304420f12a230.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zYM0quBz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-6.21.56-pm.png%3Fw%3D748)

**重要提示:**请注意，这是我认为 Smalltalk 可以对其他语言产生巨大影响的原因之一。随着物联网和边缘计算的增加，我认为我们将开始在主板或边缘上运行更多的业务逻辑。它不再仅仅是感知数据。您可以应用复杂的业务逻辑并在那里进行处理。代码变得越复杂，我就越觉得 Smalltalk 作为一种目标实现语言工作得很好。

为了让帖子更容易理解，这里的“业务逻辑”相当简单。但同样，它也可能像涉及一些 AI(人工智能)、ML(机器学习)或任何其他领域特定逻辑一样复杂。

### 为宝石设置 VASmalltalk

几年前，我写了一篇关于什么是宝石的博文:一个具有透明持久性的对象系统。

液体错误:内部

有一个名为[GBS(Smalltalk 的宝石生成器)](https://gemtalksystems.com/products/gbs-va/)的宝石工具，它允许客户端 small talk(在这种情况下是 VASmalltalk)透明地持久保存和检索宝石数据库中的对象。

因此，第一步是下载 GBS 并将其安装到您的 VASmalltalk 映像中。你可以阅读[前一个链接](https://gemtalksystems.com/products/gbs-va/)的文档。但这基本上意味着下载一个`.dat`文件，然后导入并加载 GBS 地图:

[![](img/fe9013b5455b4cab183918227097016c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KkCqtjdo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-9.31.04-am.png%3Fw%3D748)

下一步是在 Pi 中安装 GemStone RPC 库(在本例中是 ARM 编译的),这允许通过网络将客户机 Smalltalk 连接到远程 GemStone。同样，这就像进入[宝石网站](https://gemtalksystems.com/products/gs64/)，下载[树莓库](https://downloads.gemtalksystems.com/pub/GemStone64/3.5.0/GemStone64BitClient3.5.0-arm32.Linux.zip)一样简单。

一旦你下载了库，你必须将它们移动到 VASmalltalk 的预期位置，这个位置通常是根目录:

```
cp ~/Instantiations/GemStone/gciArmLinuxLibs-3.4.4/*.so ~/Instantiations/Vast92Ecap2_Linux/ 
```

Enter fullscreen mode Exit fullscreen mode

然后你必须告诉 GBS 使用哪个确切的库，这样你就可以在 Smalltalk 中评估这段代码:

```
GbsConfiguration current libraryName: 'libgcirpc-3.4.4-32.so'. 
```

Enter fullscreen mode Exit fullscreen mode

最后，您必须至少添加一个宝石会话信息，以便您可以尝试连接。你可以通过主菜单`GemStone`->-`Sessions`->-`Add`按钮来完成:

[![](img/26f3ba5f58d75ad6210bfc8c98834239.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PpdRgaun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-9.39.03-am.png%3Fw%3D748)

#### 或…通过脚本

如果你像我一样懒惰，你更喜欢编写脚本，那么这就是我所做的:

```
"Import"
StsConfigurationMapsBrowser new importAllConfigMapsFrom: '/home/pi/Instantiations/GemStone/GemBuilder5.4.5/gbs5.4.5.dat'.

"Load latest"
(EmConfigurationMap editionsFor: 'GBSGemBuilder') first loadWithRequiredMaps.

" Set libraries"
GbsConfiguration current libraryName: 'libgcirpc-3.4.4-32.so'.

"Add the session to my Gemstone database"
GBSM addParameters: (GbsSessionParameters new
        gemStoneName: 'gs_343_gbs';
        username: 'XXXX';
        password: 'YYYY';
        rememberPassword: true;
        gemService: '!@XXXX#netldi:50400!gemnetobject';
        yourself). 
```

Enter fullscreen mode Exit fullscreen mode

在这篇文章中，我将连接到一个在云端远程运行的宝石。

### 从 VASmalltalk 连接、浏览和评估宝石代码

我们想尝试的第一件事就是将 Pi 上运行的本地 VASmalltalk“连接”到云中运行的 GemStone。

液体错误:内部

为此，我们可以使用主菜单`GemStone`->-`Sessions`->选择会话->-`Login RPC`按钮。如果工作正常，您将看到下面列出的连接。

[![](img/1c9470ab798029c88687ca9755f34923.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WLXSrJS1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-12.50.11-pm.png%3Fw%3D748)

现在我们连接上了，我们可以玩宝石了…我们可以用`GemStone`->-`Tools`->-`New GS Workspace`打开一个工作区，在上面评估、检查和显示代码:

[![](img/f2150970e069fe060d4e5264b005cfdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UY7g-pa5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-12.54.48-pm.png%3Fw%3D748)

你甚至可以浏览宝石类，实现者，发送者，类引用等。

[![](img/d41e2a7a7606cf8b07f65acafff0329e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LuvqouL6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-12.56.40-pm.png%3Fw%3D748)

### 最后，透明、可扩展和面向对象的持久性

能够从 Pi 上运行的 VASmalltalk 浏览和评估远程宝石的代码是非常酷的。但是更好的是真正把它作为一个透明的、可伸缩的、面向对象的数据库来使用。

好吧…因此，我们现在可以用域对象来表示温度测量，并通过 GPIO 访问来真正检测它。我们如何在 GBS 的宝石上坚持这一点？

第一件事是像我们之前做的那样连接到宝石会话。那么我们必须把`RjsTemperatureSensorReading`的定义从 VASmalltalk“复制”到 GemStone。为此，我们只需浏览该类，右键单击，然后单击`Create in GS`。

[![](img/621a7213afcb747e36a8dc66854856d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tQtGFJqP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-6.16.24-pm.png%3Fw%3D748)

酷毙了。这个职业现在也存在于宝石上。那么…让我们来做最后的魔术:

```
"
GemStone Set Up:
    1) Execute 'RjsTemperatureSensorReading clearReadings.' in VA Smalltalk to initialize the class variable.
    2) Go to GBS and open a session into the stone.
    3) Use GBS to push the class RjsTemperatureSensorReading to the server (right click -> 'Compile in GS'.
    4) Open a GemStone workspace and execute 'RjsTemperatureSensorReading clearReadings.' in GemStone to initialize the class variable.
        GemStone -> Tools -> New GS Workspace
    5) Commit.
    6) Execute below lines
"   

| session connector measures |
session := GBSM currentSession.
connector := GbsClassVarConnector
        stName: #RjsTemperatureSensorReading
        gsName: #RjsTemperatureSensorReading
        cvarName: #Readings.
connector updateSTOnConnect.
connector connectInSession: session.

session evaluate: 'RjsTemperatureSensorReading clearReadings.'.

measures := OrderedCollection new.
measures add: TestOneWireDS18B20ThermometerDevice fetchTemperature -> DateTime now. 
(Delay forSeconds: 2) wait.
measures add: TestOneWireDS18B20ThermometerDevice fetchTemperature -> DateTime now. 
(Delay forSeconds: 2) wait.
measures add: TestOneWireDS18B20ThermometerDevice fetchTemperature -> DateTime now. 

measures do: [:each | 
    (RjsTemperatureSensorReading newTemperature: each key asOf: each value) recordSample.
].

((session evaluate: '
    RjsTemperatureSensorReading sampleReport')
        copyReplacing: Character cr withObject: Character lf)
            inspect.
session commitTransaction.  

RjsTemperatureSensorReading sampleReport inspect. 
```

Enter fullscreen mode Exit fullscreen mode

这段代码基本上告诉 GemStone 将来自客户机 Smalltalk 的类变量`Readings`映射到它自己。然后，我获取实际温度(使用传感器)，具体化一个测量对象，将该对象存储到集合中，等待 2 秒钟，重新开始。这样做三次。最后，我让 VASmalltalk 和 GemStone 打印一份关于类变量`Readings`的报告。

我现在将传感器放在热水上(这样你就可以看到温度的变化！哈哈哈)然后在 snippet 上面运行。这些是结果:

[![](img/bedfc59293b00bad30fec6fec4fc62d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GpXYIYw3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://marianopeck.files.wordpress.com/2019/06/screen-shot-2019-06-19-at-6.50.19-pm.png%3Fw%3D748)

您可以看到 3 个测量值，记录的时间和温度(摄氏度)。

请注意，每个报告字符串都是在不同的小型对话中生成的，并且收集是如何自动同步的。看到 VASmalltalk 打印日期为`06/19/2019`而 GemStone 打印日期为`19/06/2019`很有趣。

### 结论

请注意，这篇文章中解释的一切不仅仅适用于 Raspberry Pi，还适用于任何基于 ARM 的 SBC。实际上…在 VASmalltalk 和 GemStone 运行的任何平台上。当然，也有缺点和限制:

1.  目前有了 VASmalltalk，我们可以瞄准那些有足够硬件来运行 Linux 的 SBC。
2.  包装的 pigpio 库只在 Raspberry Pi 上工作…对于其他平台，我们需要包装另一个库。
3.  正如 Smalltalk 经常发生的那样，我们没有像其他语言(如 Python)那样多的库。

但是，我个人认为，有 Smalltalk 这样完全面向对象的、成熟的、简单的、功能强大的编程语言来做物联网是很棒的。与可从任何 SBC(单板计算机)访问的可伸缩、透明、面向对象的持久性相结合，符合独特的技术堆栈。

液体错误:内部