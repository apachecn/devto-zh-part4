# 在 JavaScript & TypeScript 中扩展字符串原型

> 原文：<https://dev.to/itsrainingmani/extending-the-string-prototype-in-javascript-typescript-254a>

## 什么是对象原型

从 [MDN](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes) :

JavaScript 通常被描述为一种基于原型的语言——为了提供继承，对象可以有一个原型对象，这个原型对象充当一个模板对象，它从这个模板对象继承方法和属性。一个对象的原型对象也可能有一个原型对象，它从原型对象继承方法和属性，等等。这通常被称为原型链，它解释了为什么不同的对象在其他可用的对象上定义了属性和方法。

简单地说，在原型链中，每个对象都从它上面的对象继承特性。这也允许我们通过添加原型来扩展对象的功能。

## 在 JavaScript 中扩展字符串原型

假设我们想在字符串的两边填充一个指定的字符。在 python 中，我们只需对具有最终长度的字符串调用`center()`方法，还可以选择填充字符。然而，JS 本身在 String 原型上没有这样的方法。定义的最接近的方法是`String.prototype.padEnd()`和`String.prototype.padStart()`。

我们可以简单地写一个这样的函数:

```
function padStartEnd(inputString, maxLength, fillString) {
    fillString = fillString || "  ";
    return inputString.length >= maxLength ? inputString : inputString.padStart((inputString.length + maxLength) / 2,
    fillString).padEnd(maxLength, fillString);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以像这样在任何字符串上调用这个函数:

```
let stringToPad = "help";
let paddedString = padStartEnd(stringToPad, 10, '+');
console.log(paddedString);

// Output
"+++help+++" 
```

Enter fullscreen mode Exit fullscreen mode

万岁！我们现在有一个小函数可以填充给定字符串的开始和结束。

然而，有点烦人的是，为了在字符串上执行这个相对基本的操作，我们必须提供字符串本身作为参数。如果我们能够以`String.prototype.padEnd()`和`String.prototype.padStart()`的方式在一个字符串对象上调用这个函数，那么在语法上会更加优雅。

我们将通过扩展`String.prototype`来做到这一点。

> ⚠️ **警告**:下面的例子纯粹是指导性的。请小心扩展本机类型，尤其是当您的代码将被其他人使用时，因为这可能会导致意外的行为。建议在扩展方法的名称前添加一些标识符，以便潜在用户可以区分您的代码和在类型上本地定义的方法。

事不宜迟，下面是我们如何在字符串上调用 pad startend:

```
function padStartEnd(maxLength, fillString) {
    fillString = fillString || "  ";
    return this.length >= maxLength ? this.toString() : inputString.padStart((inputString.length + maxLength) / 2,
    fillString).padEnd(maxLength, fillString);
}
String.prototype.center = padStartEnd; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们对原始函数做了一些修改。我们已经移除了`inputString`参数，因为我们将在一个字符串对象上调用该函数，该字符串对象可以通过函数中的`this`来访问。

最后一行将函数`padStartEnd()`分配给`String.prototype`上的属性`center`。

我们现在可以像这样在字符串上调用这个函数:

```
console.log("help".center(10, "+"));

// Output
"+++help+++" 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。我们已经成功扩展了`String.Prototype`！
我们可以通过使用`hasOwnProperty()`额外检查扩展是否成功。

```
console.log(String.prototype.hasOwnProperty("center"));
// expected output: true 
```

Enter fullscreen mode Exit fullscreen mode

这表明`String.prototype`对象具有指定的属性。

## 这可是在打字稿里

现在我们有了 JavaScript 中扩展的`String.prototype`的工作实现，让我们看看如何在 TypeScript 中做同样的事情。

我们将创建一个名为`string.extensions.ts`的新文件来保存我们的接口定义和实现。(您可以在与主代码相同的文件中这样做，但是将它移动到不同的文件并从您的代码中导入它会更简洁一些)。

在此`string.extensions.ts`中，添加以下代码:

```
// string.extensions.ts

interface String {
    center(maxLength: number, fillString?: string): string;
}

String.prototype.center = function (maxLength: number, fillString?: string): string {
    fillString = fillString || "  "; // If fillString is undefined, use space as default
    return this.length >= maxLength ? this.toString() : this.padStart((this.length + maxLength) / 2, fillString).padEnd(maxLength, fillString);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们通过声明一个新的接口`String`并向该接口添加函数`center`来扩充字符串的类型。
当 TypeScript 运行到同类型的两个接口中时，它会尝试合并定义，如果这不起作用，就会引发错误。

所以将`center`添加到我们的`String`接口增加了原来的`String`类型来包含`center`方法。

现在剩下的就是在你的源代码中导入这个文件，你就可以使用`String.prototype.center`！

```
import './string.extensions' // depends on where the file is relative to your source code

let stringToPad: string = "help";
console.log(stringToPad.center(10, "+"));

// Output
"+++help+++"; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了。在 JavaScript 和 TypeScript 中扩展`String.Prototype`的简单方法。您也可以使用本文中概述的方法来扩展/增加其他本机类型的原型。

如果你觉得这篇文章有帮助，或者想报告任何错误，请在 Twitter 上联系我。

编辑:改变了警告，以反映这篇文章的指导性质，并减少对本机类型原型扩展的利弊的矛盾。