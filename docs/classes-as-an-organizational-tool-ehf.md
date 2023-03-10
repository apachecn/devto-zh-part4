# 作为组织工具的类

> 原文：<https://dev.to/austinbh/classes-as-an-organizational-tool-ehf>

作为一个目前在我的编码训练营的 react 部分的人，我最近一直试图更好地理解 Javascript 类。

希望在本文结束时，你我都能对 Javascript 类及其工作原理有更好的理解。

# 什么是 Javascript 类？

Javascript 类实际上只是一种特殊类型的函数。就像函数表达式和函数声明一样，类也有类表达式和声明。

### 函数表达式

```
let foo = (a, b) => console.log(a + b)

foo(2, 3)

// => 5 
```

### 功能声明

```
foo(2, 3)

function foo(a, b) {
   console.log(a + b)
}

//=> 5 
```

虽然编写函数的两种方法将返回相同的结果，但是如果我们像调用函数声明那样调用它，我们看到的函数表达式将是未定义的。函数声明是被提升的，这意味着在我们运行调用我们函数的代码行之前，它们被评估和保存。尽管这对我们的函数很重要，但对类来说，它的工作方式有点不同。

# 类声明

正如所承诺的，类的声明有点不同。类声明的提升方式不同于函数声明，这意味着在创建之前不能创建该类的新实例。

#### 关于吊装的简要说明

为了这篇博客的目的，我们需要了解的关于提升的所有事情是，在我们直接调用函数之前，它会导致我们的函数声明被解释。这意味着我们可以在声明函数之前调用它，并且函数调用仍然是成功的。

### 类表达式

```
let foo = class bar {
   constructor(hello, world) {
      this.hello = hello,
      this.world = world
   }
}

const baz = new foo("Hello", "World")
console.log(baz.hello)
console.log(baz.world)

//=> "Hello"
//=> "World" 
```

### 类声明

```
class fooBar {
   constructor(hello, world) {
      this.hello = hello,
      this.world = world
   }
}

const baz = new fooBar("Hello", "World")
console.log(baz.hello)
console.log(baz.world)

//=> "Hello"
//=> "World" 
```

在这种情况下，在我们试图创建这些类的新实例之前，我们的表达式和声明都必须完成。然而，我们能够使用类表达式来引用使用 foo 的 bar 类。

# 为什么 Javascript 类很重要？

Javascript 类非常适合包含代码。它们允许我们定义一个类，为该类创建一个构造函数，然后编写修改该类的方法。例如，如果你想写一个取直角三角形的两条边并显示斜边的函数，你可以很容易地把它和一个类结合起来。

### 三角形类

```
class triangle {
   constructor(a, b) {
      this.a = a,
      this.b = b
   }
   findHypotenuse() {
      return Math.sqrt((this.a**2)+(this.b**2))
   }
   get c() {
      return this.findHypotenuse()
   }
}

const foo = new triangle(3, 4)
console.log(foo.c)

//=> 5 
```

使用我们编写的 and 斜边函数，我们能够获得直角三角形的斜边，并将该值保存为 c。返回斜边的长度。

虽然这看起来像是寻找斜边的大量工作，这个类也可以用来创建额外的函数，我们用它来与直角三角形交互。从这个意义上说，通过将三角形的所有函数放在一个地方，我们可以更好地组织代码。

# 使用‘扩展’在类之间共享函数

extends 关键字可用于允许子类继承其父类的结构。为了与上面的几何示例保持一致，我们可以看到它显示为一个父“shape”类，该类可能有一个构造函数来保存和显示长度和宽度。

```
class shape {
   constructor(length, width) {
      this.length = length,
      this.width = width
   }
}
class triangle extends shape {
   findHypotenuse() {
      return Math.sqrt((this.length**2)+(this.width**2))
   }
   get hypotenuse() {
      return this.findHypotenuse()
   }
}
class rectangle extends shape {
   findArea() {
      return this.length * this.width
   }
   get area() {
      return this.findArea()
   }
}

const foo = new triangle(3, 4)
console.log(foo.hypotenuse)
const bar = new rectangle(4, 5)
console.log(bar.area)

//=> 5
//=> 20 
```

在这个例子中，我能够为矩形和三角形类编写两个函数，它们继承了父 shape 类的构造函数。由于父类具有两个子类都使用的属性，我们不必为每个子类创建额外的构造函数。

这也意味着当我们增加程序的复杂性和添加更多的形状时，我们会节省越来越多的代码行。我们还可以考虑，我们的父类可以有一些函数也传递给子类。

## 参考文献

*   [https://developer.mozilla.org/en-US/docs/Glossary/Hoisting](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Classes/constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/constructor)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
*   [https://developer . Mozilla . org/en-US/docs/web/JavaScript/Reference/Operators/function](https://developer.mozilla.org/en-US/docs/web/JavaScript/Reference/Operators/function)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Statements/function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)