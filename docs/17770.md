# 函数和箭头函数的区别

> 原文：<https://dev.to/stephenfluin/the-differences-between-function-and-arrow-functions-4663>

有人问我经典的`function`和新的现代的`arrow function`语法之间的区别，但是如果没有例子，这有点难以解释，所以这里有两个主要的区别。

假设我们有一个名为`Data`的类，让我们强制性地创建两个函数。

```
class Data {
    saveFunction;

    constructor() {
        // Classic Function
        this.saveFunction = function (data) {
            // save my data
        }
        // Arrow Function
        this.saveFunction = (data) => {
            // save my data
        }

    }
} 
```

这两个看起来一样，但让我们深入了解两个主要的区别。

## 语法差异

所以首先现代的比较短，万岁！你甚至可以使用一些简写方式，比如当你只有一个参数时去掉圆括号，当你想返回一个表达式的结果时去掉花括号。

当您开始传递函数时，这些语法差异变得非常有用，当我们使用承诺、可观察值编写异步代码时，甚至当我们需要编写回调时，都会发生这种情况。

考虑以下两个实现。两者都试图获取一个列表并返回奇数。

经典函数风格:

```
[1,2,3,4,5,6].filter(function(item) {
  return item % 2;
} 
```

箭头功能样式

```
[1,2,3,4,5,6].filter(item => item % 2); 
```

缺少括号和表达式值的自动返回确实有助于减少这里的长度。

一旦你知道了箭头函数是如何工作的，这种简洁性就增加了事情的清晰度。

## 语义差异

还有一个更重要的区别，那就是箭头函数和经典函数的工作方式实际上略有不同。经典函数创建一个“对象”和一个有自己定义的范围`this`，而[箭头函数没有](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions#No_separate_this)。

这在我们最初的例子中变得很重要，我们在数据类内部创建函数。在经典函数中，`this`可能不会引用您可能期望的实例，但 arrow 函数会。

我们可以通过弹出下面的代码是一个 ES5 transpiler 并查看经典的 JavaScript 来看到这一点。

```
class Data {
    saveFunction;
    currentValue;

    constructor() {
        // Classic Function
        this.saveFunction = function (data) {
            // save my data
            this.currentValue = {};

        }
        // Arrow Function
        this.saveFunction = (data) => {
            // save my data
            this.currentValue = {}
        }
    }
} 
```

使用 [TypeScript Playground](https://www.typescriptlang.org/play/) 上的 transpiler，我们得到下面的 ES5:

```
var Data = /** @class */ (function () {
    function Data() {
        var _this = this;
        // Classic Function
        this.saveFunction = function (data) {
            // save my data
            this.currentValue = {};
        };
        // Arrow Function
        this.saveFunction = function (data) {
            // save my data
            _this.currentValue = {};
        };
    }
    return Data;
}()); 
```

因为现代版本使用的是它创建的特定映射的`_this`，所以对`this.currentValue`的调用正如我们所期望的那样，更新实例的属性。在经典函数中，它在函数上创建了一个新的属性，该属性很快被丢弃。