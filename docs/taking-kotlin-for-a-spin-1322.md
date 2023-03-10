# 带着科特林去兜风

> 原文：<https://dev.to/renegadecoder94/taking-kotlin-for-a-spin-1322>

最近，我的妻子拿起了一个 Kindle Fire，我觉得为它编写一个应用程序会很有趣。事实上，你可能还记得[很久以来我一直试图为她制作一个图书馆应用程序](https://therenegadecoder.com/code/the-passive-income-library/)。嗯，还有什么比带着科特林去兜风更好的方法呢？

## 手机 App 开发

我与移动应用程序开发的关系相当短暂。事实上，我唯一的一次体验是在 2016 年本科最后一个学期，当时我开发了一个 Android 应用程序来与智能锁互动。

当时，我只熟悉 Java、C、Verilog 和 x86。郑重声明，这是追求计算机工程学位的人所期望的重复。不管怎样，除了这些语言之外，我没有太多的经验，所以我选择了 Android 路线来利用我的 Java 经验。

对于那些好奇的人，我们使用了一个 Arduino 来驱动一个螺线管锁。Arduino 有一个蓝牙附件，我们用来通过 Android 移动应用程序与锁进行通信。老实说，这个项目非常基础，但是我和一个多学科团队一起从零开始设计一些东西，从中获得了很多乐趣。

快进到今天，你会发现变化不大——至少直到最近。作为一名通晓多种语言的人，我决定不仅要尝试移动应用程序开发，还要尝试 Kotlin。

## 重访府库

早在 2016 年初，我决定为我当时的女朋友摩根(Morgan)制作一个图书馆应用程序 PopLibrary。她想要一个可以用来对她的藏书进行基本分类的东西，这样她就可以像图书馆一样把它们借给她的学生。

### 失败之路

为了让事情变得有趣，我决定扩展这个工具，这样我就有可能从中赚钱。特别是，我想提供所有 Morgan 想要的功能，并增加书籍推荐等功能。这些推荐会绑定到我的 Amazon Associates 账户，这将为我挣大钱——至少我是这么认为的。

结果是，在两年的时间里，我无法将这个应用程序变成现实。我想我只是不具备能够编写全栈应用的技能，而这种现实从未真正发生过。毕竟，我曾三次尝试实现弹出式图书馆:

*   C#中的 Windows 应用程序
*   JavaFX 应用程序
*   Laravel 网络应用

试了三次，我放弃了。然后，摩根买了一台 Kindle Fire，我再次兴奋起来。不管出于什么原因，我觉得事情可能会有所不同。

### 换挡要求

失败三次后，我决定这一次先实现摩根想要的所有功能。然后，我会设法看看我是否能在副业上赚一点钱。也就是说，府库应该能够做以下事情:

*   显示用户拥有的图书列表
*   允许用户添加和编辑他们自己的书(标题，图像等。)
*   本地保存图书数据(长期目标:云存储)
*   提供搜索和过滤功能，以更改显示的图书
*   允许用户将图书借给其他用户
*   用相机扫描条形码

在这一点上，我已经实现了前几个功能，我只在应用程序上工作了大约两天。有经验是值得的！

## 科特林观感

总之，我相信你不是来了解我的项目的。你来这里可能有很多原因，比如:

*   看看是否值得去科特林看看
*   看看第一次接触这门语言的人是怎么想的
*   分享一些成长的烦恼

不管原因是什么，这是我目前对科特林的看法。

### 把自己从虚无中拯救出来

几乎每一种我有幸接触过的语言(C，C#，Java，Python，JavaScript，PHP 等等。)已经有了这个`null`的概念。对我来说，`null`才有意义。毕竟，当引用类型的值不存在时，这是给予引用类型的最佳值。例如，如果您向用户提供一个表单，而他们选择不填写某些可选元素，那么这些元素的值应该是`null`——而不是某个任意值。

嗯，至少，这是我对`null`的理解。我没有意识到这可能是一个问题。事实上，有大量文献认为`null`是计算机科学中最大的错误之一。奇怪的是，直到 2017 年我在 Swift 写我的 [Hello World 文章之前，我都没有听说过这种对`null`的敌意。](https://therenegadecoder.com/code/hello-world-in-swift/)

#### 引入可空值

由于 null 可能引入的问题，许多现代语言都试图消除它们。至少，像 Kotlin 和 Swift 这样的语言已经将`null`封装在对象中，这引入了一些安全检查。换句话说，没有 NullPointerExceptions(npe ),除非你主动要求。

特别是在 Kotlin 中，您可以使用问号
将任何变量设置为`nullable`

```
var count: Int? = null 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们创建了一个名为`count`的变量，类型为`Int?`，意味着 count 可以是一个数字或`null`。当使用`count`时，你可能想在它上面调用一个方法，比如减量方法:

```
count.dec() 
```

Enter fullscreen mode Exit fullscreen mode

理想情况下，这个方法会递减`count`，但是`count`实际上不是一个数字——它是`null`。在大多数语言中，我们会得到一个 NPE，但 Kotlin 实际上将无法编译。为了适应这一点，我们必须稍微改变语法:

```
count?.dec() 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们对`count`进行了安全调用。如果`count`是`null`，整个链会返回`null`，但是我们不会得到一个 NPE。

#### 实际上可空

现在，这对于憎恨者来说是一个很棒的功能，但是我发现它偶尔会让生活变得更困难。例如，我创建了一个类似下面这样的`Book`类:

```
data class Book( 
  val isbn13: String? = null, 
  val title: String? = null, 
  val author: String? = null, 
  val editor: String? = null, 
  val language: String? = null, 
  val coverImageURL: String? = null, 
  val pageCount: Int? = null, 
  val dateOfPublication: Date? = null
) { } 
```

Enter fullscreen mode Exit fullscreen mode

我将每个字段都设置为`nullable`，因为我不想用任意数据填充这些字段。换句话说，我不想将字符串字段设置为空字符串或其他任意数据，因为我必须记住默认值是什么，以便以后检查。相反，我将所有未填写的字段保留为空，并在出现空问题时进行处理。

也就是说，我遇到了一些问题。例如，如果我想检查一个字符串是否包含在标题中，我可能会这样写:

```
title?.contains("Gatsby", true) 
```

Enter fullscreen mode Exit fullscreen mode

当然，这里的问题是这个表达式可能返回 true、false 或 null。在像 JavaScript 这样的语言中，条件可能能够处理这种模糊性，但在 Kotlin 中却不行。因此，我们基本上必须使用`Elvis`操作符:
将 null 值强制为 false

```
title?.contains("Gatsby", true) ?: false 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，如果 title 是`null`，那么表达式返回 false。

现在，想象有某种条件来检查这些术语。很快，我们得到了一个混乱的表达式，它要求`Elvis`操作符处理任何类型的空可能性。最后，我将上面的表达式包装在一个函数中，并使用 OR 运算符将不同的可能性联系在一起:

```
checkContains(title, str) 
  || checkContains(author, str) 
  || checkContains(editor, str) 
  || checkContains(language, str) 
```

Enter fullscreen mode Exit fullscreen mode

显然，这并不理想，但是没有 npe！我想更多经验丰富的 Kotlin 开发人员会有更好的方法来处理这个问题，但我只是想让一个应用程序运行起来。

### 用运算符重载比较对象

虽然 Kotlin 对我来说最有趣的特性是空安全，但我不得不说运算符重载紧随其后。通常，我会完全反对操作符重载，因为它给语言带来了不必要的复杂性，但是我认为 Kotlin 在这个特性上做得很好。

不幸的是，要想了解这个特性，您需要对 Java 的工作原理有一点了解。尤其需要熟悉对象的`equals()`方法和可比接口的`compareTo()`方法。

#### 对象对等

在 Java 中，所有的对象都有一个`equals()`方法，所以可以用另一个对象来测试它们是否相等。当然，`equals()`的替代物是`==`操作符，但是它有不同的用途。`==`操作符不是测试两个对象是否等价，而是测试两个对象是否具有相同的身份。换句话说，如果两个对象有相同的身份，它们实际上是一个有多个别名的对象。

在科特林语中，`==`普遍用于表示平等。同时，身份检查由`===`操作符处理。因此，`==`和`equals()`是同义词。一旦我们实现了`equals()`方法，我们就可以使用`==`操作符来代替它:

```
val x = Date(1000)
val y = Date(1000)
x.equals(y) // Evaluates equality based on equals() 
implementation x == y // Does the same exact thing 
```

Enter fullscreen mode Exit fullscreen mode

事实上，IntelliJ 提倡使用操作符而不是方法，我是它的超级粉丝。但是等等，还有更好的！

#### 物体比较

在 Java 中，当我们想要比较两个对象时——比方说，为了排序的目的——我们通常确保实现了`Comparable`接口。作为该接口的一部分，我们必须覆盖`compareTo()`方法，该方法接受一对对象并返回一个表示它们之间关系的数字。当两个对象等效时，该方法应返回 0。同时，当调用对象是“更大”的对象时，该方法应该返回一个正数，否则返回一个负数。

决定哪个物体“更大”取决于我们使用的物体的类型。例如，字符串“apple”比字符串“carrot”小，因为字母顺序规定“apple”在前面。换句话说，`compareTo`应该表现如下:

```
"apple".compareTo("carrot") // Returns some negative number
"carrot".compareTo("apple") // Returns some positive number 
```

Enter fullscreen mode Exit fullscreen mode

无论如何，`compareTo`有点令人困惑，Kotlin 通过引入一些操作符很好地缓解了这种困惑。使用与上面相同的例子，我们可以使用关系运算符来比较“苹果”和“胡萝卜”:

```
"apple" > "carrot" // false
"apple" < "carrot" // true 
```

Enter fullscreen mode Exit fullscreen mode

就我个人而言，我用这个来按照他们的 Lexile 级别分类书籍。在我的项目中，`Lexile`是一个实现`Comparable`的类。为了比较它们，我用它们的数值:

```
override fun compareTo(other: Lexile): Int { 
  return this.toInteger() - other.toInteger()
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我可以比较两个`Lexile`对象如下:

```
val lex1 = Lexile(270, Lexile.LexileType.NA)
val lex2 = Lexile(400, Lexile.LexileType.NA)
assertTrue(lex1 < lex2) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我觉得这很酷。

### 告别啰嗦

人们对 Java 最大的抱怨之一就是语言的冗长。特别是，变量定义需要大量的细节:

```
ArrayList<Integer> myList = new ArrayList<Integer>() 
```

Enter fullscreen mode Exit fullscreen mode

为了创建这个列表，我们必须指定大量信息:

*   键入两次
*   泛型类型，两次
*   名字
*   关键词(新)
*   运算符(=)
*   构造器

当然，这一行代码的长度会呈指数增长，这取决于类型名称的长度、嵌套泛型类型的数量以及构造函数的大小等因素。

为了解决这个问题，Kotlin 引入了一个更简洁的语法:

```
val list = arrayListOf<Int>() 
```

Enter fullscreen mode Exit fullscreen mode

显然，这里发生了很多事情，但是重要的是要注意到冗余信息的缺乏。我们不指定类型，但我们有选择权。此外，填充`ArrayList`要简单得多:

```
val list = arrayListOf<Int>(5, 6, 8, -4) 
```

Enter fullscreen mode Exit fullscreen mode

现在，虽然精简的冗长很好，但我还想指出，Kotlin 还引入了两个新的关键字:`val`和`var`。当我们想要将一个变量标记为不可变或只读时，我们使用`val`(想想 Java 中的`final`),将一个变量标记为可变时，我们使用`var`。

### 掌握流量控制的艺术

如果说我从与编程语言打交道中学到了什么，那就是大量的流控制机制。例如，对于初学者来说，有 if 语句和循环。此外，还有一些有趣的机制，如 goto 和 switch 语句，它们为流控制提供了更多的选项。

尽管如此，Kotlin 还是向我介绍了另一种流量控制机制:`when`。它本质上是一个`switch`语句，但是我发现它的语法更加简洁:

```
override fun toString(): String { 
  return when (this.type) { 
    LexileType.NA -> level.toString() + "L" 
    else -> type.name + level.toString() + "L" 
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个方法中，我们覆盖了`toString()`方法，在两种可能的情况下返回一个字符串:

*   类型为 NA
*   类型是其他任何东西

特别是，我们返回接受该对象类型的`when`语句的结果(与前面的`Lexile`类相同)。如果类型是 NA，我们返回一些字符串。否则，我们返回另一个字符串。

在我看来，`when`语句很聪明，因为它删除了很多你可能在`switch`语句中发现的冗余代码:break、return 等。很自然，我一直在使用它们，因为 IntelliJ 实际上更喜欢它们而不是 if 语句链。而且，我只是觉得他们很酷。

## 判决结果

现在，我很喜欢科特林。零安全功能很难解决，但其他一切都很好。Kotlin 是我热爱 Java 的一切，也是我热爱 Python 等高级语言的一切。随着大部分样板文件的清除，我觉得我真的可以快速构建一些东西，同时还可以依赖编译语言标配的所有惊人的静态分析实用程序。

总之，我们很快就会看到我的感觉。我可能只是在蜜月期，但我真的很喜欢这门语言。这让我想起了我第一次开始使用 C#时的感受——两者都是对 Java 的巨大改进。

由于这是我第一次真正像这样深入地复习一门语言，我真的没有什么文章可以推荐。不管怎样，这里有几篇文章我很想让更多人看到:

[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 使用模运算的石头剪刀布

### 杰里米·格里夫斯基 3 月 18 日 19 时 11 分阅读

#python #beginners #tutorial #programming](/renegadecoder94/rock-paper-scissors-using-modular-arithmetic-2j4p)[![renegadecoder94 image](img/4ad5302c8e9c3fbd47325dc4154af595.png)](/renegadecoder94) [## 回想我第一学期的教学

### 杰里米格里夫斯基 1917 年 5 月 7 日 17 分钟阅读

#java #leadership #teaching #computerscience](/renegadecoder94/reflecting-on-my-first-semester-of-teaching-1e91)

此外，如果你喜欢这篇文章，给它一个分享！到它发表的时候，我可能会对这个问题有完全不同的看法，所以让我们也进行一次对话。对于那些想要建立社区的人，请访问[会员页面](https://therenegadecoder.com/members/)并注册！对于不太忠诚的人来说，还有一个[时事通讯](http://eepurl.com/dB-ABr)。一如既往，谢谢你的来访！

带着科特林兜了一圈的帖子最先出现在变节的程序员 T2 的网站上。