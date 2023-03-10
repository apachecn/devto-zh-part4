# JavaScript:所以类是一个函数？

> 原文：<https://dev.to/azrulaziz/javascript-so-class-is-a-function-102d>

我用 React 写了很多类组件。但是我从来没有真正理解过阶级。在这篇短文中，我将从根本上探索什么是阶级。

让我们继续看一种声明类的方法:

```
class User {} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们使用一个类声明来声明一个名为 User 的类。那么什么是阶级呢？是函数吗？还是一个物体？让我们测试:

```
typeof(User) // "function" 
```

Enter fullscreen mode Exit fullscreen mode

嗯**类其实就是一个函数！**
如果它是一个函数，让我们试着调用它，看看会发生什么。

```
User() //TypeError: class constructors must be invoked with |new| 
```

Enter fullscreen mode Exit fullscreen mode

埃罗尔！发生了什么事？最初我们看到 class 是一个函数，但是当我们试图像普通函数一样执行它时，它抛出一个错误，说*类构造函数必须用|new|* 调用。

#### 类是“特殊函数”

根据 MDN，类是“特殊函数”。我去寻找更多的信息，了解到**类实际上被用作构造函数**。构造函数是用来构造对象的函数。这就是为什么控制台抛出需要*新*关键字的错误。为了调用一个构造函数，我们必须使用 *new* 关键字。

让我们看一个例子:

```
const lily = new User()

typeof(lily) // "object"

console.log(lily) // Object {} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 new 关键字调用该类，并将其赋给一个名为 *lily* 的变量。当我们评估*百合*的类型时，我们得到的结果是 object。记录该值的结果也返回一个对象。如上所述，构造函数生成一个对象。

我在这里只是触及了表面。在以后的文章中，我将进一步探讨类内部的内容，以及当我们可以使用普通函数作为构造函数来构造对象时，为什么要使用类。