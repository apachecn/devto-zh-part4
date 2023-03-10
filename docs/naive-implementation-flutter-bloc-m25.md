# 阻塞的简单实现(在颤振中)

> 原文：<https://dev.to/mzaink/naive-implementation-flutter-bloc-m25>

确实如此。确实如此。外面有比这好得多的资源。我不否认这一点。但这是我对集团模式的理解。所以，如果我做得不好，请原谅我。但是，如果这篇文章消除了你对集团模式的担忧，那就请鼓掌吧。

## 什么是 BLoC？

业务逻辑组件。是啊。那是一些看起来可怕的扩张。不，不要费心去记住它。相反，为了便于理解，请允许我给你一个简单的类比。

假设你正在制造一辆汽车。在这本书里，你必须设计你的加速装置，这样每当有人踩下踏板时，你的车就会以与踏板走过的距离成比例的加速度向前冲。

<center>
![](img/bf1569dfade0c7061ad950294853c3aa.png)

<figure>

<figcaption>That’s how you do your pedals. 😌 (source : [giphy.com](giphy.com))</figcaption>

</figure>

</center>

现在，你会如何设计这样一个机制？

这很简单。您只需将拉紧的电线/连接器从踏板连接到发动机的通风口。然后，每次踩下踏板，通风口就会进一步打开，让更多的空气进入。

这里的关键词是:

1.  踏板(水槽)
2.  连接器(控制器)
3.  通风口(水流)

因此，让我们在我们的应用程序中模拟同样的情况。(为简单起见，假设您必须逐步操作踏板，即要达到 0–100km/h，您必须踩下踏板 10 次。每走一步意味着速度增加 10 公里/小时。)

## 阻挡汽车踏板 App

启动你的新颤振项目(称之为“块 _ 车 _ 踏板”，只是为了有创意，你看😜)并清除 main.dart 文件(lib/main.dart)中任何以前的代码

<center>

<figure>![](img/8c753e2dc9fd3d4e511e6ed991dd0c6c.png)

<figcaption>That’s the structure of your app and our main file would look pretty flushed! 😜</figcaption>

</figure>

</center>

继续前进。让我们为我们的应用程序创建一个基本设置。

<center>

<figure>![](img/b4eef7d129efd7e8bf602e7d87e01b0a.png)

<figcaption>That’s how your main.dart shall look thereafter. 👽</figcaption>

</figure>

</center>

<center>

<figure>![](img/18e3955b60a9ace597c93c6303f20caf.png)

<figcaption>After running the above code. 📱</figcaption>

</figure>

</center>

好吧。所以到目前为止，按下这些按钮没有任何作用。理想情况下，向上按应该将速度增加 10(如上面踏板示例中所解释的)，向下按应该将速度降低 10。让我们来看看如何实现这一目标。

从这里开始，我们将从 BLoC 模式实现开始，当用户按下按钮时更新速度。

(抱歉，你可能还必须假设，要降低你的车速，你必须按下“向下”按钮，其作用类似于前面解释过的油门踏板，尽管这不是一辆真正的汽车的工作方式。它应该只有一个踏板。😬😬)

## BLoC 模式实现

如下创建文件夹结构，并在“bloc_pattern”文件夹下添加两个 dart 文件，即“speed_event.dart”和“speed_bloc.dart”。

<center>

<figure>![](img/e3e4c8635e3af17fa803134f2b50e199.png)

<figcaption>Please ignore the ‘misc’ folder though. You don’t need to add it. 🙏🏽</figcaption>

</figure>

</center>

现在，我们需要为模式创建事件。这些事件应为:

1.  SpeedUpEvent
2.  速度下降事件

这些只是继承了另一个名为“SpeedEvent”的抽象类的空类。这同样没有实现。

上面的类除了帮助我们确定事件的类型之外没有其他工作。(在这里，事件意味着踩地板或松开油门，或者在我们的例子中，按下两个按钮中的一个)。

### 1。速度 _ 事件.飞镖

因此，请转到“speed_event.dart”文件，并加入以下代码。

<center>

<figure>![](img/fffd6b6606d7acd92aea1abc467f6b8c.png)

<figcaption>That’s it. Go grab some coffee after so much heavy lifting. 😂</figcaption>

</figure>

</center>

### 2。速度 _ 区块.飞镖

在这个文件中，我们可以看到一些块模式的实现。不要陷入困境。这将是非常愚蠢的简单。但是在我们写任何代码之前，让我带你看一下我们的基本方法是什么。

<center>
![](img/c26eebc1ecadb247244dba8f0c580c8e.png)
</center>

所以这里的想法是有一组控制器，它们通过它们的“接收器”记录我们的输入，并通过它们的“流”产生一些输出。如前所述，事件对应于用户“按下按钮”或类似动作。

在这里，每当一个事件来临时，比如说用户按下了 UP 按钮，我们通过它的接收器将它添加到 EventController 来注册这个事件(实际上我们将它添加到它的内部流中，但这没关系。假设我们将它添加到控制器中)。然后，该事件被传播到 StateController，它接收该事件，分析需要做什么(在我们的例子中，增加汽车的速度)，然后对状态变量的值(在我们的例子中，汽车的速度)进行相应的更改。这种变化然后被汽车拾取，汽车加速。Pppppoooffffff！🏎

对了，想知道耳朵的事吗？这叫做倾听者。它只是监听它所连接的流，然后，每当一些数据被添加到流中时，这个监听器中包含的方法就会触发我们。你很快就会明白这意味着什么。(亲提示:简单到傻逼地狱)。

这是它的理论部分。现在，您可以调出“speed_bloc.dart ”,并输入以下代码:

<center>

<figure>![](img/76e3aa4f049ccaf18e1e07a32d8b0f10.png)

<figcaption>Part 1: speed_bloc.dart 🌜</figcaption>

</figure>

</center>

<center>

<figure>![](img/f63d86f86281cbb16699ea30509a2812.png)

<figcaption>Part 2: speed_bloc.dart 🌛</figcaption>

</figure>

</center>

就是这样。不多不少。您阻塞模式实现已经准备好了。好好搓你的手。你做了惊人的工作。(🕺🏼/💃🏽)

## 更新 main.dart 就大功告成了！

在本节中，我们将更新 main.dart 文件以利用我们编写的块。为此，我们将使用一个名为`StreamBuilder`的小部件，它只是接收一个流，并根据流中的数据返回一个小部件(按照我们的代码)。

它需要三个重要的命名参数:

1.  `stream`:在我们的例子中，我们会给它一个`_stateController`的流，或者像我们之前为它编码的一个 getter，例如`speedStream`。

2.  `initialData`:设置为零(0)。它只是最初出现在流中的数据。(有助于在首次创建流且未处理任何事件时避免空输入)。

3.  `builder`:这个函数以`BuildContext`和`AsyncSnapShot`为参数，返回一个 widget。

酷吗？那么让我们更新我们的“main.dart”。

<center>

<figure>![](img/80fbb3c4eb61654f1bfa40db10982703.png)

<figcaption>Part 1: main.dart 🌗</figcaption>

</figure>

</center>

<center>

<figure>![](img/6c9ff0eb4e02086537e87a564d0f503d.png)

<figcaption>Part 2: main.dart 🌓</figcaption>

</figure>

</center>

去管管你那可爱的孩子吧。你的车可以在世界各地加速(0-120 公里/小时)😜)

【无耻插件:如果你真的喜欢这篇文章或者觉得它有用，请一定要喜欢并分享这篇文章。]

【其实不算无耻。可耻还是无耻？随便啦。]

[您也可以继续从我的 GitHub repo:[impairable-span/bloc _ pattern](https://github.com/ineffable-span/bloc_pattern.git)中克隆代码]

<center>

<figure>![](img/a10ad106c8704fe3b63f27e6f731df30.png)

<figcaption>That’s how your app should look like.</figcaption>

</figure>

</center>

就是这样。请不要介意视频质量差。我在模拟器上拍摄的，因此结果。不管怎样？如果一切顺利，你将会在你的手机/模拟器上看到它，这太棒了。

干杯！