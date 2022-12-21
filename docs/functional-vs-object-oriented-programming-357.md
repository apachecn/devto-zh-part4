# 函数式编程与面向对象编程

> 原文：<https://dev.to/krtb/functional-vs-object-oriented-programming-357>

# 大意

不同的编程语言有不同的特征，或者说*范式*，可以用来对它们进行分类。

今天我们来看看两个最常被谈论的，函数式的和面向对象的。

编辑#1:正如[亚当](https://dev.to/adam_cyclones)所指出的，这两者并不是对立的，它们只是不同而已！这些只是众多不同方法中的两种，它们都可以更好地服务于你(错别字是最好的双关语)，所以永远不要停止你的研究！

[![](img/6c494b4e9bed6844f2ec7b25aa7dcdf9.png)](https://i.giphy.com/media/KKbXQxrDFLLQQ/giphy.gif)

## 那个啥

首先，让我们看看一些共性

*   这两种方法都必须解决的问题
    *   数据
        *   你的程序想知道和使用什么
    *   行为
        *   你的程序想做什么，如何做

大多数编码训练营让你从 ruby 或 python 开始是有原因的，因为它们都是非常友好的语言。让我们使用 Ruby 进行演练吧！

[![Ruby from Steven Universe](img/e5e6a798b5a46f10fc835f1275fa818d.png)](https://i.giphy.com/media/85RTAcTGvcTxm/giphy.gif)

### 面向对象编程( *OOP*

*   通常用于生成对象实例的类
*   类定义了我们想要赋予对象的属性。
*   我们将给我们的类“*实例方法*”，它们将存在于我们的对象中。
*   这些实例方法可以在对象本身上调用。
*   “initialize”不是一个实例方法，而是告诉类在创建时它将具有什么属性。
*   我们对象的每个新实例都将包含预设的**数据**和**行为**
*   如上所述，数据将在创建时提供给我们的实例
*   然后，我们使用实例对象上的方法来操作它的数据
*   我们的对象包含的所有重要信息都安全地存储在它们的类中。如果你能想象自己是一家相当大的公司的工程师，有很多预先写好的代码，你也能看到这在哪里会派上用场。
*   **核心概念**
    *   抽象
    *   遗产
    *   多态性
    *   包装

[![meow,meow,meow](img/913bb1aee3b40f2ba31825fc2a2d5921.png)](https://i.giphy.com/media/3oz8xSqoR27S07xXUs/giphy.gif)

```
class Cat
  def initialize(name, mood)
    @name = name
    @mood = mood
  end

  def change_name(name)
    @name=name
  end

  def change_mood(mood)
    @mood = mood
  end
end 

kuma = Cat.new("Kuma", "mischievous")

```

### 上面发生了什么？

*   我们创建了一个 Cat 类，或者说 blue-print，用于处理我们的 Cat 实例
*   我们用一个名字和*情绪*初始化我们的猫。如果你曾经和猫一起出去过，你会知道它们的心情是除了它们的名字之外你记得的第二件事。

现在让我们改变他们的名字和心情！

```
kuma.change_name("Grapefruit")
kuma.name
# "Grapefruit"

kuma.change_mood("post-meal-happy")
kuma.mood
# "post-meal-happy" 

```

### 上面发生了什么？

*   使用 *change_name()* 方法允许我们更改 Cat 类实例对象的名称
*   使用 *change_mood()* 方法允许我们改变我们的 Cat 类实例对象的情绪
*   我们的 initialize 方法将接受我们传递给 Cat 对象的名称和情绪，并存储这些信息，以便我们以后可以轻松地访问它
*   *@* 符号用于实例变量，这些存在于一个对象(*实例*)中，不用传入就可以使用

### 功能编程( *FP*

*   使用一系列小方法，每个小方法完成自己的特定工作。
*   实现*组合*或者用较小的任务构建较大的任务，这也在 OOP 语言中使用，但是对于 FP 语言来说是至关重要的。
*   对象是不可变的，创建后不能更改
*   最适合数据科学工作
*   函数是可重用的
*   **核心概念**
    *   高阶函数
    *   纯函数
    *   递归
    *   严格和非严格评估
    *   类型系统
    *   对透明性有关的

<figure>

[![ants](img/f3e560a7c4235256fcac1c5e88dcaa47.png)](https://i.giphy.com/media/yUko4cwc6nkHu/giphy.gif)

<figcaption>Here we see small functions in their natural environment</figcaption>

</figure>

```
def doesOneThing(number){
 return number * number
end

```

### 上面发生了什么？

*   上面我们有一个纯函数的例子
*   只要给出相同的输入，将总是返回相同的值
*   函数中没有其他操作会改变我们的结果
*   一个立竿见影的好处是减少了代码行！
*   在 FP 中，我们将把我们所做的每一件事看作是通过对数据应用某种操作来转换数据，然后返回一个新的数据集
*   我们还经常默认使用一个 **map** 方法，而不是 **each** ，这将创建数据的一个新副本并将其存储在一个数组中。原始阵列完好无损，因为在 FP 中，我们数据的不变性是关键
*   不变性允许我们跟踪数据的价值

## 对照表

| 主题 | 冰点 | 面向对象的程序设计(Object Oriented Programming) |
| --- | --- | --- |
| 定义 | 强调功能评估 | 基于对象的概念 |
| 数据 | 不变的 | 易变的 |
| 模型 | [声明式编程](https://en.wikipedia.org/wiki/Declarative_programming) | [命令式编程](https://en.wikipedia.org/wiki/Imperative_programming) |
| 支持 | 支持并行编程 | 不支持 |
| 执行 | 语句可以以任何顺序执行 | 需要订单吗 |
| 循环 | 递归 | 环 |
| 基本要素 | 函数和变量 | 对象和方法 |
| 美国就业局(United States Employment Service) | 需要更多操作的东西很少 | 多事少操作 |

[感谢 EDUCBA！](https://www.educba.com/functional-programming-vs-oop/)

但是是哪一个呢？

好吧，根据这篇恶心的 [stackoverflow](http://stackoverflow.com/questions/2078978/ddg#2079678) 帖子，在考虑这两种非常流行的方法时，这里有一些关键概念要记住。

面向对象的程序设计(Object Oriented Programming)

*   在处理对事物的一组固定的**操作时很有帮助**
*   随着代码的发展，你*添加新的东西*
    *   这意味着您添加了新的类，这些类利用了现有的方法
    *   现有的类被搁置

危险区域

*   添加一个新操作可能需要编辑许多类定义来添加一个**新方法**

冰点

*   当你有一套**固定的东西**时很有帮助
*   随着代码的发展，你**在事物上添加新的操作** *。*
    *   这意味着您添加了新的函数，这些函数使用现有的数据类型进行计算
    *   现有功能保持不变

危险区域

*   添加一个新东西可能需要编辑许多函数定义来添加一个**新事例**

### TLDR；

面向对象编程使用类、对象和方法来实现它的崇高目标。另一方面，函数式编程利用大量的纯函数和变量来构建一个更大的整体。致力于这个或那个的语言都有自己的问题(看看你的 JavaScript😭).确保研究支持您正在寻找的任何语言或框架的社区，您将被引导到某种程度上正确的方向。也就是说，直到你碰到一只巨大的虫子并试图转向另一个方向...这是不明智的，但并非不可能。尽管如此，就像在 React 应用程序中添加 Redux 一样，你为什么要给自己增加额外的工作呢？计划，计划，计划！

[![Yoda](img/972702eb4484338bc93da0491c0750f0.png)](https://i.giphy.com/media/d2W7eZX5z62ziqdi/giphy.gif)