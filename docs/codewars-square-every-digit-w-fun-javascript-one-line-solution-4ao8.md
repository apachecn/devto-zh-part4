# Codewars:“平方每一个数字”与有趣的 JavaScript 一行解决方案✨

> 原文：<https://dev.to/acupoftee/codewars-square-every-digit-w-fun-javascript-one-line-solution-4ao8>

这是我解释解决编码问题的方法的系列文章的一部分。这是为了帮助我更好地表达我的思维过程，并为开发人员启发新的解决问题的方法！

**问题陈述:**

> 在这个形中，你被要求平方一个数字的每一个数字。比如我们通过函数运行 9119，就会出来 811181，因为 92 是 81，12 是 1。注意:该函数接受一个整数并返回一个整数

**方法** :
对于这个问题，给我们一个整数(没有小数)。我们需要返回一个新数字，其中每个数字都是平方的。
例如，如果给我们数字 9119，我们将返回 811181，因为 9^2 = 81，而 1^2 = 1。我们需要确保之后返回一个整数。你会在下面看到原因。下面是一般的方法:

1.  获取数字列表。
2.  对于列表中的每个数字，将其平方。
3.  连接每个数字。
4.  返回带平方数字的新数字。

**方案一** :

```
/**
 * Returns a new number with squared digits
 * Time complexity: O(n), space complexity: O(n)
 * @param {Number} num an integer
 * @returns {Number} an integer with squared digits
 */
const squareDigits = num => {
    // Get an array of digits
    const digits = num.toString().split('')

    // Square each digit. Because JavaScript has 
    // automatic type coercion, it will quietly convert each string
    // into a number to perform multiplication
    const squaredDigits = digits.map(n => n * n)

    // Join each digit together, then use the + operator 
    // to convert the string into a number
    const squaredNumber = squaredDigits.join('')

    return +squaredNumber
} 
```

Enter fullscreen mode Exit fullscreen mode

**解释** :
这是一个很好的问题，应用 JavaScript 的 string 方法将数字拆分并保存到一个数组中。由于没有内置的方法可以做到这一点，我们必须将数字转换成字符串。然后，在返回新数字之前，我们可以使用数组迭代方法对每个数字求平方。这就是我们要做的。

我们使用`toString()`来创建数字的字符串表示。如果我们传递我们的函数`9119`，这将创建一个字符串表示，这将是`'9119'`。

按照我们的`toString()`方法，我们使用`.split('')`分割字符串。这意味着我们将数字分解成单个的数字，然后将它们保存到一个数组中。

```
const digits = num.toString().split('') 
```

Enter fullscreen mode Exit fullscreen mode

这将产生一个如下所示的数组:

```
['9', '1', '1', '9'] 
```

Enter fullscreen mode Exit fullscreen mode

对于每一个数字，我们需要平方它们。现在我们有了一个字符串数组，我们可以使用`map()`查看我们的原始数组，并将每个数字相乘。这节省了我们的空间，因为我们只更新我们的原始数组，而不是创建一个新的。

```
const squaredDigits = digits.map(n => n * n) 
```

Enter fullscreen mode Exit fullscreen mode

你可能想知道这怎么可能，因为我们已经习惯了数字相乘，对吗？JavaScript 用一种叫做“自动类型强制”的东西把我们联系了起来这仅仅意味着我们正在使用特定类型的操作，在这种情况下，JavaScript 将秘密地将数字转换成数值！

之后，我们的数组将看起来像这样:

```
[81, 1, 1, 81] 
```

Enter fullscreen mode Exit fullscreen mode

我们将使用`.join('')`将每个数字连接在一起。这将为我们提供新数字的*字符串*表示:

```
const squaredNumber = squaredDigits.join('') 
```

Enter fullscreen mode Exit fullscreen mode

问题语句希望返回一个数值，而不是一个字符串！我们可以在我们的`squaredNumber`常量前使用`+`前缀很容易地做到这一点。这将把它转换成一个数字:

```
return +squaredNumber 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案的时间复杂度是 O(n ),因为我们对数组中的每个数字求平方。空间复杂度是 O(n ),因为我们正在使用`.split('')`创建一个新的数组。

* * *

解决方案 2 是一个简洁的一行程序！这是一种应用数组的有趣方式。来自:

**方案二** :

```
/**
 * Returns a new number with squared digits
 * Time complexity: O(n), space complexity: O(n)
 * @param {Number} num an integer
 * @returns {Number} an integer with squared digits
 */
const squareDigits = num => {
    return +Array.from(num.toString(), n => n * n).join('')
} 
```

Enter fullscreen mode Exit fullscreen mode

这家伙到底在做什么？

1.  [Array.from()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 从我们的输入中创建新的[浅拷贝](https://www.cs.utexas.edu/~scottm/cs307/handouts/deepCopying.htm) `Array`。
2.  在这个方法中，我们将数字转换成一个`String`——JS 中的一个可迭代对象。这允许`Array.from()`创建一个字符串数字数组。
3.  然后，我们使用可选的映射参数，我们将平方我们的数字，
4.  之后，我们将连接每个数字，并使用`+`运算符将`String`结果转换为`Number`。

* * *

*Thanks for reading! As always questions, feedback, and ideas are always encouraged. Happy hacking!*