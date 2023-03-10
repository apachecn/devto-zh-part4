# JavaScript 箭头函数简介

> 原文：<https://dev.to/attacomsian/introduction-to-javascript-arrow-functions-20f>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/javascript-arrow-functions)。*

* * *

ES6/ECMAScript 2015 中引入的箭头函数是常规 ES5 函数在语法上的紧凑替代。它们无疑是 ES6 最受喜爱的功能之一，如今被广泛使用。Arrow 函数引入了一种编写简洁函数的新方法。

下面是一个用 ES5 函数语法写的函数:

```
function absolute(num) {
    return Math.abs(num);
}

absolute(-9);   //9 
```

现在，这里是使用 ES6 箭头函数语法编写的同一个函数:

```
const absolute = (num) => {
    return Math.abs(num);
}

absolute(-9);   //9 
```

如果函数体只包含一条语句，我们甚至可以省略括号，将所有内容写在一行:

```
const absolute = (num) => Math.abs(num); 
```

## 功能参数

如果没有参数，只需在`=>` :
前加一个空括号即可

```
const pi = () => Math.PI; 
```

你甚至可以用`_` :
替换空括号

```
const pi = _ => Math.PI; 
```

如果您有一个或多个参数，只需在括号中传递它们:

```
const abs = (num) => Math.abs(num); // on parameter
const sum = (a, b) => a + b;    // two parameters 
```

如果只有一个参数，可以完全去掉括号:

```
const abs = num => Math.abs(num); 
```

## 简洁 vs 块状体

一个箭头函数可以有一个**简洁体**或者**块体**。如果函数只包含一条语句，可以使用简洁的函数体。在简洁的主体中，只指定了隐式返回值的表达式(不使用`return`关键字):

```
const multiply = (a, b) => a * b; 
```

在块体中，必须使用显式的`return`语句:

```
const multiply = (a, b) => {
    return a * b;
}; 
```

看这些花括号。在块体中需要它们来包装语句:

```
const oddNumber = (num) => {
    if(num % 2 == 0) {
        return false;
    } else {
        return true;
    }
}; 
```

## 对象文字量

如果你使用简洁体返回一个对象文字，它必须用括号括起来，以避免被当作块体:

```
const json = () => ({x: 2}); 
```

## `this`关键词

在正则函数表达式中，`this`关键字根据调用函数的**上下文**被绑定到不同的值:

*   构造函数情况下的新对象。
*   `undefined`在严格模式下函数调用。
*   如果函数作为对象方法调用，则为父对象。

例如，这里有一个具有`fullName()`功能的人对象:

```
const person = {
    firstName: 'Mike',
    lastName: 'Lilly',
    fullName: function () {
        return `${this.firstName}  ${this.lastName}`;
    }
};

person.fullName(); // Mike Lilly 
```

调用 person 对象的`fullName()`方法返回这个人的全名。

然而，一个 arrow 函数没有自己的`this`并且在词汇上被**绑定。这实质上意味着`this`范围是从包含 arrow 函数的代码中继承的**。因此，在查找当前作用域中不存在的`this`时，ES6 arrow 函数将使用定义它的作用域中的`this`的值。这就是为什么对`fullName()`的调用将不起作用，并将返回一个未定义的值:**** 

```
const person = {
    firstName: 'Mike',
    lastName: 'Lilly',
    fullName: () => {
        return `${this.firstName}  ${this.lastName}`;
    }
};

person.fullName(); // undefined undefined 
```

这个箭头函数不适用于对象方法。您也不应该将它们用作构造函数，以免出错。

## DOM 事件处理

在处理事件时，箭头函数可能是个问题。DOM 事件侦听器将`this`设置为目标元素。现在，如果您使用 arrow 函数进行回调，`this`关键字将不会绑定到目标元素，而是绑定到它的父作用域。

```
const button = document.getElementsByTagName('button')
button.addEventListener('click', () => {
  this.classList.toggle('blur');
}); 
```

现在如果你点击按钮，你会得到一个`TypeError`，因为`this`指的是这个范围内的`window`。如果回调函数需要一个**动态上下文**，则需要一个正则函数表达式:

```
const button = document.getElementsByTagName('button')
button.addEventListener('click', function() {
  this.classList.toggle('blur');
}); 
```

## 结论

箭头函数提供了一种在 ES6 中编写简洁函数的新方法。它们更短，没有`this`关键字的绑定，隐式返回值(当使用简洁体时)，在现代代码库中被广泛使用。箭头函数不适合作为对象方法，也不能用作构造函数。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。

* * *

<small>照片由[尼克·费因斯](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](/search/photos/arrow?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 上拍摄</small>