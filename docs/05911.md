# 重构时重命名变量

> 原文：<https://dev.to/paramharrison/rename-variables-while-destructuring-54f4>

在我的上一篇教程中，我简要介绍了 JavaScript 中的析构。如果你还不知道，什么是析构，以及如何在析构时设置默认值。请点击查看

在开始在析构函数内部重命名之前，我们将会看到一些需要这样做的情况。

*   当从服务器返回的对象没有更好的键名时。例子，

```
const obj = {
  prop1: 'Param',
  prop2: 26,
}; 
```

在上面的片段中，如果不知道后端的细节，没有人知道什么是 prop1 和 prop2。如果我们析构并使用同一个变量，会使前端代码不可读，以后也不可调试。

*   当两个不同的对象中有相似的对象道具时。例子，

```
const obj1 = {
  name: 'Param',
  age: 26, // same key `age` present in obj2
};

const obj2 = {
  firstName: 'Ahmed',
  lastName: 'John',
  age: 29, // same key `age` present in obj1
}; 
```

现在，两个对象都有了`age`键，我们不能像 age 一样析构两个变量。相反，我们需要重命名其中任何一个。

让我们看看如何做到这一点，

对于示例 1，这就是如何对它进行析构和重命名。

```
const { prop1: name, prop2: age } = obj;
console.log(name, age); // Param, 26 
```

对于例 2，

```
const { age: ageOfParam, ...restOfParam } = obj1;
const { age: ageOfAhmed, ...restOfAhmed } = obj2;

console.log(ageOfParam, ageOfAhmed); // 26, 29 
```

语法不是很好用吗？😎

```
// Syntax

const { propKey: NewVariableName } = Object; 
```

析构在 javascript 中是非常强大的，它有很多用例，并且通过几个用例使开发人员的工作变得更容易。

点击此处查看示例，

[https://codesandbox.io/embed/rename-while-destructuring-6qrob](https://codesandbox.io/embed/rename-while-destructuring-6qrob)