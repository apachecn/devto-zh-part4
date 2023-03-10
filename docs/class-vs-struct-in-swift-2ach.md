# Swift 中的类与结构

> 原文：<https://dev.to/burakakyalcin/class-vs-struct-in-swift-2ach>

当我们需要决定如何存储数据和建模行为时，有两种选择:类和结构。在这篇文章中，我将解释在不同的情况下应该首选哪种类型。

##### 我们来说说他们能提供的共同点。

*   它们都定义属性来存储值。如果我们想创建一个具有某些属性和行为的对象，我们可以同时使用它们。
*   它们都定义了下标，所以我们可以通过下标语法得到它们的值。
*   类和结构有初始化器来创建带有初始值的实例。
*   遵守协议也是常见的。有些协议可能只有类，因此只有类可以符合该协议，否则两者都可以符合它。
*   最后，它们可以被扩展。

##### 类还拥有一些结构所没有的额外能力。

*   他们可以继承另一个阶级的特征。
*   类可以通过取消初始化来释放分配的资源。

##### 那么，区别呢？

这两者的主要区别是类是引用类型，结构是值类型对象。好吧，但这意味着什么？

#### 什么是引用类型？

当我们初始化一个对象时，RAM 为它分配内存空间和地址。然后，将它的内存地址分配给我们创建的对象。

让我们看一个例子。

我们有一个只有名字属性的动物类。然后，我们创建一个狗对象，创建猫对象，但将狗分配给猫对象。

既然是引用类型对象，都是指向同一个内存地址，所以其实是同一个对象！如果我们改变其中一个属性，另一个也会受到影响，因为它们指向相同的地址。下面我们来看看。

```
class Animal {
   var name: String
   init(name: String) {
     self.name = name
   }
}
var dog = Animal(name: “dog”)
var cat = dog

print(dog.name) // prints "dog"
print(cat.name) // prints "dog"

dog.name = “hound”

print(cat.name) // prints "hound" 
```

来自 [Swift 官方文档](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)的注释

> 与值类型不同，引用类型在被赋给变量或常数时，或者被传递给函数时，不会被复制。使用对同一现有实例的引用，而不是副本。

#### 什么是值类型？

来自 [Swift 官方文档](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)的另一条注释

> 值类型是这样一种类型，当它的值被赋给一个变量或常数时，或者当它被传递给一个函数时，它的值被复制。

句号。就这么简单。

让我们看看上面稍加修改的例子。

```
struct Animal {
   var name: String
   init(name: String) {
      self.name = name
   }
}
var dog = Animal(name: “dog”)
var cat = dog

print(dog.name) // prints "dog"
print(cat.name) // prints "dog"

dog.name = “hound”

print(cat.name) // prints "dog" 
```

## 什么时候使用类？

当我们需要 Objective-C 的互操作性时，我们需要使用类。如果我们使用从我们这边接收数据的 Objective-C API，这些数据必须是一个类，因为 Objective-C 没有结构。

类的另一个用例是当我们需要控制身份时。如果我们通过应用程序需要一个对象的实例，并且我们想要控制它的身份，那么类就是解决方案。

## 什么时候使用 structs？

几乎随时。默认情况下，我们必须使用结构来表示常见的数据类型。Swift 中的结构功能强大，有很多特性。它们有存储属性、计算属性和方法。此外，他们可以遵守协议并获得他们的行为。Foundation 和 Swift 标准库中的许多类型都是结构；例如字符串、数组、数字和字典。

结构有助于跟踪部分代码，因为它们是值类型。我们只需要关注使用 struct 的区域，因为没有任何对象指向 struct 的地址并操纵它。否则，我们需要寻找可能改变对象值的地方。

除了类，当我们不能控制身份时，我们应该使用结构。想想从服务器获取数据，可能有相同的对象，但由于我们不控制身份，我们最好使用结构。

参考资料:

[https://developer . apple . com/documentation/swift/choosing _ between _ structures _ and _ classes](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)

[https://docs . swift . org/swift-book/language guide/classesandstructures . html](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)

[https://fluffy.es/reference-vs-value-type/](https://fluffy.es/reference-vs-value-type/)