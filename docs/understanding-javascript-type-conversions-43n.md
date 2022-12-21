# 理解 JavaScript 类型转换

> 原文：<https://dev.to/antonmelnyk/understanding-javascript-type-conversions-43n>

# 简介

JavaScript 最令人困惑的部分可能是它如何处理类型。由于 JavaScript 是一种具有丰富历史的灵活、宽容的语言，所以可以实现很多不可思议的东西。你可能已经看过[这样有趣的事情](https://github.com/aemkei/jsfuck):

```
(NaN+Object()["to"+String["name"]]["call"]())[11] // Produces letter "U" 😮 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子太奢侈了，但是一般来说，优秀的开发人员应该了解他们正在使用的编程语言的所有内幕。

让我们澄清一下关于 Javascript 如何以及何时转换类型的所有误解。

# JavaScript 中有哪些类型？

JavaScript 中的值是以下类型之一:

```
// We can use typeof function to get the type of the value

typeof undefined // "undefined"
typeof 42 // "number"
typeof "hello" // "string"
typeof true // "boolean"
typeof { name: 'Jhon' } // "object"
typeof alert // "function"
typeof Symbol("id") // "symbol"

typeof null // "object" 
```

Enter fullscreen mode Exit fullscreen mode

如果您已经使用过 JavaScript，这应该是不言自明的。

当然，`null`值不是一个对象。它的类型是`"null"`。然而由于历史原因，`typeof`函数为`null`值返回`"object"`。

因为 JavaScript 是一种弱类型语言，所以当发生这种情况时，它会尝试在类型之间进行隐式转换。但是，在 JavaScript 世界中使用隐式是一个危险的词！

# 什么是类型转换？

当操作数或函数参数没有预期的类型时...

```
3 > "3"  // Expects number, given string
3 + true // Expects number, given boolean
alert(3) // Expects string, given number
alert({ name: 'John' }) // Expects string, given object 
```

Enter fullscreen mode Exit fullscreen mode

Javascript 按照特定的规则将值转换为预期的类型。

让我们检查一下您在代码中最有可能遇到的每一种情况:

## 字符串类型转换

当给定值应该是字符串时，应用字符串类型转换。最基本的例子就是*报警*功能:

```
alert(3)    // 3 becomes "3"
alert(true) // true becomes "true"
alert(null) // null becomes "null" 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，字符串转换以一种显而易见的方式如您所料地发生了。

## 数字类型转换

在数学表达式和比较中可以遇到数字类型转换。这里通常是很多困惑的来源。

```
2 * "3" // 6
6 / "2" // 3
3 - "1" // 2

3 + "3" // "33" 🤬 
```

Enter fullscreen mode Exit fullscreen mode

打扰一下。是啊！`+`操作符的工作方式实际上有点不同。**如果其中一个操作数是字符串，那么所有其他操作数也转换为字符串**，它的工作方式类似于字符串串联，而不像数学表达式:

```
// One of the operands is string "2"
// JavaScript will convert every other operand to string too
1 + "2" + true // The result is "12true" 
```

Enter fullscreen mode Exit fullscreen mode

算术表达式中，`boolean`、`null`、`undefined`转换成数字如下:

```
1 + true      // true becomes 1, result is 2
1 + false     // false becomes 0, result is 1
1 + null     // null becomes 0, result is 1
1 + undefined // undefined becomes NaN, result is NaN 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。没有✨魔法，只有严格的规则！

## 布尔型转换

这种转换发生在逻辑运算中。它也遵循严格的规则，但它们大多是显而易见的:

*   `0`、`NaN`、`undefined`、`null`、`""`正在转换为`false`
*   其他一切，包括对象，都归`true`

```
if ("hello") // true
if (0)       // false
if ({})      // true 
```

Enter fullscreen mode Exit fullscreen mode

## 对象的类型转换

如果 JavaScript 需要将一个对象转换成字符串或数字，它要做什么？我们来看看:

```
parseInt({}) // NaN (converted to number)
alert({})    // "[object Object]" (converted to string)
alert([])    // "" 
```

Enter fullscreen mode Exit fullscreen mode

这些是默认的转换值。你很少会想把对象转换成原语...但是，如果您的代码需要更有意义的转换，您需要知道如何显式地设置转换规则。

当转换`object`类型(不是数组)时，JavaScript 试图找到并调用三个对象方法:

1.  调用`obj[Symbol.toPrimitive](hint)`–使用符号键`Symbol.toPrimitive`的方法。

2.  否则，如果提示类型是`"string"`调用`obj.toString()`和`obj.valueOf()`，无论存在什么。

3.  否则，如果提示类型是`"number"`或`"default"`，则调用`obj.valueOf()`和`obj.toString()`，无论存在什么。

提示是对象将要转换成的原语类型。

如您所见，如果您需要对对象进行有意义的可视化，您将需要为对象显式地设置`Symbol.toPrimitive`属性。

### 符号本原

让我们创建一个对象并设置`Symbol.toPrimitive`属性。

```
const obj = {
  name: "Default conversion"
}

const country = {
  name: "Estonia",
  population: 1291170,

  [Symbol.toPrimitive](hint) {
    // For string conversion
    if(hint == "string") {
      return `Country: ${this.name}, population: ${this.population}`
    }

    // Otherwise number conversion
    return this.population
  }
}

alert(obj) // "[object Object]"
alert(country) // "Country: Estonia, population: 1291170"
alert(country + 1) // 1291171 
```

Enter fullscreen mode Exit fullscreen mode

## 比较和类型转换

有两个具体的比较规则。

*   **进行非严格比较时，如果操作数的类型不同，Javascript 会将操作数转换为数字**:

```
0 == "0"  // true. String is converting to a number
0 === "0" // false. Strict comparison compares types too!
"0" != "" // true. There isn't type conversion 
```

Enter fullscreen mode Exit fullscreen mode

*   `null == undefined`！这里没有任何类型的转换，这些值有不同的类型！然而**在非严格比较中`undefined`等于`null`和`undefined`** 是设计好的:

```
null == undefined // true. God bless JavaScript ❤️ 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

这里我们描述了 JavaScript 进行类型转换的主要规则和方法。如果你仔细观察所有这些规则，你会发现它们在大多数情况下基本上是显而易见的。无论如何，在实际的产品代码中，我鼓励你避免隐式类型转换和奇怪的比较:

*   例如，从用户输入接收的值将是字符串类型。在进一步使用之前，将其明确转换为数字:

```
// ... imagine we handled user input event
const money = parseInt(event.target.value);
alert(typeof money == "number"); // true. Now we can safely use money as a number 
```

Enter fullscreen mode Exit fullscreen mode

*   使用模板文字代替`+`运算符连接字符串:

```
// Can be confusing to read because the result can vary depending on operands types
const result = one + two 

// Explicitly concatenated string
const text = `${one}${two}` 
```

Enter fullscreen mode Exit fullscreen mode

*   使用严格比较来比较不同类型的值，以避免隐式转换为数字:

```
const something = 0;
alert(something == false)  // true
alert(something === false) // false 
```

Enter fullscreen mode Exit fullscreen mode

* * *

就是这样！我希望这个小指南对你有所帮助，现在你可以更好地理解 JavaScript 中的类型和隐式转换。

现在你应该能够判断文章图片:

```
{} + {}    // NaN, because object is converting to NaN
[] + []    // "", because array is converting to ""
0 == "0"   // true, because "0" is converting to 0
0 == ""    // true, because empty string is converting to 0
"" != "0"  // true, because operands are the same type (string) and no conversion happens 
```

Enter fullscreen mode Exit fullscreen mode

快乐编码！〈希莫丝〉