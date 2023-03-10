# 为什么移动地理定位测试对质量保证至关重要

> 原文：<https://dev.to/django_stars/why-is-mobile-geolocation-testing-essential-for-quality-assurance-npo>

这篇关于地理位置测试的介绍性文章面向使用 web 应用程序、跨平台(React Native)和本地(iOS、Android)移动应用程序的质量保证(QA)和测试工程师。它提供了如何在您的项目上开始地理定位测试的逐步说明，还描述了几种可能方法的优缺点。在这里，您还将学习后端表示、数据生成、数据验证、仿真和使用 REST API 测试级别等关键概念。

## 什么是地理定位，为什么需要地理定位测试

使用地理定位的不再只是 GPS 导航应用。位置、路线方向和预计到达时间是交通、食品配送、电子商务、游戏、社交媒体中正在实施的功能——事实上，不使用地理定位的应用类别每天都在减少。

对于一家基于 app 的公司来说，业务逻辑往往取决于基于人和物的位置的动作触发。这使得对如何执行地理定位测试的坚实理解成为使这类项目成功的必备要素。

在开始之前，先回顾一下定义:

*   地理定位。移动电话或计算机的真实世界地理位置的识别或估计。因为我们处理的是设备和网络，所以了解我们可能遇到的问题(正常运行时间、连接速度、准确性)以及定义中估计部分的性质是很重要的。
*   地理定位测试。通过互联网识别设备地理位置的技术。换句话说，这种测试包括与一个物体在空间中的位置有关的一切，基于该物体关于其他物体的位置的行动，以及对不同过程和区域的影响。

## 地理定位在应用程序后端是如何表现的

地理定位请求和响应表示为 JSON (GeoJSON)，存储地理数据的内容类型为 application/json。

### geo JSON 是什么？

GeoJSON 是一种开放标准格式，旨在表示简单的几何对象及其非空间属性。它基于 JSON，JavaScript 对象符号。

### 什么是地理数据？

在众多地理数据几何对象中，最重要的是点、路径、面、轨迹和要素。

#### *点*

点是操作系统纬度、经度和高程坐标的组合。我们可以想象一个三维向量空间中的点，用 x 轴、y 轴和 z 轴上的坐标来表示。主要的点参数是纬度和经度，因为高程坐标很少使用，并且仅在特定情况下使用，如大气压力计算。

关于这一点，有两件重要的事情需要记住:

1.  标准是必须总是先列出纬度，再列出经度(似乎不是每个人都知道这一点)；
2.  纬度或经度的第五位及以后的小数意义不大，第五位小数代表略超过 10 英尺，或不到 5 米。

在一个食品配送应用程序示例中，点代表订单的取货位置、客户的卸货位置和配送人员的位置，通常只有他们的纬度和经度坐标。

**点编码表示法**

```
{
    "geometry": {
        "type": "Point",
        "coordinates": [
            13.381969928741455,
            52.50479381812203
        ]
    }
}
```

[![point-code-representation-geolocation](img/dc6b908f725c2896b9cc0d1d0574f81b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9qu-NWT3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/uploads/2019/07/point-code-representation.png)

#### *路径*

路径是一组点。你连接两个或更多的点得到一条路径。因此，该功能可以显示设备在一天中是如何移动的，或者建立到目的地的路径。

使用相同的食品交付应用程序示例，路径显示了从交付人员到提货地点的距离，以及从提货地点到卸货地点的路线。

**路径代码表示**

```
{
    "geometry": {
        "type": "LineString",
        "coordinates": [
            [
            13.379566669464111,
            52.50663540575101
            ],
            [
            13.38233470916748,
            52.50445422616214
            ],
            [
            13.386626243591309,
            52.50536850623138
            ],
            [
            13.38611125946045,
            52.50638725305074
            ]
        ]
    }
}
```

[![path-code-representation-geolocation](img/605788841360cae23e3749d7f7abedc1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fAAUVc6G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/uploads/2019/07/path-code-representation.png)

#### *多边形*

多边形是一组封闭的点。多边形看起来像一条路径，但有一个重要的区别。多边形地理对象的主要规则是具有相同的第一点和最后一点。因此，您的路径在起点处结束，并形成一组称为多边形的封闭点。换句话说，我们得到一个面积，我们可以计算或定义一个点是否属于特定的多边形(即面积)。多边形有一个问题:这种类型的对象不能从自身中排除另一个多边形或点(即，不能创建一个中间有洞的多边形)。

使用食品配送应用程序示例，多边形将标记配送人员可以提取请求的区域以及他们将配送请求的区域。

**多边形代码表示**

```
{
    "geometry": {
        "type": "Polygon",
        "coordinates": [
            [
            [
            13.383235931396484,
            52.50723619069716
            ],
            [
            13.382356166839598,
            52.50448034871372
            ],
            [
            13.386626243591309,
            52.50539462823964
            ],
            [
            13.383235931396484,
            52.50723619069716
            ]
            ]
        ]
    }
}
```

[![polygon-code-representation-geolocation](img/505fe8878033d77c5c49e1cedb223de3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KVf5C4NH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/uploads/2019/07/polygon-code-representation.png)

#### *轨道*

轨迹是一组带有时间戳的点。给每个接收到的点加上时间，你就得到一个轨迹。Track 提供设备在每个时刻的位置信息。这种类型的地理对象通常以 GPX 格式存储，这是一种 XML 模式，设计为软件应用程序的通用 GPS 数据格式。它可以用来描述路点、轨迹和路线。

**轨道代码表示**

```
<gpx>
    <wpt lon="-78.4814684" lat="38.0283608">
        <name>TP001</name>
        <ele>139.36167907715</ele>
        <time>2017-05-18T12:20:25Z</time>
    </wpt>
    <wpt lon="-78.48174" lat="38.02844">
        <name>TP002</name>
        <ele>139.58670043945</ele>
        <time>2017-05-18T12:20:37Z</time>
    </wpt>
    <wpt lon="-78.4817437" lat="38.0284428">
        <name>TP003</name>
        <ele>139.79161071777</ele>
        <time>2017-05-18T12:20:39Z</time>
    </wpt>
    <wpt lon="-78.48159" lat="38.02877">
        <name>TP004</name>
        <ele>139.78443908691</ele>
        <time>2017-05-18T12:21:14Z</time>
    </wpt>
</gpx>
```

#### *功能*

要素是具有附加信息的任何地理对象。与任何地理对象相关的所有附加信息都可以存储在对象的“属性”中，包括时间、地址等。

在应用程序中，这对于显示设备发送坐标、地址、请求阶段等的时间非常有用。

**特征代码表示**

```
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [
            30.492553710937496,
            50.46236709872438
        ]
    },
    "properties": {
        "created_time": "2019-07-04T12:15:32.753544Z",
        "address": "st. Glubochitskaya, 44"
    }
}
```

## 如何进行地理定位测试

因为地理定位依赖于设备和网络，并且因为现实生活条件从来都不理想(除了代码本身)，所以为了获得最佳结果，使用一系列物理和模拟的 Android 和 iOS 移动设备来测试应用程序总是明智的。让我们回顾一下有助于地理定位测试的工具和步骤。

### 地理定位数据生成和验证工具

有效和彻底的地理定位测试需要工具和服务来帮助生成和验证测试数据。使用以下工具将提高测试速度，并为探索性或其他测试类型提供额外的时间。

#### *[geojson . io](http://geojson.io)T3】*

这个免费的 web 服务提供了为点、路径(线串)和多边形对象生成 GeoJSON 代码的能力，只需单击地图上的相关位置即可。此外，您还可以查看构建路径的长度(以米、公里、英尺、码、英里为单位)以及构建面的面积(以平方米、平方公里、平方英尺、英亩、平方英里为单位)。

#### *[GeoJSONLint](http://geojsonlint.com/)T3】*

这项免费的网络服务有助于验证 GeoJSONs。使用这个简单的工具，您可以将任何 GeoJSON 粘贴到验证器中，并获得地图上对象的可视化，以验证它是否与预期结果匹配。

#### *[创建轨迹](https://www.gpsies.com/createTrack.do)*

这个免费的网络工具对轨迹生成非常有用。它允许你画一条路径，选择需要的速度，并下载各种格式的曲目。唯一的不便是需要注册才能下载文件。

### 安卓地理定位仿真

数据生成只是第一步，因为没有仿真就没有任何意义。仿真有助于消除到处跑来跑去进行测试的需要。相反，使用普通的模拟方法。

对于基于 Android 的设备，目前我们找到了 3 种方法:使用 Android API 调用、内置 Android geo 模拟功能和专门构建的应用程序。

#### *Android API 调用*

以下是使用 Android API 调用模拟地理数据的步骤:

1.  通过多次选择“内部版本号”部分(关于电话>内部版本号)，在设备设置中打开“开发者”模式。轻敲几下后，你会看到一个小的弹出警告，告诉你“你现在离成为开发者还有 X 步之遥”，每多敲几下就会有一个数字倒数。
2.  使用 USB 电缆将您的 Android 设备连接到笔记本电脑或 PC。
3.  使用命令行通过 TELNET 协议发出请求，并发送该请求，这将更改设备上的位置坐标:

```
import telnetlib port = b'5554'
host = b'192.168.88.161'
device = telnetlib.Telnet(host=host, port=port)
device.write(b'geo fix ' + xpoint + b' ' + ypoint + b' ' + b'\n')
```

#### *Android 模拟器内置地理模拟*

您还可以使用笔记本电脑或 PC 连接 Android Studio 来模拟 Android 设备本身。

1.  打开 Android Studio
2.  启动任何 Android 模拟器
3.  点击“更多”按钮
4.  编辑经度和纬度字段
5.  上传 GPX/KML 文件并点击开始按钮

[![emulate-geolocation-testing-for-mobile](img/74b13c2ed791f415e83e80c19176571e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Sb5Do94--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/uploads/2019/07/emulate-the-android-device.png)

#### *设备上的应用*

模拟位置数据最简单的方法是使用一个专门为此设计的应用程序。你唯一需要的是一个安卓设备和互联网。在这一类别的许多应用程序中，最好的一个是 [Lockito](https://play.google.com/store/apps/details?id=fr.dvilleneuve.lockito&hl=en) 。这个应用程序可以建立一个从 A 点到 B 点的基于道路的路径，包括速度设置。此外，它允许重复使用以前创建的轨道。

[![geolocation-for-mobile-app](img/36d20941c97e64f09f0655ede50d3a6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--toDHNovC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/uploads/2019/07/on-device-apps.png)

### iOS 地理定位仿真

iOS 设备也可以模拟地理数据，但由于应用商店的限制，不允许应用程序更改地理位置，因此不像 Android 设备那样容易。不过，有几种方法可以为 IOS 设备生成模拟数据。

#### *Xcode(适用于 MacOS)*

Xcode 是 macOS 的集成开发环境，包含 Apple 开发的一套软件开发工具，用于开发 macOS、iOS、iPadOS、watchOS 和 tvOS 的软件。这个工具为您提供了一个机会，可以在仿真器上模拟数据，也可以在插入笔记本电脑的真实设备上模拟数据。您将能够设置自定义位置数据(屏幕 1)并上传 GPX 文件以模拟设备上的运动(屏幕 2)。

[![xcode-geolocation-testing](img/929ec37d1acc850b3747109c40741259.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7rjnARi---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/uploads/2019/07/xcode.png)

#### *iSpoofer(适用于 Windows)*

iSpoofer 可以帮助您仅模拟物理设备的位置数据(即，它不使用仿真器模拟数据)。该工具的唯一问题是它不是免费的，只有 3 天的免费试用期。尽管如此，Windows 用户仍然可以使用它，而无需在 MacOS 上创建虚拟机。

[![geolocation-testing-tools](img/ee9e0c6d08b5d3f0bae98af2a1a42e0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5_erc66_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://djangostars.com/blog/uploads/2019/07/ispoofer.png)

### 地理定位应用测试级别

从上面可以看出，有三种测试地理定位应用的主要方法:API 调用，在使用模拟数据的仿真设备上，以及在使用真实数据的物理设备上。

#### *API 调用*

因为所有地理位置数据都是使用 API 交换的，所以必须测试设备用来通信和交换位置数据的调用。我们使用 Python 和请求库，但是任何手工测试工具，比如 Postman 或者任何编程语言的脚本都可以。当然，在测试 API 调用时，您既不需要仿真器，也不需要真实的设备。

#### *带有模拟数据的设备仿真器*

大多数地理定位测试使用具有模拟数据的仿真设备，因为结果是所有设备和仿真器最终生成 API 调用来与数据库通信地理定位数据。将模拟器与模拟数据一起使用是有道理的，但是您应该意识到这仍然是一种非常概念性的测试方式，并且只是真实世界条件的近似。

#### *具有真实数据的物理设备*

考虑到上述情况，物理设备上的真实数据测试无疑是地理位置测试的最重要级别，因为我们无法预测使用应用程序在现实生活中可能发生的所有情况。虽然昂贵且耗时，但如果有策略地使用，这种类型的测试可以帮助您在网络和设备运行条件远非理想的混乱环境中避免重大问题。

## 地理定位测试建议

总结一下地理位置测试，它绝对是任何基于位置的 app 都不可忽视的重要方面。幸运的是，通过一些努力，即使没有太多初始经验的人也可以掌握这个原则。请记住:

*   不要害怕地理定位测试。最有可能的是总共只有 4 个参数(即经度、纬度、海拔和时间)。
*   纬度(1st)和经度(2nd)有标准位置。记住它们，不要混淆它们。
*   真实数据不同于模拟数据。这是不一样的，所以至少有选择地用真实的设备和数据来测试你的应用。
*   测试时，如果无法模拟设备移动，可以简单地将目标对象移近设备。
*   请注意应用程序逻辑在后端是如何工作的。

## 地理定位的进一步阅读

*   [https://macwright.org/2015/03/23/geojson-second-bite.html](https://macwright.org/2015/03/23/geojson-second-bite.html)了解更多关于 GeoJSON 的信息
*   [https://gisgeography.com/gis-formats/](https://gisgeography.com/gis-formats/)关于各种地理数据格式
*   [https://github.com/tmcw/awesome-geojson](https://github.com/tmcw/awesome-geojson)了解 github 存储库中与 geojson 相关的其他仪器

**这篇关于[地理定位测试](https://djangostars.com/blog/mobile-geolocation-testing/)的文章是由 Django Stars 的 QA 工程师 Vladislav Treshcheyko 撰写的。最初发布在[姜戈明星博客](https://djangostars.com/blog/)。**