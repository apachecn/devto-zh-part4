# 原型继承——时光倒流之旅

> 原文：<https://dev.to/ogwurujohnson/prototypal-inheritance-a-trip-back-in-time-2f70>

在类成为 javascript 中的事物之前，原型是 JavaScript 中面向对象的做事方式。这似乎让一些人对 javascript 敬而远之，而另一些人则完全忽略了它。

目前，学习 javascript 的人尽一切可能避免原型开发。如果在 es6 时代学习 javascript 的每个人都理解当我们在 Javascript 中使用类、类继承和 super()关键字时幕后发生了什么，那就好了。

要开始学习本教程，我们首先必须重温我们一开始学习的基本概念，其中之一是`objects`。
对象是键/值对。创建对象最常见的方法是使用花括号{}，并使用点符号向对象添加属性和方法。为了有效地学习 JavaScript 中的原型，我们将把我们的思想引回到`objects`以及它们是如何被使用的。

### 原型:

原型是所有 JavaScript 对象相互继承的机制。

```
 function Person(attributes) {
     this.age = attributes.age;
     this.name = attributes.name;
     this.occupation = attributes.occupation;
   } 
```

Enter fullscreen mode Exit fullscreen mode

给定构造函数 person，我们希望使用 Person.prototype
引入一个 speak 属性

```
 Person.prototype.speak = function () {
      return `Hello, my name is ${this.name}`;
   }; 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经将`speak`属性添加到了`Person`构造函数中，它现在完全拥有它，并且可以将它传递给`Person`的每个实例，而不必在任何对象上创建新的属性。

#### 继承使用原型:

让我们使用原型来看看 JavaScript 中的继承是如何工作的。我们创建一个`Child`构造函数

```
 function Child(childAttributes) {
      Person.call(this, childAttributes);
      this.isChild = childAttributes.isChild; // this will be a special attribute to Child
   } 
```

Enter fullscreen mode Exit fullscreen mode

通过编写代码行`Person.call(this, childAttributes)`，我们将`this`绑定到`Person`，这确保了`Child`构造函数继承了`Person`构造函数的属性，这个非常酷的过程已经被`Es6`中的`super`关键字抽象掉了。

一个问题仍然存在，我们的继承还没有完成，因为`Child`构造函数还不知道 Person 原型。我们必须使用`Object.create()`手动告诉`Child`关于`Person`的信息。

```
Child.prototype = Object.create(Person.prototype); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想添加更多的属性，并且希望它们是特定于`Child`构造函数的，我们可以通过
来实现

```
 Child.prototype.cry = function () {
       return `Baby crying...`;
   }; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经将`Person`原型与`Child`原型链接起来。最终，我们将免费获得 class 关键字的链接，但是看到`Object.create()`真的很好，因为它展示了`class`关键字是如何在幕后工作的。

现在我们已经创建了一个子构造函数，让我们把 Ben 介绍给这个家族:

```
 const ben = new Child({
      age: NaN,
      name: Ben Halpern,
      occupation: Comedian
   }); 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，我们使用原型从`Child`构造函数的继承来访问我们的`Person`属性。现在我们可以调用 ben.speak()看看会发生什么。

`Hello, my name is Ben Halpern`

我们只是使用了从原型的继承来使用 ben.speak。如果您有任何问题和反馈，请在评论中留言。

干杯🥂🎉🎉🎉