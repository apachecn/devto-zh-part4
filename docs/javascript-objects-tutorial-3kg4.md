# JavaScript 对象教程

> 原文：<https://dev.to/jamesqquick/javascript-objects-tutorial-3kg4>

[https://www.youtube.com/embed/VIKY1DqlRtI](https://www.youtube.com/embed/VIKY1DqlRtI)

JavaScript 对象无处不在，要想成功使用 JavaScript，你必须理解它们。在本文中，让我们浏览一下使用 JavaScript 对象的基础知识。

## 什么是 JavaScript 对象

JavaScript 中的对象由任意类型的键/值对组成，它们类似于 Python 中的字典、Java 中的 HashMaps 等。我们来看看吧！

## 创建 JavaScript 对象

使用`{}`创建一个空对象。

```
const person = {}; 
```

Enter fullscreen mode Exit fullscreen mode

还可以使用 object literal 语法定义一个已经包含了键/值对的对象。在本例中，我创建了一个具有四个属性的对象:名字、姓氏、年龄和电子邮件。

```
const person = {
    first: "James",
    last: "Quick",
    age: 28,
    email: "james@learnbuildteach.com"
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理对象属性

创建具有属性的对象后，可以使用点符号来访问每个属性。在你的宾语后用一个点(。)后跟您要查找的属性的名称。

```
console.log(person.first); //"James"
console.log(person.last); //"Quick"
console.log(person.email); //"james@learnbuildteach.com" 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以使用以下语法，在括号中使用括号和您要查找的属性的名称。

```
console.log(person['first']); //"James"
console.log(person['last']); //"Quick"
console.log(person['email']); //"james@learnbuildteach.com 
```

Enter fullscreen mode Exit fullscreen mode

有了我们刚刚做的“命名符号”，你也可以传入一个变量。例如，如果我们创建一个值为“first”的变量，我们可以使用括号内的值来获取该属性。

```
const keyName = "first";
console.log(person[keyName]);//"James" 
```

Enter fullscreen mode Exit fullscreen mode

您也可以使用点符号或命名符号来更新属性。

```
person.name="Jessica";
person.age = 29;
console.log(person.name); //"Jessica"
console.log(person.age); //"29 
```

Enter fullscreen mode Exit fullscreen mode

若要从对象中删除属性，可以调用 delete，后跟要移除的属性。移除属性后，当您试图访问它时，它将返回 undefined。

```
delete person.age;
console.log(person.age); //undefined 
```

Enter fullscreen mode Exit fullscreen mode

## 对象相等和复制

比较对象的相等性可能有点棘手。重要的是要明白，当你创建一个对象时，变量本身是一个指针而不是值本身。这个指针将指向内存中存储该对象的地址。因此，即使两个对象具有相同的属性，它们也不会被认为是相等的，因为指针指向两个不同的对象。

```
const person1 = {
    name:"James"
}

const person2 = {
    name: "James"
}

console.log(person1 == person2); //false 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可以将一个指针分配给另一个指针。在这种情况下，两个 person 对象(person1 和 person2)都指向内存中的同一个对象，因此它们是相等的。

```
const person1 = {
    name:"James"
}

const person2 = person1;
console.log(person1 == person2); //true 
```

Enter fullscreen mode Exit fullscreen mode

记住，如果两个变量指向内存中的同一个对象，那么改变其中一个的属性也会改变另一个。因为两个指针都指向同一个对象，所以这些变化会反映在指向该对象的任何其他指针中。

```
const person1 = {
    name:"James"
}

const person2 = person1;

person2.name = "Bob";
console.log(person1.name); //"Bob"
console.log(person2.name); //"Bob" 
```

Enter fullscreen mode Exit fullscreen mode

## 遍历一个对象

遍历对象的键/值对的最简单方法是使用一个`let...in`循环。这将遍历这些键，您可以使用每个键来获取相关的值。

```
for(let key in person){
    console.log(`${key}: ${person[key]}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以通过调用`Object.getKeys(myObject);`获得一个对象的键列表。然后您可以使用`let...of`循环来遍历这个键列表，并做同样的事情来获得适当的值。

```
const keys = Object.keys(person);
console.log(keys); //["first", "last","age", "email"]
for(let key of keys){
    console.log(`${key}: ${person[key]}`)
} 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 物体特征

Spread 操作符是 ES6 JavaScript 中一个非常好的特性。在这种情况下，您总是需要复制对象。请记住，当我们将人员 2 分配给人员 1 时，这意味着对对象的更改在两者中都有所反映。通过使用扩展操作符，可以快速制作对象的真实副本。

```
const person3 = {
    name:"James"
}

const person4 = {...person3};

person3 == person4; //false 
```

Enter fullscreen mode Exit fullscreen mode

ES6 中还有一个奇特的特性，叫做对象析构。这允许您快速创建与对象属性相关联的变量。在没有对象析构的情况下，您需要这样做。

```
const person5 = {
    first: "James",
    last: "Quick",
}

const first = person5.first;
const last = person.last; 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以简单地这样做。

```
 const {first, last} = person5; 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果您希望用于对象中的值的变量名与键相同，您只需键入一次变量名。比如代替这个。

```
const first = "James"
const person6 = {
    first:first
} 
```

Enter fullscreen mode Exit fullscreen mode

你能做到的。

```
const first = "James"
const person6 = {
    first
} 
```

Enter fullscreen mode Exit fullscreen mode

## 重述

对象是 JavaScript 的重要组成部分，你会经常用到它们。值得花些时间来复习如何与他们合作。希望这有所帮助！