# Java 中的 8 种基本类型

> 原文：<https://dev.to/renegadecoder94/the-8-primitive-types-in-java-10cl>

当我开始编辑几年前的 Java 基础知识系列时，我认为将一些真正详细的信息放到他们自己的帖子中是有意义的。这样，初学者的内容更容易理解。首先，我拿出了所有关于 Java 中 8 种基本类型的信息。

## Java 原语类型

正如本文的标题所示，Java 语言内置了 8 种基本类型。在下面的小节中，我们将不分先后地看一下所有的八种基本类型。对于每个基本类型，我们将描述如何使用它以及它的限制。

#### Int 原语类型

首先，Java 整数是 32 位有符号(表示正或负)值，它们由关键字`int`表示:

```
int someNumber = 10; 
```

Enter fullscreen mode Exit fullscreen mode

当然，像所有基本类型一样，整数也有其局限性。因为它们是 32 位的，所以我们看到的值范围是从-2，147，483，648 到 2，147，483，647。那可是一大堆数字啊！当然，我们可以在 DrJava 的交互面板中使用一个方便的技巧来确认这个范围:

```
Integer.MAX_VALUE // Prints 2,147,483,647
Integer.MIN_VALUE // Prints -2,147,483,648 
```

Enter fullscreen mode Exit fullscreen mode

自然，`int`可能是用于简单计算的最常见的整数类型。如果您需要更多范围，请参见下面的`long`。

#### 双原语类型

与`int`不同，Java 双精度是 64 位浮点值，它们由`double`关键字:
标记

```
double someNumber = 110.55; 
```

Enter fullscreen mode Exit fullscreen mode

提醒一下，浮点数只是实数。换句话说，当我们使用双精度浮点数时，我们就能接触到小数点。

由于双精度是 64 位的，我们可以访问比整数多得多的数字。如果我们使用同样的交互面板技巧，我们可以发现潜在值的范围有多大:

```
Double.MAX_VALUE // Prints 1.7976931348623157E308
Double.MIN_VALUE // Prints 4.9E-324 
```

Enter fullscreen mode Exit fullscreen mode

请记住，负指数意味着极小的数字——与极小的负数相反——所以我们看到的范围与整数并不完全相同。

一般来说，`double`是 Java 中浮点值的默认选择。备选是`float`。

#### Char 原语类型

正如我们已经看到的，Java 字符代表 16 位字符，它们由关键字`char`标记:

```
char someCharacter = 'f'; 
```

Enter fullscreen mode Exit fullscreen mode

Java 中的所有字符都用单引号定义。同时，双引号用于定义字符串——我们稍后会谈到这一点。

像往常一样，我们可以使用下面的代码片段找出字符范围:

```
Character.MAX_VALUE // Prints '???' 
Character.MIN_VALUE // Prints '' 
```

Enter fullscreen mode Exit fullscreen mode

为了理解这个范围，我们可以将结果转换成一个整数(稍后会详细介绍):

```
(int) Character.MAX_VALUE // Prints 65535
(int) Character.MIN_VALUE // Prints 0 
```

Enter fullscreen mode Exit fullscreen mode

事实证明，`char`类型是 Java 中唯一的无符号类型。换句话说，一个字符的范围可以从 0 到 65，535，其中每个数字映射到一个特定的字符。为了创建这个列表之外的字符，Java 可能会组合成对的字符。查看 Java 中的[反转字符串的一些例子。](https://therenegadecoder.com/code/reverse-a-string-in-java/)

#### 字节原语类型

我们在聊二进制的时候，谈到了位的概念。一组八位组成一个字节，Java 支持它作为一个基本类型。`byte`类型本质上只是一个范围从-128 到 127 的 8 位数字。正如所料，字节由关键字`byte`表示:

```
byte someByte = 20; 
```

Enter fullscreen mode Exit fullscreen mode

和往常一样，我们可以使用下面的代码片段来确认一个`byte`类型的范围:

```
Byte.MAX_VALUE // Prints 127
Byte.MIN_VALUE // Prints -128 
```

Enter fullscreen mode Exit fullscreen mode

根据我的经验，`byte`类型对于读取和处理原始数据很有用。然而，一般来说，我们可能用不到它，因为潜在值的范围很小。

#### 简短的原始类型

`short`类型是另一种整数类型，但是它比 int 类型占用更少的空间。事实上，它占据了 16 位的一半空间，并且由关键字`short`标记:

```
short someNumber = 11; 
```

Enter fullscreen mode Exit fullscreen mode

就范围而言，`short`类型只记录整数的一半位，但我们可以像往常一样确认:

```
Short.MAX_VALUE // Prints 32767
Short.MIN_VALUE // Prints -32768 
```

Enter fullscreen mode Exit fullscreen mode

出于实用目的，一个`short`只有 65，546 个可能值。当内存和磁盘空间不足时，通常使用`byte`和`short`。否则，使用`int`是安全的，因为它是声明整数时的默认类型。

#### 长原始型

另一端是`long`原始类型。这种类型表示非常大的整数，我们可能需要比`int`类型所能提供的更大的值。`long`类型是一个 64 位有符号整数，这意味着值的范围是五次方。

自然，我们用关键字`long`来表示多头:

```
long someBigNumber = 1013401346173L; 
```

Enter fullscreen mode Exit fullscreen mode

为了演示一个 64 位值可以有多宽的范围，让我们看一下下面的代码片段:

```
Long.MAX_VALUE // Prints 9,223,372,036,854,775,807
Long.MIN_VALUE // Prints -9,223,372,036,854,775,808 
```

Enter fullscreen mode Exit fullscreen mode

也许`long`可以用来计算光在给定时间框架内的传播距离。一秒钟后，光大约传播 3 亿米。如果我们写一个程序来实时跟踪光的距离，int 类型将在大约 7 秒后结束，而 long 可以计算出大约 975 年。不相信我？看看[这个贯穿整个计算的要点](https://gist.github.com/jrg94/820d3f0f482dd19f0170964346381df0)。

#### 浮点原始类型

虽然我们经常使用 64 位浮点数类型的`double`，但是 Java 支持另一种称为`float`的浮点数类型。然而，像`int`一样，Java 默认使用`double`作为浮点值。无论如何，我们可以使用`float`关键字:
来表示一个 32 位浮点数

```
float someNumber = 11.4f; 
```

Enter fullscreen mode Exit fullscreen mode

为了了解 a `float`的范围，让我们再次使用我们的技巧:

```
Float.MAX_VALUE // Prints 3.4028235E38
Float.MIN_VALUE // Prints 1.4E-45 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所见，32 位确实降低了我们的范围和精度。如果我们想在使用一半空间的同时使用精度低于`double`的十进制值，那么`float`类型是一个选项。

#### 布尔原语类型

最后可以覆盖`boolean`型。要声明一个`boolean`，我们可以使用`boolean`关键字:

```
boolean isBool = true; 
```

Enter fullscreen mode Exit fullscreen mode

布尔有点独特，因为它们不像所有其他原始类型那样表示数值。事实上，我们的小`MAX_VALUE`和`MIN_VALUE`伎俩在这里行不通。相反，布尔表示`true`或`false`，我们可能会想起上一节逻辑课中的内容。

我们现在不会在这上面花太多时间，因为它将成为我们用 Java 做的几乎所有事情的一部分。也就是说，我们通常不会像这样显式地声明它们。相反，它们是驱动代码中逻辑的比较的结果。有关更多信息，请查看下面的布尔运算符部分。

## 想了解更多？

如前所述，这些信息可以在 Java 文章中的[原语类型和变量中找到。当然，现在这篇文章更容易阅读了。](https://therenegadecoder.com/code/primitive-types-and-variables-in-java/)

如果你喜欢这篇文章并且想要更多，那么[成为 Renegade Coder](https://www.patreon.com/TheRenegadeCoder) 的一员。会员资格是表达你的支持的一个很好的方式，它对确保像这样的内容对公众保持免费大有帮助。

无论如何，再次感谢你的支持。下次见！

Java 中的 8 种原始类型第一次出现在变节的程序员 T2 的文章中。