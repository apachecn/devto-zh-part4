# 什么是模算符？包含实际使用案例的简短指南

> 原文：<https://dev.to/mjclemente/what-is-the-modulus-operator-a-short-guide-with-practical-use-cases-5elj>

加法、减法、乘法和除法。这是我小时候受教育时被教的四种数学运算，它们的运算符，`+`，`-`，`*`，`/`都很熟悉。我没有学过`%`、 ***模数运算符*** ，我最近发现它本身就非常有用和有趣。

模数运算符，在大多数编程语言中被写成`%`或`mod`，执行所谓的[模数运算](https://en.wikipedia.org/wiki/Modulo_operation)。可以理解，您的下一个反应可能是，“这并不能澄清任何事情”，所以让我们仔细看看:

*   [工作原理](#how-it-works)
    *   [用公式表示的模运算](#the-modulo-operation-expressed-as-a-formula)
*   [模运算的用例](#use-cases-for-the-modulo-operation)
    *   [偶数/奇数和交替](#even--odd-and-alternating)
    *   [将数量限制在范围内](#restrict-number-to-range)
    *   [通过有限选项旋转(圆形阵列)](#rotating-through-limited-options-circular-array)
    *   [每隔 n 次操作循环一次](#every-nth-operations-in-a-loop)
    *   [转换测量单位](#converting-units-of-measure)
    *   [杂集](#miscellany)
*   [一些有用的阅读](#some-helpful-reading)

## 工作原理

模数运算符——或者更准确地说，模运算——是一种确定除法运算的余数的方法。模运算不是返回除法的结果，而是返回整数余数。

一些例子可能有助于说明这一点，因为当你第一次遇到它时并不一定是直观的:

```
5 % 1 = 0
// 5 divided by 1 equals 5, with a remainder of 0

5 % 2 = 1
// 5 divided by 2 equals 2, with a remainder of 1

5 % 3 = 2
// 5 divided by 3 equals 1, with a remainder of 2

5 % 4 = 1
// 5 divided by 4 equals 1, with a remainder of 1

5 % 5 = 0
// 5 divided by 5 equals 1, with a remainder of 0 
```

在你学习分数和小数之前，回想一下你早期的数学课可能会有所帮助。对于整数，数学的处理方法有所不同——如果数字不是偶数的倍数，则除以后总会得到余数。这个余数就是模运算返回的结果。

如果这看起来奇怪、无聊或者不是特别有用，请耐心听我说一会儿——或者直接跳到用例。

### 用公式表示的模运算

作为最后一种解释方式，对于那些更倾向于数学的人来说，这里有一个描述模运算的公式:

```
a - (n * floor(a/n)) 
```

通过替换值，我们可以看到模运算在实践中是如何工作的:

```
100 % 7 = 2
// a = 100, n = 7
100 - (7 * floor(100/7)) = 2 
```

如果你觉得这个公式没有帮助，不要担心——一开始我也没有。有些人发现这种抽象表示有助于加深或澄清他们对操作的理解，但你不需要知道它。

这里的最后一个注意事项——如果您想知道模运算如何处理负数或小数，这超出了本文的范围。出于我们的目的，我们将只处理正整数。 <sup id="fnref1">[1](#fn1)</sup>

好了，数学到此为止。虽然返回余数是模运算的功能，但这不是它唯一的用途；事实上，我们会看到它更方便——但这是一个必要的起点。

## 用于模运算的用例

当我第一次遇到它的时候，模数运算符看起来只不过是一点数学琐事。当我开始学习它的实用性时，我发现它有趣多了。我在这里讨论几个应用。

### 偶数/奇数相间

模数运算符的一个最基本的用例是确定一个数是偶数还是奇数。 <sup id="fnref2">[2](#fn2)</sup> 这是可能的，因为`x % 2`总是返回 0 或 1。偶数因为能被 2 整除，所以总是返回 0，而奇数总是返回 1 的余数。我的意思是:

```
for( i=0; i <= 10; i++ ) {
  writeOutput( i%2 );
}
// 01010101010 
```

那么，用例是什么？这种技术通常用于在一个循环中改变值。我第一次使用 modulus 操作符是为了手动对表格行进行斑马条纹处理，行的背景颜色基于它是偶数还是奇数。同样，我使用`%`将 web 表单的结果分发给两个接收者，每隔一个接收者；在伪代码:

```
if( incrementalId % 2 == 1 )
  // send to one
else
  // send to the other 
```

当你有一组或一串记录、小工具、线索等时，这是一个方便的方法。，您希望交替处理。

### 限制数量范围

当您使用模数运算符进行奇偶交替时，您实际上利用了它的一个更有用的属性，尽管您可能没有意识到这一点。下面是属性:**`x % n`的取值范围在 0 和`n - 1`** 之间，也就是说*的取模运算不会返回大于除数*的值。 <sup id="fnref3">[3](#fn3)</sup>

同样，例子可能有助于澄清这个想法；在这里的每个实例中，除数是 5，所以结果的范围是 0 - 4。

```
1 % 5 = 1
// 1 cannot be divided by 5, so the remainder is 1

4 % 5 = 4
// 4 cannot be divided by 5, so the remainder is 4

7 % 5 = 2
// 7 divided by 5 equals 1, with a remainder of 2

25 % 5 = 0
// 25 divided by 5 equals 5, with a remainder of 0

218 % 5 = 3
// 218 divided by 5 equals 43, with a remainder of 3 
```

如你所见，不管初始数是多少，模数(除数)限制了结果的范围。

就其本身而言，这个属性似乎没有什么用处，但是当在一个较大的集合中应用时，它可以用来创建一个循环重复的模式。下面是一个以 3 为模数递增数字的例子:

```
0 % 3 = 0
1 % 3 = 1
2 % 3 = 2
3 % 3 = 0 // cycle back to 0
4 % 3 = 1
5 % 3 = 2
6 % 3 = 0 // cycle back to 0 
```

注意模运算的结果是如何不断重复 0-1-2 的；值会换行。描述这些结果的一种方式是用圆形阵列来表示[，就像钟面上的数字。让我们仔细看看这个属性的一个实际应用。](https://www.quora.com/What-is-a-circular-array-and-how-does-it-work)

#### 旋转通过有限的选项(圆形阵列)

在选项有限的情况下-工作日、原色、公司项目、客户等。-我们可以使用模运算在重复循环中遍历它们。也就是说，我们可以将一系列选项视为一个循环往复的圆圈。

这里有一个有点做作的例子来说明这一点:

```
// array of options that we want to cycle through
weekdays = ['Mon', 'Tue', 'Wed', 'Thu', 'Fri'];

// option count provides modulus (divisor)
dayCount = weekdays.len();

employeeCount = 14;

// loop over employees while rotating through days
for( i=0; i < employeeCount; i++ ) {

  // employee number mod option count
  dayIndex = i % dayCount;
  // adjust because CFML array indexed from 1
  dayIndex++;
  // use result to cycle through weekday array positions
  weekday = weekdays[dayIndex];

  writeOutput( "Scheduling employee on #weekday#. " );
} 
```

当我们遍历雇员列表时，我们将每个人分配到工作日。一旦我们安排了五个雇员，我们就到达星期五。由于没有更多选项，assignment 将返回到星期一，并继续循环，直到安排好所有员工。这是可能的，因为如前一节所示，模数 5(工作日的数量)返回 0-4 的循环数组，我们可以将它映射到工作日数组中的选项。看到并理解这一点对我来说是一个重大的启示。

### 每第 n 次运算循环一次

模运算的另一个应用是确定循环内的间隔；也就是说，计算诸如“每四次”、“每十次”等的出现次数。

在这种情况下，原则是如果`n`是`x`的偶数倍，那么`x % n = 0`。因此，`x % 4`每四次将返回 0；`x % 10`每十次会返回 0，以此类推。它的一个实际用途是在长时间或长时间运行的循环中提供反馈:

```
// given a list of widgets, files, people, etc.
longList = 10000; 
feedbackInterval = 100; // to be used as the modulus

// loop over the list to process each item
for( i=1; i <= longList; i++ ) {

  // perform some operation

  // mod operation gives feedback once every hundred loops
  if( i % feedbackInterval == 0 ) {
    percentCompleted = ( i / longList ) * 100;
    writeOutput( "#percentCompleted# percent complete. " );
  }

} 
```

您可以使用类似的原理来触发基于时间或参与度的客户/用户交互。例如，每 90 天发送一次促销，或者每 50 次登录提供一次反馈调查。

请记住，时间实际上也是一个漫长的循环。每小时有 60 分钟，周而复始。如果您想安排一个任务每小时运行四次，您可以使用模运算来实现——只需在`minutes % 4 == 0`时运行它。

### 转换计量单位

转换度量单位是模运算实际应用的常见例子。一般的用例是当你想把一个较小的单位，比如分或英寸/厘米，转换成一个较大的单位，比如小时或英里/公里；在这些情况下，小数或分数并不总是有用的。

例如，如果我们想知道 349 分钟的小时数，将结果表示为 *5 小时 49 分钟*可能比 *5.8167 小时*更有帮助。这里有一个快速和肮脏的采取什么类型的转换函数可能看起来像:

```
function minutesToHours( m ) {
  hours = floor( m/60 );
  minutes = m%60;

  return "#hours# hours #minutes# minutes";
}
writeOutput( minutesToHours( 349 ) ); 
```

标准除法(向下舍入到最接近的整数)确定小时数，而模运算用于跟踪剩余的分钟数。无论你处理的是时间、距离、压力、能量还是数据存储，你都可以使用这种通用的单位转换方法。

### 杂集

你可能认为我已经穷尽了所有可能使用模数运算符的情况，但是你错了。以下是我在 Stack Overflow、Quora 和互联网上找到的一些例子:

*   [反转一个数字](https://stackoverflow.com/a/42234911/5361034)
*   [将线性数据转换成矩阵](https://stackoverflow.com/a/37449343/5361034)
*   [确定数组是否是彼此的旋转版本](https://stackoverflow.com/a/51461187/5361034)
*   [分页](https://www.quora.com/What-would-be-a-practical-use-of-modulus-operator/answer/Graham-Cox-18)
*   [闰年计算](https://www.quora.com/In-which-cases-is-the-Modulo-operation-used-in-programming/answer/Mesam-Zahid)

此外，一旦你熟悉了模运算，解决这里讨论的 [FizzBuzz 问题](https://blog.codinghorror.com/why-cant-programmers-program/)应该不会有任何问题。

## 一些有益的阅读

在我研究和写这篇文章的时候，我从这些帖子中学到了很多:

*   [Java 中的模运算符](https://www.baeldung.com/modulo-java)
*   [如何在 PHP 中使用模运算符](https://code.tutsplus.com/tutorials/how-to-use-the-modulo-operator-in-php--cms-32393)
*   [有趣的模运算](https://betterexplained.com/articles/fun-with-modular-arithmetic/)
*   [识别何时使用模数运算符](https://stackoverflow.com/questions/2609315/recognizing-when-to-use-the-modulus-operator)(堆栈溢出)
*   [编程中在哪些情况下使用模(%)运算？](https://www.quora.com/In-which-cases-is-the-Modulo-operation-used-in-programming) (Quora)

现在你知道了；去用吧！如果你有其他的用途或例子想分享，我很乐意听听。

* * *

* * *

1.  编程语言在支持和处理*浮点*(十进制)模运算以及负数的方法上有所不同。处理这些问题超出了本文的范围，这也是我所关心的，也是我读过或遇到的最实际的用例。 [↩](#fnref1)

2.  以免我在评论中被指出，我应该注意到*位运算符*提供了另一种更有效的方法来确定一个数字是偶数还是奇数，但这是另一天的另一个主题。 [↩](#fnref2)

3.  我应该注意到模运算中的除数也叫做模数。 [↩](#fnref3)