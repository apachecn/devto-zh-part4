# 改变你创建对象的方式——Javascript Weekly

> 原文：<https://dev.to/ganeshmani/change-the-way-you-create-an-object-javascript-weekly-k95>

在本文中，我们将看到用 javascript 创建对象的不同方法。改变创建对象的方式——Javascript Weekly

[循环中的承诺- javascript 周刊](https://cloudnweb.dev/2019/07/promises-inside-a-loop-javascript-es6/)

[原型继承- Javascript 周刊](https://cloudnweb.dev/2019/07/prototypal-inheritance-javascript-weekly/)

[理解 Javascript 中的闭包](https://cloudnweb.dev/2019/07/understanding-closures-in-javascript-javascript-weekly/)

[Javascript 中的 Currying】](https://cloudnweb.dev/2019/07/currying-in-javascript-javascript-weekly/)

对象在 javascript 生态系统中扮演着重要的角色。作为 javascript 开发人员，我们在编写代码时会创建大量的对象。我们会看到一些不同的方法来做到这一点

## 作为文字的对象

这是创建对象的默认方式。用花括号创建对象是对象创建中流行的方式

```
const User = {
  name : "John",
  age : 25
}
```

对象作为文本允许动态地将属性添加到对象中。例如，如果你想给**用户**对象添加一个属性，你可以这样做

```
User.address = "Country"
```

除此之外，在对象文本中还可以有一个嵌套属性

```
const Company = {
  name : "Test",
  tax : {
      id : "12345"
   }
}
```

## “新”运算符

你也可以用关键字 **new** 创建一个对象。

```
function Document(name,category) {
  this.name = name;
   this.category = category
}

let salarydocument = new Document("salary","SALARY");

let insurance = new Document("insurance","INSURANCE");
```

使用**新的**操作符的主要优点是，您可以使用原型来链接方法。[原型链](https://cloudnweb.dev/2019/07/prototypal-inheritance-javascript-weekly/)是使用**新**关键词中的一个重要概念

```
function Language(name,shortform) {
  this.name = name;
  this.shortform = shortform;
}

Language.prototype.getShortForm = function(){
   return this.shortform;
}

let english = new Language("english","en");
```

## 对象.创建()方法

对象创建方法允许您从现有对象创建对象。

例如，假设您有一个对象**消息**，创建一个与**消息**具有相同属性的对象。你可以这样做

```
let Message = {
  title : "Hola!",
  body : "Welcome"
}

let greeting = Object.create(Message);

console.log(greeting.title);
```

你也可以给**问候语**对象添加属性

```
let Message = {
  title : "Hola!",
  body : "Welcome"
}

let greeting = Object.create(Message,{type : {
  writable : true,
  enumerable : true,
  configurable : false,
  value : "Data"
}});

console.log(greeting.type);
```

## 使用 ES6 类

ES6 类是用 javascript 创建对象的另一种方式

```
class User {

  constructor(name,age) {
     this.name = name;
     this.age = age;

  }

  getInfo() {
    console.log(`Hey ${this.name} Age : ${this.age}`);
  }
}

let admin = new User("admin",40);

admin.getInfo();
```

这些都是在 javascript 中创建对象的不同方法。

改变创建对象的方式——Javascript Weekly

我们将在接下来的文章中看到更多关于 Javascript 的内容。我非常喜欢 Eric Elliot 在 Javascript 方面的工作。看看他的最新文章。

[React 挂钩会取代高阶组件(hoc)吗？](https://medium.com/javascript-scene/do-react-hooks-replace-higher-order-components-hocs-7ae4a08b7b58)

直到那时快乐编码:-)