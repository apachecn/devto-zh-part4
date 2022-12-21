# 6 个 JavaScript WTFs 以及从中学习什么

> 原文：<https://dev.to/andreib123/6-javascript-wtfs-and-what-to-learn-from-them-406d>

嗨！我相信在你的 JavaScript 开发者之旅中，你至少见过一次*(每天)* **【未定义】不是函数**或者你问过自己为什么**NaN 的类型实际上是一个数字。**嗯，有时候 JavaScript 想对你进行压力测试。
[![Gordon Ramsay angry](img/e9b5f2619c7d062efa1a404f6088c4d2.png)](https://i.giphy.com/media/l41Yb2wVzu9vmumZi/giphy.gif)

在本文中，您将成为这种美丽的编程语言有趣的*(和黑暗的)*的一部分。开始吧！

* * *

## 1。最小>最大

```
Math.min() > Math.max() //true 
```

## 解释:

好的，首先让我们定义一些东西。

> *   **`Math`** is a built-in object, which has properties and methods of mathematical constants and functions. Is not a function object.
> *   The static function **`Math.max()`** returns the number of the maximum value passed to it, or [`NaN`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN) if any parameter is not a number and cannot be converted to a number.

完美，直到现在我们知道了 JavaScript 中的数学对象代表什么，以及**代表什么。max()** 静态函数确实如此。同样的，**。min()** 函数会做相反的操作。直到现在，我们的直觉可能会说 *Math.max()* 应该返回**数字。MAX_VALUE** 如果没有提供参数。

然而，这种假设是错误的。原因如下。假设您必须实现一个函数，从数组中找到**的最大值。那很简单！最简单的方法是遍历整个数组，比较元素，*存储最大值*。转折来了！你存储最大值的那个变量应该用一个非常非常小的值**初始化，这个值是最小的。

你现在可能认为，Javascript 中的最小值是 **Number。MIN_VALUE (5e-324)** 而你是对的。但是，JavaScript 在这种特殊情况下为你准备了一些东西，那就是**无限**。

> 全局 [`Infinity`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity) 属性是代表无穷大的数值。

最后是对*的完整描述。max()* 功能:

> Math.max()返回给定数字中的最大值。如果任何一个或多个参数不能转换成数字，则返回 [`NaN`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/NaN) 。如果没有提供参数，结果是 [`-Infinity`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Infinity) 。

```
Math.min() > Math.max() -> Infinity > -Infinity //true 
```

### 外卖:

*   什么是**数学**对象
*   **min()** 和 **max()** 函数的行为方式
*   JavaScript 中的 **Infinity** 对象

## 2。0.1 + 0.2 = ?

那太简单了。0.1 + 0.2 = 0.3 对吧？JavaScript 里没有！*(或 JAVA，或 C++，或 C#，或..*你说对了)

```
0.1 + 0.2 === 0.3 //false 
```

[![Intense calculations](img/7aa4a3fdfd1253c75645c63a9a1bfa7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QY8HuCgU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media0.giphy.com/media/3o7btPCcdNniyf0ArS/giphy.gif)

## 解释:

这怎么可能？在你重新考虑你学过的所有基础数学之前，让我介绍一下[浮点数学](http://0.30000000000000004.com/)。

> 计算机本身只能存储整数，所以它们需要某种方式来表示十进制数。这种表现带有某种程度的**不准确性**。

这个问题很复杂，需要投入大量的时间。但是，我会针对这种特殊情况尽量简化它。

在以**为基数的 10 进制**的情况下，唯一可以清晰表达的分数是那些以质因数为基数的*(，，1/5 等。)*。相比之下，1/3 有**循环小数** *(0，33333..)*。现在，如果我们获取此信息并将其应用于 **2 基本系统**，则干净分数为*和 18*，而*15 和 110*具有循环小数。这导致了这个例子中的一些剩余。

```
0.1 + 0.2 === 0.30000000000000004 //true 
```

> 当你计算这些循环小数时，你最终会得到**余数**，当你将计算机的基数 2(二进制)数转换成更容易被人类理解的基数 10 数时，余数会继续存在。

### 外卖:

*   **浮点数学**概述
*   这个概念适用于*大多数编程语言*

## 3。香蕉

好，做完那道数学难题后，让我们做点有趣的事吧！

```
"b" + "a" + +"a" + "a" -> baNaNa 
```

## 解释:

与其他两个 wtf 不同的是，这个 wtf 稍微简单一点。那是因为你已经解决了 75%的问题。现在我们只有一个微小的方面需要澄清:什么**++“a”**将返回。

JavaScript 语法是有效的，因为第二个 **+** 不是加法运算符，而是一个**一元运算符**。

> **[一元+](https://www.ecma-international.org/ecma-262/#sec-unary-plus-operator)** 运算符将其操作数转换为数字类型。任何不支持的值将被评估为`NaN`。

毫不费力！所以我们的表达式会 l 如下，因为**“a”**不能转换成数字。

```
"b" + "a" + NaN + "a" -> baNaNa 
```

为了得出结论，我们应该再确认一块拼图。什么**字符串+字符串+ NaN +字符串**会返回？加法运算符将如何表现？

> **[加法运算符](https://www.ecma-international.org/ecma-262/#sec-addition-operator-plus)** 执行字符串连接或数字加法。

因此，有两种类型的加法可以发生，**字符串串联**或**数字加法**，以这种特定的顺序。算法的工作方式如下:

使用 **[ToPrimitive()](https://www.ecma-international.org/ecma-262/#sec-toprimitive)** 函数将操作数转换为基元。

如果其中一个操作数是一个**字符串**，那么将另一个转换成一个字符串并执行*字符串连接*。否则，将二者转换为**数字**并执行*数字加法*。

```
"b" + "a"-> "ba"
"ba" + NaN -> "ba" + "NaN" -> "baNaN"
"baNaN" + "a" -> "baNaNa" 
```

### 外卖:

*   什么是**一元运算符**
*   加法运算符*算法*
*   **top primitive()**函数及其一个用例

## 4。声明前的初始化？

以这段代码为例:

```
message = "don't forget to hit the heart button if you liked it.";
console.log(promoteArticle("Stranger"));
function promoteArticle(name) {
    return `${name}, ${message}`;
};
var message; 
```

控制台会提示什么？A **ReferenceError** 该消息未定义？又或许是那串**《陌生人，未定义》**。不，一定是**类型错误**，promoteArticle 不是函数。

对我们来说幸运的是，输出将正是我们想要的:*“陌生人，如果你链接了它，别忘了点击心脏按钮”*。但是为什么呢？这是由***的 JavaScript 行为造成的。***

 ***> 提升是 JavaScript 默认的`moving declarations to the top`行为。

*注意:这只适用于用 var 关键字定义的变量和声明的函数。*

利用这些信息，我们可以断言，在**编译** :
之后，我们的代码会是这样的

```
function promoteArticle(name) {
    return `${name}, ${message}`;
};
var message;
message = "don't forget to hit the heart button if you liked it.";
console.log(promoteArticle("Stranger")); 
```

让我们一步一步来。 **promoteArticle()函数**在顶部，因为函数声明是第一个元素移动到顶部的**，后面是 var 变量声明。**

此外，不会抛出错误，消息具有正确的值，因为在调用函数时，变量既被声明为又被初始化为**。**

为了确保我没有引起任何混淆，我将提到**声明函数和表达式函数**之间的区别。下面是包含这两种类型的示例。

```
function declaredPromoteArticle(name) {
    return `${name}, ${message}`;
};
var expressionPromoteArticle = function(name) {
    return `${name}, ${message}`;
} 
```

编译后:

```
function declaredPromoteArticle(name) {
    return `${name}, ${message}`;
};
var expressionPromoteArticle; // just the variable definition was hoisted
expressionPromoteArticle = function(name) {
    return `${name}, ${message}`;
} 
```

### 外卖:

*   什么是**吊装**
*   函数声明与函数表达式

## 5。NaN == 'number '的类型

[![God please no meme](img/7b659a4e44d7f33e4986e0bbb4db26af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n3A8VqZM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://tilldawngroup.com/wp-content/uploads/2015/04/giphy-1.gif) 
这个可能会因为词法方面的原因显得特别奇怪，**【不是数就是数】**，但是一秒钟就说得通了。首先，让我们检查一下定义:

> **全局** `NaN`属性是代表非数字的值。

NaN 的定义简单明了，但我们可以在**‘全局’**一词中找到窍门。与我们的第一直觉相反，NaN 并不是一个关键字(像 null、if、var 等。)，而是一个**全球性质的**。什么全局对象可能包含此属性？没错，你猜对了，就是这个**号的物体**。

```
typeof NaN == 'number' -> typeof Number.NaN == 'number' //true 
```

> [`Number.MIN_VALUE`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/MIN_VALUE)
> 可表示的最小正数——即最接近零的正数(实际上并不为零)。
> [`Number.NaN`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/NaN)
> 特殊的“非数字”值。

你可能会问，为什么我还提取了 MIN_VALUE 属性。这是因为它将更清楚为什么 JavaScript 编译器不知道 **MIN_VALUE** 和 **NaN** 属性之间的任何区别，因此两种类型都是**数字**。

### 外卖:

*   NaN 这不是一个关键字，而是一个属性
*   在这种情况下，操作符的[类型如何表现](https://www.ecma-international.org/ecma-262/#sec-typeof-operator)

## 6。Array.prototype.sort()

最后一个 WTF 的主题是 **sort()** 方法的行为，没有发送任何参数。

```
[32, 3, 6].sort() //[3, 32, 6] 
```

好吧，这和我们预想的不一样。为什么这些值是按特定的顺序排列的？让我们举**更多的例子**来冒险。

```
[32, 3, true, 6].sort() //[3, 32, 6, true]
[32, 3, true, 6, NaN].sort() //[3, 32, 6, NaN, true]
[32, 3, true, 6, NaN, undefined].sort() //[3, 32, 6, NaN, true, undefined] 
```

明白了吗？是的，默认算法**将每个值**转换成一个*字符串*，然后对它们进行相应的排序。

为了达到预期的结果，sort()方法将需要一个比较函数作为参数。该函数接收**两个参数**，并返回一个描述它们之间的*关系的数字*。

> 符号 a < b means comparefn(a, b) < 0; a = b means comparefn(a, b) = 0 (of either sign); and a > b 表示 comparefn(a，b) > 0。

下面是一个使用一组用户的例子。排序算法基于每个用户的年龄属性。

```
let users = [
    {
        name: "Andrei",
        age: 24
    },
    {
        name: "Denisa",
        age: 23
    }];
users.sort((first, second) => first.age - second.age);
//[ { name: 'Denisa', age: 23 }, { name: 'Andrei', age: 24 } ] 
```

### 外卖:

*   **Array.prototype.sort()** 默认行为
*   如何实现特定于**的**排序机制

## 加成:`NaN`不是`NaN`

惊喜，还有！

```
NaN === NaN //false 
```

这个指的是[严格相等比较](https://www.ecma-international.org/ecma-262/#sec-strict-equality-comparison)及其实现。

> 1.  If [type](https://www.ecma-international.org/ecma-262/#sec-ecmascript-data-types-and-values) (x) is different from [type](https://www.ecma-international.org/ecma-262/#sec-ecmascript-data-types-and-values) (y), return false.
> 2.  If [type](https://www.ecma-international.org/ecma-262/#sec-ecmascript-data-types-and-values) (x) is a number, then
>     1.  If x is `NaN`, return false.
>     2.  If y is `NaN`, return false. ...
> 3.  返回 SameValueNonNumber (x，y).

我们知道，NaN 类型是 number，所以如果条件匹配则*秒。之后，如果任何一个操作数是 NaN，**返回 false** 。*

### 外卖:

*   第一部分**严格相等比较**的实现
*   该算法的最后一部分使用了另一个名为 **SameValueNonNumber** 的算法

* * *

最后，我们完成了。你可能会觉得那些 wtf 是**幼稚** *(而且你会对其中的一些)*，但它们可能隐藏着**小 bug***(影响大)*，浪费你大量的时间和精力。

此外，当你的代码中有可疑之处时，习惯**搜索官方文档，学习*编译器如何“思考”*真的可以提高你的技能。**

[![Done meme](img/126eaf137f506aac336a18ebfdb84a2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hGmjXQJ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media0.giphy.com/media/8JW82ndaYfmNoYAekM/giphy.gif)

**注:** *这是我第一次尝试写技术文章。请在评论中给我任何反馈，还有你感兴趣的话题。编码快乐！****