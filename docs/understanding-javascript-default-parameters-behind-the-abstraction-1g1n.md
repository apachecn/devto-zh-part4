# 理解 JavaScript 默认参数——抽象背后

> 原文：<https://dev.to/dutiyesh/understanding-javascript-default-parameters-behind-the-abstraction-1g1n>

MDN 将默认参数描述为:
*默认函数参数允许**命名的参数**在没有传值或未定义的情况下用**默认值**初始化。*😕

定义可以重新表述为:
如果没有值或`undefined`被传递给**参数**，则使用**默认值**。😌

在构建定制默认参数之前，让我们熟悉一下定义中提到的一些术语。

## 参数

参数是调用函数时传递的参数的名称。

```
function greeting(username) {
  return `Hi ${username}!`;
}

greeting('Bruce'); // Hi Bruce! 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，参数`username`是赋予自变量`'Bruce'`的名称。

👩‍🏫**提问时间:**如果我们在调用函数时没有给参数`username`传递任何值，函数将返回什么？

```
function greeting(username) {
  return `Hi ${username}!`;
}

greeting(); // Hi undefined! 
```

Enter fullscreen mode Exit fullscreen mode

**回答:**它会返回`Hi undefined!`。

**原因:**如果我们不向参数传递任何值，JavaScript 就会使用`undefined`作为默认值。
在我们的例子中，因为我们没有传递任何值给`username`参数，JavaScript 给它赋值`undefined`并返回`Hi undefined!`。

那么如何才能避免`undefined`案例呢？

## 默认值

ES6 为此类场景引入了默认参数。

```
function greeting(username = 'Anonymous') {
  return `Hi ${username}!`;
}

greeting(); // Hi Anonymous! 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们不向参数`username`传递任何值，JavaScript 将使用`'Anonymous'`值来代替`undefined`。

* * *

## 让我们建立自定义默认参数💪

我们将需要 2 样东西:
I .一个参数列表及其值
II。值类型检查器

### I .一个参数列表及其值📋

JavaScript 函数提供了一个名为`arguments` object 的局部变量来保存所有的参数值。

```
function greeting() {
  console.log(arguments);     // { 0: 'Bruce' }
  console.log(arguments[0]);  // 'Bruce'
}

greeting('Bruce'); 
```

Enter fullscreen mode Exit fullscreen mode

`arguments`是一个类似数组的对象。它保存调用函数时使用的所有参数值，从索引 0 开始。
在我们的`greeting`函数中，因为我们用参数`'Bruce'`调用它，`arguments`对象将把它保存在第 0 个索引处。

### 二世。值类型检查器🕵️

JavaScript 提供了一个名为`typeof`的一元运算符，用于计算操作数的类型并返回一个字符串值。

```
function greeting() {
  console.log(arguments);            // { 0: 'Bruce' }
  console.log(typeof arguments[0]);  // 'string'
}

greeting('Bruce'); 
```

Enter fullscreen mode Exit fullscreen mode

👩‍🏫**提问时间:**如果我们不传递任何值，会是什么类型？

```
function greeting() {
  console.log(arguments);            // { }
  console.log(typeof arguments[0]);  // 'undefined'
}

greeting(); 
```

Enter fullscreen mode Exit fullscreen mode

**回答:**它会返回`'undefined'`。

**原因:**由于我们在调用`greeting`函数时没有传递任何参数值，`arguments`将是一个空对象，JavaScript 将返回索引为 0 的`'undefined'`值。

* * *

## 让我们结合目前所学，开始构建自定义默认参数。

```
function greeting(username) {
  username = typeof arguments[0] !== 'undefined' ? arguments[0] : 'Anonymous';

  return `Hi ${username}!`;
}

greeting(); // Hi Anonymous! 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`greeting`函数中，我们使用`argument[0]`访问第一个参数的值，并使用`typeof`操作符检查其类型。
如果它的计算结果不是`'undefined'`，这意味着在调用`greeting`函数时在索引 0 处传递了一个参数，我们可以将该值用于`username`，否则使用`'Anonymous'`作为默认值。

* * *

## 用不同的输入测试我们的功能

```
function greeting(username) {
  username = typeof arguments[0] !== 'undefined' ? arguments[0] : 'Anonymous';

  return `Hi ${username}!`;
}

greeting('Bruce');      // Hi Bruce!
greeting(undefined);    // Hi Anonymous!
greeting('undefined');  // Hi undefined! (Gotcha! We are passing undefined 
                        // wrapped up in quotes as a string) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

希望这篇文章能够让你清楚地了解 JavaScript 默认参数背后的背景。❤️❤️

请关注我，我们将在下周发布下一篇**【抽象背后】**帖子，敬请关注！

> 封面图片的灵感来自 Wes Bos 的“可视化 JavaScript 函数”