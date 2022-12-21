# JavaScript 中的对象、原型和类

> 原文：<https://dev.to/attacomsian/objects-prototypes-and-classes-in-javascript-3i9b>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/objects-prototypes-classes-javascript)。*

* * *

JavaScript 基于一个简单的面向对象的编程模型，对象是语言的基本组成部分。JavaScript 中几乎所有的东西都是对象。与 Java 等其他面向对象的编程语言不同，JavaScript 对象非常灵活**。**

 **## 物体

对象是称为属性的键值对的集合。属性的键是一个字符串或符号(也称为**属性名**),值可以是任何值。在 JavaScript 中创建对象有多种方法。最简单也是最流行的方法是使用**对象字面**语法:

```
const user = {
    fisrtName: 'John',
    lastName: 'Doe',
    age: 29 
}; 
```

也可以使用**新操作符**创建对象。`new`关键字既可以与内置的`Object`构造函数一起使用，也可以与用户自定义的构造函数:
一起使用

```
// in-built constructor function
const user = new Object();
user.fisrtName = 'John';
user.lastName = 'Doe';
user.age = 29;

// user-defined constructor function
function User(fisrtName, lastName, age) {
    this.fisrtName = fisrtName;
    this.lastName = lastName;
    this.age = age;
};

const user = new User('John', 'Doe', 29); 
```

属性值也可以是一个函数。这样的属性称为方法:

```
const user = {
    fisrtName: 'John',
    lastName: 'Doe',
    age: 29,
    fullName: function () {
        return `${this.fisrtName}  ${this.lastName}`;
    }
}; 
```

可以使用点(`.`)符号和方括号(`[]`)符号来访问属性值:

```
// dot notation
console.log(user.fullName()); // John Doe
console.log(user.age); // 29

// square bracket notation - does not work for methods
console.log(user['fisrtName']); // John 
```

你可以给现有对象添加新的属性，只要给它们赋值:

```
user.country = 'United States';

// method can also be added
user.ageRange = function() {
    return `${this.age - 5}-${this.age + 5}`;
} 
```

要从对象中删除属性，使用`delete`操作符:

```
delete user.age; 
```

要遍历一个对象的所有键，我们可以使用`for...in`循环:

```
for (const key in user) {
    console.log(user[key]);
} 
```

object literal 语法只能用于创建单个对象。为了创建多个相同类型的对象，我们必须使用对象构造函数:

```
function Animal(name, icon) {
    this.name = name;
    this.icon = icon;
};

const rabbit = new Animal('Rabbit','🐰');
const cat = new Animal('Cat','🐱'); 
```

我们不能直接给对象构造函数赋值来添加新的属性和方法。必须在对象构造函数中添加新的属性:

```
Animal.color = 'Red'; // wrong way

// correct way
function Animal(name, icon, color) {
    // ...
    this.color = color;
}; 
```

## 原型

JavaScript 中的所有对象都从另一个名为 prototype 的对象继承属性和方法。属性允许我们向现有的对象构造函数添加新的属性和方法。新属性在指定类型的所有实例之间共享，而不是仅由对象的一个实例共享。

让我们通过 prototype:
向所有类型为`Animal`的对象添加新的属性

```
// add property
Animal.prototype.color = 'White';

// add method
Animal.prototype.meow = function() {
    if(this.name === 'Cat') {
        return `${this.name} can meow!`;
    } else {
        return `${this.name} cannot meow!`;
    }
} 
```

现在上面的`cat`和`rabbit`对象拥有属性`color`和方法`meow()`，因为`Animal`的`prototype`拥有它们:

```
console.log(cat.color); // White
console.log(rabbit.meow()); // Rabbit cannot meow!
console.log(cat.meow()); // Cat can meow! 
```

如果您想通过特定类型的实例访问共享的`prototype`属性，有一个`__proto__`属性可用。通过该属性，您可以修改现有的原型属性，甚至添加新的属性。因为`prototype`属性在所有实例之间共享，所以对一个实例的`prototype`属性或方法的更改将反映在所有实例中:

```
cat.__proto__.color = 'Black';
cat.__proto__.eatMeat = true;

console.log(rabbit.color); // Black
console.log(rabbit.eatMeat); // true 
```

类似于对象，`prototype`属性或方法可以使用`delete`操作符:
移除

```
delete cat.__proto__.eatMeat;
// OR
delete Animal.prototype.eatMeat;

console.log(rabbit.eatMeat); // undefined 
```

正如我们在上面看到的，`prototype`属性是 JavaScript 中对象基础的核心部分。对此属性所做的任何更改都会影响该特定对象类型的所有实例。

## 类

ES6 (ECMA2015)中的 JavaScript 引入了类的概念。在面向对象的编程范例中，类是一个蓝图，用于创建具有属性和方法的对象，同时封装用户的实现细节。然而，JavaScript 中并不存在真正的类的概念。

JavaScript 类只不过是现有的基于原型的继承和构造函数的语法糖。让我们看一个例子:

```
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    sayHi() {
        return `👋 ${this.name}!`;
    }
} 
```

现在，如果我们想创建一个`Person`类的新实例，我们必须使用`new`操作符:

```
const bill = new Person('Bill', 25);
console.log(bill.sayHi()); // 👋 Bill! 
```

当我们创建一个`Person`类的对象时，类构造函数做如下事情:

1.  创建一个名为`Person`的新函数，并将构造函数中声明的所有属性复制到其中(`name`和`age`)。
2.  将该类的所有方法(如`sayHi()`)添加到`Person.prototype`属性中。

之后，当我们调用对象的任何方法时，它都是从`prototype`属性中获取的。

## 总结

对象是 JavaScript 语言的重要组成部分。JavaScript 中的几乎所有东西都是对象。

*   对象是键值属性的集合。可以使用对象文字语法或对象构造函数语法创建对象。
*   每当在 JavaScript 中创建新函数时，JavaScript 引擎会自动为它附加一个`prototype`属性。对此属性所做的更改在特定对象类型的所有实例之间共享。这是向现有对象构造函数添加新属性的好方法。
*   ES6 给 JavaScript 带来了类，这只不过是利用原型功能编写构造函数的一种新方法。

如果你想了解更多关于对象的知识，这里有一个深入的 [MDN 指南](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects)，它解释了如何使用对象、属性和方法。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。**