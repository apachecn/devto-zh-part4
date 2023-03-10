# 永远不要让你的类型检查失败

> 原文：<https://dev.to/aminnairi/never-fail-your-type-checking-ever-again-m69>

别告诉我狗不会笑。

有时候，知道我们正在处理的是哪种类型对我们的脚本的成功是至关重要的。我们可能会尝试使用类似于`typeof`的关键字。但是你不应该。原因如下。

```
"use strict";

console.log(typeof null);
console.log(typeof []);
console.log(typeof {});
console.log(typeof Promise.resolve()); 
```

Enter fullscreen mode Exit fullscreen mode

你们中的一些人可能认为这很简单，答案是

```
null
array
object
promise 
```

Enter fullscreen mode Exit fullscreen mode

和...没有。因为这四个都返回完全相同的东西。

```
object
object
object
object 
```

Enter fullscreen mode Exit fullscreen mode

这是因为在 JavaScript 中，几乎所有东西都是对象。甚至`null`关键词。现在你读了标题，你知道有一个更好的方法来检查对象的类型。你是对的。介绍:`Object.prototype.toString.call`。

```
"use strict";

console.log(Object.prototype.toString.call(null));
console.log(Object.prototype.toString.call([]));
console.log(Object.prototype.toString.call({}));
console.log(Object.prototype.toString.call(Promise.resolve())); 
```

Enter fullscreen mode Exit fullscreen mode

这将返回

```
[object Null]
[object Array]
[object Object]
[object Promise] 
```

Enter fullscreen mode Exit fullscreen mode

现在你可能想说，是的，这将返回正确的类型，但这种难看的格式是怎么回事？我们只是想要类型，而不是这个垃圾字符串。此外，从长远来看，一遍又一遍地输入同样的内容会让人精疲力尽。

让我们看另一个例子。

```
"use strict";

function searchNumber(numbers, number) {
    if (Object.prototype.toString.call(numbers) !== "[object Array]") {
        throw new TypeError("First argument must be an array");
    }

    if (Object.prototype.toString.call(number) !== "[object Number]") {
        throw new TypeError("Second argument must be a number");
    }

    const foundNumber = numbers.find(function(currentNumber) {
        return currentNumber === number;
    });

    if (foundNumber) {
        return true;
    }

    return false;
}

console.log(searchNumber([1, 2, 3, 4, 5], 3)); // true
console.log(searchNumber([1, 2, 3, 4, 5], 0)); // false 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们定义了一个函数，它将帮助我们在一组数字中搜索一个数字。哦，顺便说一下，你可以在任何事情上使用`Object.prototype.toString.call`。我们也做了类型检查，但是有很多字符需要输入。我觉得是时候让它成为一个功能了。

```
"use strict";

function type(target) {
    return Object.prototype.toString.call(target);
}

console.log(type(null));
console.log(type([]));
console.log(type({}));
console.log(type(Promise.resolve())); 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们

```
[object Null]
[object Array]
[object Object]
[object Promise] 
```

Enter fullscreen mode Exit fullscreen mode

整洁！但是如果能模仿`typeof`给我们的东西就太棒了:一个简单、普通的带有对象类型的字符串。让我们相应地更新我们的函数。

```
function type(target) {
    const computedType = Object.prototype.toString.call(target);
    const stripped = computedType.replace("[object ", "").replace("]", "");
    const lowercased = stripped.toLowerCase();

    return lowercased;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到了很酷的东西。

```
null
array
object
promise 
```

Enter fullscreen mode Exit fullscreen mode

是时候在我们的函数中使用它了，你不觉得吗？

```
"use strict";

function type(target) {
    const computedType = Object.prototype.toString.call(target);
    const stripped = computedType.replace("[object ", "").replace("]", "");
    const lowercased = stripped.toLowerCase();

    return lowercased;
}

function searchNumber(numbers, number) {
    if (type(numbers) !== "array") {
        throw new TypeError("First argument must be an array");
    }

    if (type(number) !== "number") {
        throw new TypeError("Second argument must be a number");
    }

    const foundNumber = numbers.find(function(currentNumber) {
        return currentNumber === number;
    });

    if (foundNumber) {
        return true;
    }

    return false;
}

console.log(searchNumber([1, 2, 3, 4, 5], 3)); // true
console.log(searchNumber([1, 2, 3, 4, 5], 0)); // false
console.log(searchNumber({1: "1", 2: "2"}, 2));
// TypeError: First argument must be an array 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个可靠的方法在运行时计算我们的类型！

JavaScript 也有一些`isSomething`函数，如果你不想使用这个函数，并且只使用一些类型，比如 array，它有一个`Array.isArray`方法来检查一个对象是否是一个数组。但是我认为使用一个单独的函数可以很好地规范你在代码中输入 check 的方式。

感谢阅读。我希望本教程已经说服您在运行时键入 check，并使用这个小助手函数来完成这项工作。如果你有任何问题或通知，请在评论区告诉我！