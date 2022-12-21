# 快速浏览析构

> 原文：<https://dev.to/vanbalken/a-quick-look-at-destructering-3f40>

这篇文章将使用几个例子快速介绍 JavaScript(和 TypeScript)中的析构。如果您想了解更多，您应该阅读 [MDN web 文档](https://developer.mozilla.org/nl/docs/Web/JavaScript/Reference/Operatoren/Destructuring_assignment)。它写得很好，包含有用的例子。

* * *

## 基础知识

析构用于从数组(`[]`)和对象(`{}`)中提取数据，代码很少:

```
const person = ['John', 25];

let name, age;

// Without destructuring
name = person[0];
age = person[1];

// With destructering
[name, age] = person; 
```

```
const person = {name: 'John', age: 25};

const {name, age} = person; 
```

也可以使用默认值:

```
const namelessPerson = {age: 21};

const {name = 'Jane Doe', age} = namelessPerson; 
```

它可以用在一个函数中，只选择你需要的属性:

```
const person = {name: 'John', age: 25};

function hello({name}) {
    console.log(`Hello ${name}!`);
}

hello(person); // 'Hello John!' 
```

## 坏榜样

将最后两个例子结合起来，你可以写出非常难读的代码:

```
const person = {name: 'John', age: 25};
const namelessPerson = {age: 21};

function hello({name = 'Jane Doe'} = {name: 'John Doe'}) {
    console.log(`Hello ${name}!`);
}

hello(person); // 'Hello John!'
hello(namelessPerson); // 'Hello Jane Doe!'
hello(); // 'Hello John Doe!' 
```

(属性`name`和参数对象都分配了默认值。)

## TypeScript 中的一个例子

析构有用的一个具体情况是当你订阅一个返回数组的可观察对象时。例如当你使用`combineLatest` :

```
combineLatest(user$, dog$).subscribe([user, dog] => {});

// And when explicitly typed
combineLatest(user$, dog$).subscribe([user, dog]: [User, Dog] => {}); 
```