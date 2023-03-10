# 基于主体的建模。第一部分

> 原文：<https://dev.to/pablo/agent-based-modelling-part-1-48d6>

# 基于智能体的建模:一个简介

基于代理的建模(从现在开始我将称之为 ABM)是一个模拟的
范例，其中程序员创建一个具有特定
属性并能执行一组动作的实体。听起来很简单，而且它们比数学或统计模型更
直观，因为它们代表了我们所看到的
物体:环境中的单个元素。如果你玩过《模拟人生》或《模拟城市》电子游戏，你可能已经对某些反弹道导弹有所了解。几乎任何东西都可以建模:金融市场、
交通网络、社会互动、自然环境等等。一个简单的例子就是交通建模。我们可以创建三个不同的
模板:汽车、道路和行人，然后我们可以
模拟几辆车，在几条街道上与一些
行人互动。我告诉过你这听起来很简单！

基于代理的模型带来的好处有: <sup id="fnref1">[1](#fn1)</sup>

> 1.  Understanding of the capture system.
> 2.  Test the system's understanding of coherence and comprehensiveness.
> 3.  See if the behavior at the individual level creates a global pattern.
> 4.  Verify the theory against real data.
> 5.  Make a prediction of the system.

但是我们如何做到这一点呢？在这一系列的文章中，我将描述如何用 Python 和 Ruby 来做这件事。我想告诉你的是，你不一定非要使用那些编程语言，还有其他的选择，比如作为任何具有面向对象“思想”的编程语言的
(例如，
C++，MATLAB 或 Java，还有**许多**其他语言)或者像
NetLogo 或 Dramatis 这样的特定工具。 <sup id="fnref2">[2](#fn2)</sup>
( *免责声明*:我现在用 Python 已经有几年了，但是几个月前开始
对 Ruby 感兴趣。因此，这更像是一种最终迫使我学习 Ruby 的方式，我使用 Ruby 的方式可能不是你见过的最好的。)

让我们开始工作吧！

首先:你需要一个 Python 和/或 Ruby 的功能版本。在你的系统中安装这两种语言是可能的，互联网上有很多好的教程，我强烈推荐
不要依赖互联网连接来运行一些脚本。万一你
做不到，不想做，或者认为你会失败得很惨，那么
有一个很棒的网站——叫做[*repl . it*](https://repl.it/)——它可以让
你毫不费力地运行脚本。这对于学习
来说很方便，但是对于更重要的项目来说是不可扩展的。

现在，*概念:*我们将使用**对象**，这个想法我将在第一篇文章中
描述。

在编程环境中，对象是包含数据
(称为*属性*或*属性*)和过程(称为
*方法*)的东西。我们可以创建一个对象，然后根据需要创建该对象的尽可能多的
实例，所有实例都遵循相同的
模式。让我们看看这如何转化为 Python 和 Ruby。
假设我们想要对前面的流量建模示例进行建模。我们需要汽车、道路和行人。为了简单起见，这里汽车将
只有模型和颜色(属性)，并且它们将能够
移动(方法)；道路、名称和规定数量的车道，它们
可以是拥挤的或不拥挤的；而行人，一个速度，他们可以
移动或静止。

在 Python 中，汽车将如下:

```
class Car:
    def __init__(self, model, color):
        self.model = model
        self.color = color
        self.moving = False
    def change_state(self):
        self.moving = not self.moving
        if self.moving:
            print("The car is moving now")
        else:
            print("The car stopped") 
```

Enter fullscreen mode Exit fullscreen mode

我们使用保留字“类”来定义汽车类。
__init__ 方法是 Python 特有的，并且在每次
创建一个汽车实例时执行，即使用其
属性初始化一辆汽车。我们还需要一个改变汽车状态的方法:
“change _ state。”
在 Ruby 中，道路可以这样编码:

```
class Road
    def initialize(name, lanes, state)
        @name = name
        @lanes = lanes
        @state=state
    end
    def get_state 
        puts "Is the road congested? #@state"
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

语法类似于 Python 中的语法，但是多了一些强制的
单词和字母。像以前一样，我们有一个初始化器，还有一个方法
给我们道路的状态。(现在不要为一条
真实道路的琐事而烦恼，如果它不是很现实的话。我想让你了解
如何用 Ruby 建模。)
在 Python 和 Ruby 中，行人可以建模如下:

```
class Pedestrian:
    def __init__(self, speed):
        self.speed = speed
        self.moving = False
    def moving(self):
        self.moving = not self.moving
        if self.moving print("The car is moving now") else print("The car
stopped") 
```

Enter fullscreen mode Exit fullscreen mode

```
class Pedestrian
    def initialize(speed, state)
        @speed=speed
        @state=state
    end
    def change_state
        puts "State was #@state"
        @state = !@state
        puts "State is #@state now"
        puts "\n"
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

酷毙了。我们知道如何为我们的对象创建模板——类——和
创建对象的副本——实例，我们如下进行(分别在
Ruby 和 Python 中):

```
ped = Pedestrian.new(0, false)
ped.change_state 
```

Enter fullscreen mode Exit fullscreen mode

```
car1 = Car('Buik', 'red')
car.change_state() 
```

Enter fullscreen mode Exit fullscreen mode

在下一篇文章中，我将一步一步地在一个虚拟模型上展示我接近这类系统的策略。值得注意的是，当你必须将个人行为
与环境、特定条件或一系列观察到的
行为联系起来时，关键点就来了。
在这个问题上，有一些工具在这个过程中有所帮助。我的
背景是工科的，所以我平时喜欢图，比如 UML
图。UML 代表统一建模语言，有助于将复杂软件系统的架构、设计和实现可视化。好处之一是，UML 是一种标准化的建模语言，因此可以跨不同的编程语言使用。软件开发人员会理解它，并可能想把它应用到他们的工作中。互联网上有许多关于 UML 的好教程
， <sup id="fnref3">[3](#fn3)</sup> 和专门的工具，可以和你使用的语言一起使用
来从你的代码中创建图表。

下期帖子再见！:D

* * *

1.  这里引用*[↩](#fnref1)*

**   参见 [*本维基百科页面*](https://es.wikipedia.org/wiki/Comparaci%C3%B3n_de_software_de_modelaje_basado_en_agentes) 了解更多信息，本文章综述最新技术水平了解更准确信息:Abar，s .，Theodoropoulos，G. K .，Lemarinier，p .，& O'Hare，G. M. (2017)。基于主体的建模和模拟工具:最新软件综述。计算机科学评论，24，13-33。 [↩](#fnref2)

    *   [*lucidchar 网站*](https://www.lucidchart.com/blog/types-of-UML-diagrams) ，以及 [*tallyfy 网站*](https://tallyfy.com/uml-diagram/) [↩](#fnref3)*