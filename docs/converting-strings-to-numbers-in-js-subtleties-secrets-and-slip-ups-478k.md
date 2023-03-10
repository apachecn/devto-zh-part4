# 在 JS 中将字符串转换成数字:微妙之处、秘密和失误

> 原文：<https://dev.to/emnudge/converting-strings-to-numbers-in-js-subtleties-secrets-and-slip-ups-478k>

在每个程序员的一生中，总有一天他们必须从字符串中提取一个数字。
* *嗅嗅* *他们长得真快...

在 Javascript 中，我们不进行类型转换，而是(通常)使用函数。我们将讨论几种不同的方法，并对它们进行更深入的解释。

## `Number()` -转换使用的数类

如果您不确定使用哪一个，这可能是一个不错的默认选择。传递任何包含非数值的字符串都将返回一个`NaN`。

```
console.log(Number("4"));    // 4
console.log(Number("4.01")); // 4.01
console.log(Number("42 g")); // NaN 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这将返回与`new Number()`不同的结果。

```
const myNum1 = Number("4");
console.log(myNum1);       // 4
console.log(myNum1 === 4); // true
console.log(myNum1 == 4);  // true

const myNum2 = new Number("4");
console.log(myNum2);       // Number {4}
console.log(myNum2 === 4); // false
console.log(myNum2 == 4);  // true 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 [MDN 页面](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)上找到更多信息。

## 一元`+`运算符——转换使用`+`

传递任何包含非数值的字符串都将返回一个`NaN`。

```
console.log(+"4");    // 4
console.log(+"4.01"); // 4.01
console.log(+"42 g"); // NaN 
```

Enter fullscreen mode Exit fullscreen mode

这是与二元运算符`+`不同的*而不是*。同样的符号，不同的语境。这里有一个例子。

```
const myNum = "4";
console.log(+myNum);    // 4
console.log(0 + myNum); // "04" 
```

Enter fullscreen mode Exit fullscreen mode

当使用二元运算符`+`将一个数字与一个字符串相加时，它将被强制转换为一个字符串。不，在第二个例子中，空格和顺序无关紧要。如果没有第一个参数，`+`会变成一元操作符，并有不同的行为。这显然是一些非常混乱和微妙的行为。

## `parseInt()` -转换为 int

`parseInt()`是`window`对象和`Number`对象的一部分。这意味着它既可以用作`parseInt()`也可以用作`Number.parseInt()`。

向它传递一个数字的字符串表示，它将返回一个整数版本。

`parseInt()`还可以传递第二个参数——基数。这个数字描述了在哪个基数上处理这个数字。例如基座 16、基座 8、基座 10 等。

```
console.log(parseInt('0xF', 16)); // 15
console.log(parseInt('1111', 2)); // 15
console.log(parseInt(10, 10));    // 15 
```

Enter fullscreen mode Exit fullscreen mode

在大多数实例中，它的默认值是基数 10，但是强烈建议无论如何都要把它加进去，因为省略它会导致[事故](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt#Octal_interpretations_with_no_radix)。 [Google Apps 脚本](https://en.wikipedia.org/wiki/Google_Apps_Script)，一种类似于 ES6 之前 JS 的语言，用于 G Suite 平台上的开发工作，如果有前导 0，默认为八进制。这主要是针对 ECMAscript 5 之前的版本，但是仍然鼓励包含基数。

使用`parseInt()`的另一个原因是它如何解析包含非数字字符的字符串。它将解析，直到遇到一个非数字字符，然后停止。如果*以非数字字符开始*，它将返回`NaN`。

```
console.log(parseInt("4 aliens", 10));   // 4
console.log(parseInt("831million", 10)); // 831
console.log(parseInt("327 * 31", 10));   // 327
console.log(parseInt(".391", 10));       // NaN 
```

Enter fullscreen mode Exit fullscreen mode

注意:因此，与所有其他选项不同，它不能将`"3.230e+2"`解析为`323`。它将返回`3`。

这对解析 CSS 单元值非常有用。这些通常包括一个数字和一些单位类型，如“150 像素”、“20 度”、“15 雷姆”等。

```
// getting the css width of a tag with the class of "box"
const boxWidth = document.querySelector('.box').style.width;
console.log(boxWidth);               // 24px
console.log(parseInt(boxWidth, 10)); // 24
console.log(Number(boxWidth));       // NaN 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 [MDN 页面](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/parseInt)上找到更多信息

## `parseFloat()` -带小数的换算

`parseFloat()`是`window`对象和`Number`对象的一部分。这意味着它既可以用作`parseFloat()`也可以用作`Number.parseFloat()`。

如果字符串不包含小数，`parseFloat()`不会加一。如果有礼物的话，它只是保存它们。

另外，`parseFloat()`不提供基数选项。那么`parseFloat()`和数字有什么区别呢？当字符串中出现非数字字符(并且不是第一个字符)时，`parseFloat()`也不提供`NaN`。

```
console.log(parseFloat("4.5 pizzas left", 10)); // 4.5
console.log(parseFloat("3.230e+2", 10));        // 323
console.log(parseFloat("830.2 * 31.4", 10));    // 327.01
console.log(parseFloat(".391", 10));            // 0.391
console.log(parseFloat("one3.1", 10));          // NaN 
```

Enter fullscreen mode Exit fullscreen mode

## `Math`方法——转换偶

`Math`对象包含许多有用的数学相关函数。这些函数还有一个副作用，就是转换非数字输入，就像使用`Number()`或一元`+`操作符一样。

我将在例子中使用`Math.pow(x, 1)`,但是它在所有数学方法中都是一样的。

```
console.log(Math.pow("32.5", 1)); // 32.5
console.log(Math.pow(".325", 1)); // 0.325
console.log(Math.pow("21 a", 1)); // NaN 
```

Enter fullscreen mode Exit fullscreen mode

这在任何情况下都不可取。当然，除非你的代码是为了迷惑读者。

## 一元`~`运算符——按位一元转换

一元运算符`~`是一个[位非运算符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators#(Bitwise_NOT))。我们将基数为 10 的数转换为基数为 2 的数，然后再转换回来。作为一个按位运算符，任何数字也可以通过截断或其他方式转换为整数(我不太清楚具体的方法)。

按位非运算符将翻转所有位。这就产生了一个用`-1 * (n + 1)`表示的数(假设 n 是整数)。重复相同的操作两次会得到原来的数字。

```
console.log(~4);      // -5
console.log(~~4);     // 4
console.log(~~4.311); // 4 
```

Enter fullscreen mode Exit fullscreen mode

虽然许多人引用了相对于`Math.floor()`的性能改进并使用了它，但是这个操作符也可以用来以类似于`parseInt()`的方式将字符串转换成整数。

```
console.log(~"4");      // -5
console.log(~~"4");     // 4
console.log(~~"4.311"); // 4 
```

Enter fullscreen mode Exit fullscreen mode

然而，这里的一个主要区别是，它永远不会返回 NaN。如果字符串为非数字或包含非数字字符，将返回`-1`。再次执行该操作将对`-1`执行按位非运算，导致`0`。

```
console.log(~"hat");  // -1
console.log(~~"hat"); // 0
console.log(~~"h4");  // 0
console.log(~~"4h");  // 0 
```

Enter fullscreen mode Exit fullscreen mode

## 二进制按位运算符——使用按位二进制进行转换

我们也可以使用其他的按位方法。虽然`~`是唯一的一元位运算子，但我们可以使用二元位运算子，并将无用的值传递给第二个操作数。

```
console.log("4"|0);   // 4
console.log("4"&-1);  // 4
console.log("4"<<0);  // 4
console.log("4">>0);  // 4
console.log("4">>>0); // 4 
```

Enter fullscreen mode Exit fullscreen mode

这样，除了把它解析成一个整数之外，我们什么也不做。理论上它应该比`~~`快，但那并不是我们将要看到的。

点击了解更多关于[位运算符的信息。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators)

# 性能测试

很多人反对性能测试。有时细节隐藏在引擎后面，我们的基准测试无法提供准确的结果。汤姆·戴尔在他的[博客](https://tomdale.net/2017/07/adventures-in-microbenchmarking/)上有一个很棒的例子。

不管怎样，考虑到这一点，它总是一件有趣的事情。我将使用 [Benchmark.js](https://benchmarkjs.com) 进行这些测试。如果其中一个选项只比另一个稍微高一点点(明智的百分比)，那么它在以后的测试中的分数可能会更低。

```
const num = "3249323242";
+num;            // operations: 6378907
~~num;           // operations: 6263920
num>>0;          // operations: 6256772
num<<0;          // operations: 6256384
num&-1;          // operations: 6239926
num>>>0;         // operations: 6125457
Number(num);     // operations: 6076579
num|0;           // operations: 5983553
parseFloat(num); // operations: 589328
parseInt(num);   // operations: 534935
Math.pow(num);   // operations: 485971 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，按位操作符比`parseInt()`快得多，但只比`Number()`快一点点。`Number()`对你的同事和未来的自己来说更容易理解，所以要友善。

# 什么时候用什么

使用`parseInt()`用于:

*   将字符串从一种碱基转换成另一种碱基？
*   从 CSS 单位字符串中获取一个数字？使用`parseInt`。

使用`parseFloat`用于:

*   从包含小数的 CSS 字符串中获取数字。

使用`Math`方法进行:

*   迷惑你的朋友。

将一元`+`运算符用于:

*   压缩/丑化 JS 文件作为`Number()`的替代。

将一元和二元按位运算符用于:

*   压缩/丑化一个 JS 文件来代替不需要基数和截断特性的`parseInt(x, 10)`。
*   具有上述用例的高度性能依赖库。

使用`Number`:

*   每隔一段时间。

## TL；速度三角形定位法(dead reckoning)

每种方法做的事情略有不同。当你不确定的时候使用`Number()`。如果你想知道细节，你必须读这篇文章。