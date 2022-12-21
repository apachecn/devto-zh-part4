# JS 扩展语法

> 原文：<https://dev.to/harbolaez/js-spread-syntax-ldg>

# JS 传播语法

### 学习 JavaScript Spread 运算符的基础知识

* * *

扩展语法是 ES6 引入的。是一个非常好的工具，可以让您的 javascript 代码更加简洁易读。

### **MDN** 定义:

> Spread 语法允许在需要零个或多个参数(对于函数调用)或元素(对于数组文本)的地方扩展可迭代对象，例如数组表达式或字符串，或者在需要零个或多个键值对(对于对象文本)的地方扩展对象表达式。

让我们从克隆它们的对象开始:

```
const person = {
  firstName: 'Henry',
  lastName: 'Arbolaez',
};

const personWithAge = { ...person, age: 27 }; 
```

Enter fullscreen mode Exit fullscreen mode

合并两个对象:

```
const profession = {
  career: 'Software Engineer',
  work: 'Lavu',
};

const personalInformation = { ...personWithAge, ...profession };

/**
 * personalInformation becomes
  age: 27;
  career: 'Software Engineer';
  firstName: 'Henry';
  lastName: 'Arbolaez';
  work: 'Lavu';
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以利用 spread 语法来复制数组的:

```
const numbers = [1, 2, 3, 4, 5];
const copiedNumbers = [...numbers];

// copiedNumbers becomes [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

一种更好的方法合并数组的

```
const numbers = [1, 2, 3];
const numbers2 = [6, 7, 8];

const numbers3 = [...numbers, ...numbers2];

// numbers3 becomes [1, 2, 3, 6, 7, 8] 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以使用分配一些提醒值...休息:

```
// note `rest` can be name anything you'll like
const [a, b, ...rest] = ['a', 'b', 'c', 'd', 'e', 'f', 'g'];

// a    => "a"
// b    => "b"
// rest => ['c', 'd', 'e', 'f', 'g'] 
```

Enter fullscreen mode Exit fullscreen mode

用扩展语法传递参数

```
const dateArray = [2019, 1, 15];
const date = new Date(...dateArray);
// @return Fri Feb 15 2019 00:00:00 GMT-0500 (Eastern Standard Time) 
```

Enter fullscreen mode Exit fullscreen mode