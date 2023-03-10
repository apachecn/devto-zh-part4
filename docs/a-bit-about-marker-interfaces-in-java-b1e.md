# 关于 Java 中的标记接口

> 原文：<https://dev.to/iuriimednikov/a-bit-about-marker-interfaces-in-java-b1e>

又见面了！这次我想谈谈 Java 中的标记接口。每个 Java 开发人员都见过它们，尽管不是每个人都知道这种类型的接口有一个特定的词。在这篇文章中，我将重点讨论以下几点:什么是标记接口，我们有哪些内置的标记接口，以及它们与另一个概念注释的比较。

## 什么是标记接口？

基本上，标记接口是空的，它们没有方法或字段(例如常量)。我们使用它们来提供关于对象的运行时信息。举个例子，我拿一个内置的标记接口 *java.lang.Cloneable* 来说。它本身并不克隆一个类，尽管有一个每个 Java 类都继承的方法 *Object.clone()* 。但是通过使用 Cloneable 接口，我们指出一个类实现了一个克隆逻辑，并且调用那个 *clone()* 方法来制作那个类的实例的一个字段对一个字段的副本是合法的。否则，调用未实现 Cloneable 的类的 *clone()* 方法将导致抛出*CloneNotSupportedException*。让我们先来看看 Java 中内置的标记接口。

## Java 内置标记接口

Java 为我们提供了 3 个内置的标记接口:

*   可克隆的
*   java.io .序列化
*   java.rmi .远程

你可以遵循 Javadoc 来实现这些接口，并确保它们完全符合上述标记接口的定义。因此，我们已经在前面的章节中讨论了可克隆接口。让我们快速看一下剩余的接口。

*java.io.Serializable* 用于使对象可序列化。序列化是将 Java 对象写成一个字节序列，并从字节流中读取这些对象。通过 *ObjectOutputStream* 实现写(或序列化)，通过 *ObjectInputStream* 实现读(或反序列化)。所以为了使一个类*可序列化*，我们需要实现这个标记接口。否则，当调用*object output stream . writeobject()*时，我们会收到 *NotSerializableException* 。顺便说一下，一个可序列化的类的所有子类(子类)也是可序列化的(我们将在一次会议上讨论这一点)。

*java.rmi.Remote* 稍微复杂一点，比较少见，但是在这里简单说一下还是有意义的。此接口用于标识可从非本地虚拟机调用其方法的接口。任何远程对象都必须直接或间接实现此接口。只有那些在*远程接口*(这是一个扩展 java.rmi.Remote 的接口)中指定的方法是远程可用的。你可以在这本优秀的甲骨文[远程接口设计教程](https://docs.oracle.com/javase/tutorial/rmi/overview.html)中读到更多关于远程接口的内容。

Java 中还有另一个看似相似的概念——*注释*。在下一节中，我们将概述注释和标记接口之间的区别。

## 标记接口与注释

Java 注释可能看起来类似于标记接口，因为它们允许我们做同样的事情。我们可以对任何类应用注释，以表明它可以用在特定的逻辑中。有时会有强烈的声音要求放弃标记接口，转而使用注释。但是它们是 100%相似吗？考虑下面的代码片段:

```
class Document implements Serializable{

    //..some fields
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们使我们的文档可序列化，并可以像我们之前所说的那样用序列化来写/读它。但是让我们考虑一下，如果我们制定几个具体的文件:

```
class Spreadsheet extends Document{}

class Diagram extends Document{}

//... 
```

Enter fullscreen mode Exit fullscreen mode

如果我们不想让图可序列化呢？但我们不能在这种配置下。标记接口遵循多态性逻辑，因此 serializable 类的所有子类也是可序列化的。另一个逻辑是，如果我们使用示例 *@Serializable* 注释来代替:

```
class Document{}

@Serializable
class Spreadsheet extends Document{}

class Diagram extends Document{} 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下*文档*也可以是标记接口，但是我们会在后面的帖子中讨论这个。

## 结论

在这篇短文中，我们讨论了标记接口:什么是标记接口，什么是内置标记接口，以及它们和注释之间的区别。所有的 Java 开发人员在某种程度上都编码了标记接口，但并不是每个人都注意到它实际上是一个独立的模式。希望这篇短文让你对这个概念更感兴趣。你也可以在我的博客中阅读这篇关于 Java 标记接口的文章的原始版本。祝您愉快！

## 参考文献

*   高拉夫·米格拉尼。*Java 中的标记接口*。极客之福。[读到这里](https://www.geeksforgeeks.org/marker-interface-java/)
*   迈纳克·戈斯瓦米。有更好的标记方法吗？ (2012)。DZone。[读到这里](https://www.javacodegeeks.com/2012/10/is-there-a-better-approach-to-marker.html)