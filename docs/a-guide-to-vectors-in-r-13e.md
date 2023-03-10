# R 中的向量指南

> 原文：<https://dev.to/kissmygritts/a-guide-to-vectors-in-r-13e>

这个周末是我第四次帮助 R 训练营。训练营为期两天。第一天包括非常基础的 R，数据结构，将数据读入 R，然后是简单的探索性数据分析。我们通常花第一天的大部分时间来练习 R 向量、矩阵、列表等基本数据结构。在这篇文章中，我试图尽可能完整地编译一个关于 R 向量的指南。

让我们开始吧。

## 原子类

在讨论向量之前，我们需要回顾一下基本的数据类型。这些基本类型被称为原子类(或原子向量)。r 有 5 种基本的向量类型。这些可以被称为原子类或原子类型。这些是构成 R 中所有内容的基本构件。以下是 R 中的基本原子类:

*   `logical` : `TRUE`，`FALSE`
*   1L，45L，328L。*`L`代表`Long`，要求强制 R 使用整数*
*   `double`:或`numeric` 1.23，4.6
*   `complex` : `5i`
*   `character`:字符串，用单引号或双引号括起来

我们可以用函数`typeof()`检查对象的类型或原子类。

```
typeof(5.52)  #> [1] "double"  typeof(6L)  #> [1] "integer" 
```

## 向量

这是你在 r 中会遇到的最常见的数据结构。它们是一维的，由 R 控制台打印时显示为单行数据。

为了创建一个矢量，你将需要使用函数`c()`(稍后将再次介绍函数)。这将把原子类组合成一个向量。e 向量中的每一项都被称为一个元素。`typeof()`可用于检查每个向量中的数据类型。

```
vector_1  <-  c(1,  2,  3)  vector_2  <-  c('a',  'b',  'c')  # check types  typeof(vector_1)  #> [1] double  typeof(vector_2)  #> [1] character 
```

所有向量只能包含一组单个原子类(字符，整数，...).当试图创建一个混合了原子类的 vector 时，r 会将数据强制转换成一个通用类型(很可能是一个字符)。尝试运行下面的一些代码，看看当试图创建混合原子类的向量时会发生什么。

```
vector_3  <-  c('apple',  3,  'orange',  4)  #> [1] "apple" "3" "orange" "4"  vector_4  <-  c('apple',  TRUE,  'orange',  FALSE)  #> [1] "apple" "TRUE" "orange" "FALSE"  vector_5  <-  c(1.43,  1L,  4L,  6.25)  #> [1] 1.43 1 4 6.25  typeof(vector_5)  #> "double" 
```

`vector_3`和`vector_4`是字符向量。`3`、`4`、`TRUE`和`FALSE`的值被强制或转换为字符。r 选择 character 是因为可以把数字转换成字符，但不能把字符转换成数字。

在`vector_5`中，`1L`和`4L`被强制从整数变为双精度(浮点数)。这个例子稍微复杂一点，因为可以将 double 转换成 integer，但是会丢失数据。更安全的操作是将整数转换为双精度数。

理解向量的这一特性非常重要。虽然这看起来像是一个约束，但它是一个非常有用的属性。因为我们知道一个向量包含一组相同类型的数据，所以我们可以安全地对该向量执行数据操作。

### 矢量属性

向量有两个主要属性:

*   长度:向量中有多少个元素- `length()`
*   type:向量中数据的类型- `typeof()`

```
# return length of vector_3  length(vector_3)  #> [1] 4  # return the type of vectors  typeof(vector_3)  #> [1] "character"  typeof(vector_5)  #> [1] "double"  typeof(c(1L,  2L,  3L))  #> [1] "integer" 
```

`typeof()`函数返回单个结果，因为 vector 中的每个元素都必须是同一类型。

### 矢量化运算

R 中的很多操作都是*矢量化*。这意味着矢量化运算将对向量的每个元素执行相同的操作。这是在 r 中工作的一个非常方便的方面。

一个简单的例子是给`vector_1`的每个值加 2。

```
vector_1  +  2  #> [1] 3 4 5 
```

这适用于任何数学运算(`-`、`*`、`^`等)。

由于矢量化运算，r 将正确地对两个向量执行数学运算。

```
vector_1  *  c(8,  9,  10)  #> [1] 8 18 30  vector_1  +  c(10,  20,  30)  #> [1] 11 22 33 
```

如果没有矢量化运算，我们将不得不手动遍历每个向量，通过索引获取适当的元素，然后执行适当的数学运算。这往往是一个非常方便的属性。

R 的主要用途是处理数据。数据很少只是一个东西，而大多是观察的行和列。当处理数据时，我们希望操作默认应用于数据(向量)的每个元素，这样我们可以避免其他通用编程语言中所需的循环。

需要注意的矢量化操作的一个警告叫做*回收*。r 天生喜欢对相同长度的向量进行运算。如果遇到两个不同长度的向量，它将循环使用较小的向量，直到长度相同。

```
# adding a vector of length 3 to a vector of length 2  vector_1  +  c(10,  20)  #> longer object length is not a multiple of shorter object length  #> [1] 11 22 13 
```

r 仍然返回一个结果，但可能不是您期望的结果。如果向量的长度不同，r 也会警告你。这个警告可能有点隐晦；`longer object length is not a multiple of shorter object length`这实质上意味着向量的长度不同。但是，如果向量的长度是彼此的倍数，那么 R 不会抛出这个警告。

```
c(1,  2,  3,  4)  +  c(10,  20)  #> 11 22 13 24  c(1,  2,  3,  4)  +  c(10,  20,  30)  #> longer object length is not a multiple of shorter object length  #> [1] 11 22 33 14 
```

### 索引

索引是一种选择向量中数据子集的操作。可以执行该操作来返回向量的单个或多个元素。这种操作有时被称为子集化，因为我们要获取一个更大数据集的子集。

索引用于指定一个元素在一组数据中的位置。R 中的所有数据结构都有一个索引，所以这个概念也将适用于后来的 R 对象。对于那些来自其他语言的人来说，R 索引从 1 开始。

```
vector_6  <-  c('a',  'b',  'c',  'd')  #> [1] "a" "b" "c" "d" 
```

在上面的向量中，每个元素都属于一个特定的位置。所以 a 的索引是 1，b 的索引是 2，c 的索引是 3，d 的索引是 4。

*注意:当一个矢量被打印到控制台时，在窗口的左侧会有一个带数字的方括号。这是控制台中最左侧值的索引。*

r 使用方括号(`[ ]`)来根据元素的索引选择元素。它看起来是这样的:`vector[x]`，其中，vector 是我们要子集化的向量的名称，x 是我们要的元素的索引。返回`vector_6`第三个元素的命令是这样的:`vector_6[3]`。试试看！

```
vector_6[3]  #> [1] "c" 
```

我们可以通过使用索引向量来选择多个元素。这听起来可能令人困惑，让我们来看一个例子。如果我们想要选择`vector_6`的第一个和第三个元素，我们需要创建一个索引为 1 和 3 的第二个向量，就像这样:`c(1, 3)`。如果我们把`c(1, 3)`放在方括号里会怎么样？

```
vector_6[c(1,  3)]  #> [1] "a" "c" 
```

返回第一个和第三个元素！

### 重新分配矢量中的元素

我们现在可以使用索引的概念给向量的每个元素赋予新的值。这是通过指定一个元素的索引(如上)，然后使用赋值操作符(`<-`)，并提供一个新值来完成的。

```
# what is in the vector  vector_6  #> [1] "a" "b" "c" "d"  # reassign  vector_6[1]  <-  'z'  #> [1] "z" "b" "c" "d"  # multiple values at once  vector_6[2:3]  <-  c('y',  'x')  #> [1] "z" "y" "x" "d" 
```

### 给矢量添加新值

可以使用`c()`功能将新值分配给矢量，就像矢量创建一样。也可以使用`c()`函数
来组合两个不同的向量

```
vector_6  <-  c(vector_6,  'a')  #> [1] "z" "y" "x" "d" "a"  vector_6  <-  c(vector_6,  c('b',  'c'))  #> [1] "z" "y" "x" "d" "a" "b" "c" 
```

*信息:这个操作并没有真正把值添加到`vector_6`向量中。相反，它将把`vector_6`的值重新分配给我们提供的向量。这只是一个有趣的事实。*

### 命名向量

向量中的每个元素都可以有一个唯一的名称。当我们想用一个向量的名字以外的东西来指代它的元素时，这是一个很方便的技巧。在下面的例子中，我们将创建一个十六进制颜色代码矢量。然后给每个十六进制代码分配颜色的名称。

```
color_vector  <-  c('#0000FF',  '#008000',  '#800080')  names(color_vector)  <-  c('blue',  'green',  'purple')  color_vector  #>      blue     green   purple  #> "#0000FF" "#008000" "800080"  # inspect the structure of color_vector  str(color_vector)  #> Named chr [1:3] "#0000FF" "#008000" "#800080"  #> - attr(*, "names")= chr [1:3] "blue" "green" "purple"  # the names of a vector can be accessed with the function names()  names(color_vector)  #> [1] "blue" "green" "purple" 
```

使用`names()`函数将名称分配给一个矢量，然后将矢量`c('blue', 'green', 'purple')`分配给该矢量。通过打印`color_vector`你可以看到，每个十六进制代码现在都有一个相关的颜色名称。

`str()`函数的结果显示了向量的加法属性。有一个属性，`attr`，叫做人名。这是与向量相关联的附加元数据。

### 逻辑子集化

我们可以通过使用任何逻辑运算符来识别向量中符合指定条件的值，从而对向量进行子集化。下面是 R 中可用的逻辑运算符(或布尔运算符)的列表:

*   `>`:大于
*   `<`:小于
*   `>=`:大于或等于
*   `<=`:小于或等于
*   `==`:等于
*   `!=`:不等于
*   在可能性的矢量中

我们可以使用这些运算符来检查值是否满足特定条件。布尔运算符返回的总是逻辑值`TRUE`或`FALSE`。

```
10  >  1  #> [1] TRUE  1  >  10  #> [1] FALSE  green  <-  'green'  'green'  ==  green  #> [1] TRUE  'green'  !=  green  #> [1] FALSE 
```

由于矢量化，r 允许我们根据布尔条件检查整个矢量！该操作的结果是每个索引的逻辑值(`TRUE`或`FALSE`)的向量。`TRUE`为符合条件的指标，不符合条件的为假。在下面的代码块中，我将使用`:`，这是生成一个递增 1 的数字序列的快捷方式。

```
# create a sequence of numbers from 101 to 106  x  <-  101:106  # apply boolean logic  x  >  103  #> [1] FALSE FALSE FALSE  TRUE  TRUE  TRUE  x  >=  103  #> [1] FALSE FALSE TRUE  TRUE  TRUE  TRUE  x  ==  103  #> FALSE FALSE TRUE FALSE FALSE FALSE  x  !=  103  #> [1] TRUE TRUE FALSE TRUE TRUE TRUE 
```

上面所有的例子都返回一个逻辑向量，它等于我们正在检查的向量的长度(这里`x`的长度是 6)。我们可以使用这个逻辑向量作为原始向量`x`的子集，只返回我们用布尔运算符指定的值。

请记住，在关于索引的章节中，我们使用`[ ]`来返回基于索引的元素。我们将在这里再次使用方括号来返回一个向量的子集，它与我们的逻辑标准相匹配。

```
# 1\. create a logical vector  logical_vector  <-  x  >  103  logical_vector  #> [1] FALSE FALSE FALSE  TRUE  TRUE  TRUE  # 2\. identify which indices are true  indices  <-  which(logical_vector)  indices  #> [1] 4 5 6  # 3\. now subset x using the indices variable  x[indices]  #> [1] 104 105 106 
```

希望你们都能跟上。如果没有，也不用担心。从头到尾再看一遍，想想每一行代码都发生了什么。大声读出每一行，想想每一行的结果会是什么。现在让我们看一下每一行。

1.  在第一步中，我们询问`x`的什么值大于(`>` ) 103。r 检查`x`中的每个元素，返回一个`TRUE` / `FALSE`值的逻辑向量，赋给变量`logical_vector`，看起来像这个`FALSE FALSE FALSE TRUE TRUE TRUE`。
2.  `which()`是一个函数，返回逻辑向量(`logical_vector`)的索引，这些索引是`TRUE`。在这种情况下，它们是索引 4、5 和 6。这被分配给变量`indices`。
3.  最后，我们通过变量`indices`中指定的索引对原始向量`x`进行子集化，这些索引是索引 4、5 和 6。这将返回向量中大于 103 的值。

我们不需要包括所有这些中间步骤来获得相同的答案。关于 R 的一个伟大的事情是有许多不同的方法可以得到相同的答案，没有一种方法是正确的。可能有更好的方法，但不总是正确的方法。下面的代码用一行代码返回相同的答案。

```
x[x  >  103]  #> [1] 104 105 105 
```

这是可能的，因为带有方括号的子集适用于长度等于原始向量的逻辑向量。所以我们可以使用上面的`logical_vector`变量到子集`x`。而`logical_vector`变量包含我们初始布尔运算`x > 103`的结果。因此，上面的代码块是获得相同精确结果的更短、更快的方法。

在下面的代码块中，你可以看到用一个逻辑向量来划分一个向量的子集是可能的。因此，不需要调用`which()`函数。

```
x[c(FALSE,  FALSE,  FALSE,  TRUE,  TRUE,  TRUE)]  #> [1] 104 105 106 
```

## 矢量汇总

对于这样一个看似简单的物体来说，这已经是很多细节了。但是现在你已经彻底了解了什么是向量以及如何使用向量。理解 R 中的其他数据结构将会更容易，因为向量是 R 中几乎所有其他数据结构的构建块。上述所有原则将再次重复，并且在下面关于矩阵、列表和数据帧的部分中再次重复。

我很可能错过了本指南中矢量的很多细节。如果你能想到任何，请留下评论，这样我就可以把它添加到本指南中。

我在考虑再写一篇关于矢量的有趣应用或技巧的文章。如果你能想到任何，请留下评论。

感谢阅读！

## 备忘单

```
# create a vector  c(2,  4,  6)  2:6  seq(2,  6,  by  =  2)  rep(2:3,  times  =  3)  rep(2:3,  each  =  3)  # subset vectors  x  <-  11:15  #> [1] 11 12 13 14 15  ## by element  x[4]  #> [1] 14  x[-4]  #> [1] 11 12 13 15  x[1:2]  #> [1] 11 12  x[c(1,  5)]  #> [1] 11 15  ## by value  x[x  ==  14]  #> [1] 14  x[x  >  13]  #> [1] 14 15 
```