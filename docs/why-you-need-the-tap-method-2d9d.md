# 为什么需要 tap 方法

> 原文：<https://dev.to/aminnairi/why-you-need-the-tap-method-2d9d>

假设您有一个脚本，它通过多次转换来处理一个数组，最终计算出结果。

```
"use strict";

const input = "732829320";

const valid =
    Array
        .from(input)
        .map(character => parseInt(input) || 0)
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .map(digit => digit > 9 ? digit - 9 : digit)
        .reduce((sum, digit) => sum + digit) % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们使用了许多数组方法来简化脚本的开发，如果没有这些方法，脚本可能会非常冗长。但是，仍然有一个问题，不可能知道发生了什么，因为我真的很累，而且是深夜。即使我们的输入有效，`valid`变量也存储`false`值。

顺便说一下，我正在验证一个警号，这是一个在法国用于识别公司的特殊企业识别号。你不需要了解这里发生了什么，但对于那些好奇的人来说，它使用的是 Luhn 算法，这与用于验证 VISA 信用卡号码的算法相同。

也许你尝试过类似的东西。

```
"use strict";

const input = "732829320";

const valid =
    Array
        .from(input)
        .forEach(character => console.log(character))
        .map(character => parseInt(input) || 0)
        // Error: cannot read property map of undefined
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .map(digit => digit > 9 ? digit - 9 : digit)
        .reduce((sum, digit) => sum + digit) % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

*警告:这不是生产就绪代码。不要复制/粘贴它来解决你的应用程序中的这个问题！这只是一个借口，给你一个如何使用`tap`方法的例子。*

不幸的是，由于`forEach`方法将返回 undefined，这意味着它不能被其他对`map`、`filter`、`reduce`等的调用所链接，所以它不能工作...

但是我们可以存储每个步骤的值，并且只记录每个步骤的输出。这是一个解决办法。

```
"use strict";

const input = "732829320";

const array = Array.from(input);

console.log(array);

const digits = array.map(character => parseInt(input) || 0);

console.log(digits);

const multiplied = digits.map((digit, index) => index % 2 === 0 ? digit : digit * 2)

console.log(multiplied);

const digitSum = multiplied.map(digit => digit > 9 ? digit - 9 : digit);

console.log(digitSum);

const sum = digitSum.reduce((sum, digit) => sum + digit);

console.log(sum);

const valid = sum % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

但它真的很冗长，像很多。我不得不为我的变量想出新的名字，这是我浪费时间的事情，因为除了记录它们的目的，我不会使用它们。

但是它工作了，并且我最终设法弄清楚为什么我有一个错误。变量`digits`的第二个日志给出了类似这样的内容:

```
[ 732829320,
  732829320,
  732829320,
  732829320,
  732829320,
  732829320,
  732829320,
  732829320,
  732829320 ] 
```

Enter fullscreen mode Exit fullscreen mode

乍一看这很奇怪，因为我希望将所有的字符转换成一个数字。但实际上，我解析的是`input`变量，而不是`character`变量。这是我的错误。我找到了它，并成功验证了我的脚本。

```
"use strict";

const input = "732829320";

const valid =
    Array
        .from(input)
        .map(character => parseInt(character) || 0)
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .map(digit => digit > 9 ? digit - 9 : digit)
        .reduce((sum, digit) => sum + digit) % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

但是我们能做得更好吗？是啊！通过使用`tap`方法。简而言之，在这种情况下，一个`tap`方法将帮助你循环遍历你的数组，而不触及它，并将返回它以在其他调用中被链接。如果你不明白，没关系。一个例子胜过一百句话。

```
"use strict";

const input = "732829320";

const valid =
    Array
        .from(input)
        .tap(character => console.log(character))
        .map(character => parseInt(character) || 0)
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .map(digit => digit > 9 ? digit - 9 : digit)
        .reduce((sum, digit) => sum + digit) % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们使用了`tap`方法来记录我们的字符，然后在下一个`map`调用中将它们映射到数字。我所做的就是在这些调用和 tada 之间转移我的`tap`方法，我们甚至不必在代码中制造混乱就可以获得数据记录。这里的`tap`方法将产生以下输出。

```
7
3
2
8
2
9
3
2
0 
```

Enter fullscreen mode Exit fullscreen mode

我们可以继续前进，并尽可能多地分支我们的`tap`方法，因为根据定义，它将总是返回相同的东西，这意味着一个数据数组。

让我们疯狂一点，到处分支。

```
"use strict";

const input = "732829320";

const valid =
    Array
        .from(input)
        .tap(character => console.log(character))
        .map(character => parseInt(character) || 0)
        .tap(character => console.log(character))
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .tap(character => console.log(character))
        .map(digit => digit > 9 ? digit - 9 : digit)
        .tap(character => console.log(character))
        .reduce((sum, digit) => sum + digit) % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

当然，这将记录许多事情，也许不是调试我们代码的最好方法，但它是一个例子，说明使用这种方法可以走多远。当然，您可以通过将`console.log`作为一级函数传递来缩短这个调用。

```
"use strict";

const input = "732829320";

const valid =
    Array
        .from(input)
        .tap(console.log)
        .map(character => parseInt(character) || 0)
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .map(digit => digit > 9 ? digit - 9 : digit)
        .reduce((sum, digit) => sum + digit) % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

或者用它做别的事情！但是要记住，它总是原封不动地返回数组，所以即使你试图更新数组，也不会将更新后的值返回给下一个链式调用！

好的，好的...我现在将向您展示如何实现这个所谓的`tap`方法。首先，我们需要增强 JavaScript 中的`Array`对象的能力，以便能够像那样链式调用`tap`方法。

```
Array.prototype.tap = function() {
    // ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要找到一种方法来获得我们想要迭代的数组。我们可以通过使用`this`关键字来获得完整的数组。让我们使用一个`for...of`循环来遍历数组的每个元素。

```
Array.prototype.tap = function() {
    for (const element of this) {
        // ...
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要做点什么...如你所见，在前面的例子中，我们作为一等公民传递了一个函数。看起来我们得到了一个回调作为参数。让我们通过将数组中当前迭代的元素传递给回调函数来使用它。

```
Array.prototype.tap = function(callback) {
    for (const element of this) {
        callback(element);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了防止中断前面的调用链，我们要做的最后一件事就是原封不动地返回数组。因为`for...of`循环不会更新这里的数组，所以我们可以安全地返回引用这里的原始数组的`this`关键字。

```
Array.prototype.tap = function(callback) {
    for (const element of this) {
        callback(element);
    }

    return this;
}; 
```

Enter fullscreen mode Exit fullscreen mode

但是没有什么告诉我们 ECMAScript 标准背后的人不会实现一个`tap`方法作为`Array`原型的一部分。也许他们会看到这篇文章，想想这个功能到底有多大用处！如果您保持您的脚本不变，并使用一个新的(假设的)JavaScript 版本来实现这样的功能，您可能最终会破坏您的脚本，因为这个定义将与标准定义冲突。我们需要增加一名特别警卫来防止这种情况发生。

```
if (!Array.prototype.tap) {
    Array.prototype.tap = function(callback) {
        for (const element of this) {
            callback(element);
        }

        return this;
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

啊！那更好。我们也可以使用数组的`forEach`方法使`for...of`循环成为一个线性的。由于`this`是一个数组，它可以很容易地用于这个目的，只是为了节省一些字节。

```
if (!Array.prototype.tap) {
    Array.prototype.tap = function(callback) {
        this.forEach(element => callback(element));

        return this;
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

这是最终的源代码。

```
"use strict";

if (!Array.prototype.tap) {
    Array.prototype.tap = function(callback) {
        this.forEach(element => callback(element));

        return this;
    };
}

const input = "732829320";

const valid =
    Array
        .from(input)
        .tap(console.log)
        .map(character => parseInt(character) || 0)
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .map(digit => digit > 9 ? digit - 9 : digit)
        .reduce((sum, digit) => sum + digit) % 10 === 0;

console.log(valid); 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以通过使用这个简洁的小技巧轻松跟踪您的状态和 bug！

您也可以使用`map`来模拟这种行为，而不必为`tap`方法编写定义。

```
const valid =
    Array
        .from(input)
        .map(character => { console.log(character); return character; })
        .map(character => parseInt(character) || 0)
        .map((digit, index) => index % 2 === 0 ? digit : digit * 2)
        .map(digit => digit > 9 ? digit - 9 : digit)
        .reduce((sum, digit) => sum + digit) % 10 === 0; 
```

Enter fullscreen mode Exit fullscreen mode

这完全可行！它的优点是不会冒与假设的 ECMAScript 对`tap`方法的定义冲突的风险(尽管我们为这种情况增加了一个防护),缺点是有点拗口。

该不该用？有人说，在某些情况下使用基于原型的继承会导致有问题的行为，在出现错误的情况下很难跟踪这些行为。但是我想我们都同意，如果使用得好，这些类型的模式对于开发人员来说是非常强大和令人愉快的。评论区有一段有趣的对话延续了这个想法，所以我建议你不要就此止步，继续前进！