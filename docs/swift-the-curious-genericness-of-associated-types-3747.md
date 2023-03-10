# swift——关联类型的奇特通用性

> 原文：<https://dev.to/shawonashraf/swift-the-curious-genericness-of-associated-types-3747>

### 在我们深挖之前，协议！

Swift 引入了这一新范式，称为 POP 或`Protocol Oriented Programming`。对于不熟悉 Swift 的人，尤其是那些来自 Java 或 C#等面向对象语言的人，协议可能看起来有点像接口。至少从快看上去是这样。说到实现，协议实际上是一种与接口完全不同的动物。(*更不用说协议带来的额外好处了。)*

假设您希望有一个协议，其中包含一个将两个数相加并返回总和的方法。现在，让我们只考虑添加`Int`类型的数字。

```
protocol Adder {
    func add(a: Int, b: Int) -> Int
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要为这个方法添加一个实现，对吗？那么我们该怎么做呢？我们为这个协议创建一个`class`到`conforms`。

```
class IntAdder: Adder {
    func add(a: Int, b: Int) -> Int {
        return a + b
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看这是否有效！

```
let adder = IntAdder()
print(adder.add(a: 55, b: 45)) // prints 100 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们不只是想添加`Int`，而是添加`Float`和`Double`类型的数字呢？等一下，我知道你会喊泛型。**协议还有另一种方式。我们可以用`Associated Types.`**

### 追逐兔子.....什么是关联类型？

我们希望我们的协议方法是通用的，对吗？这意味着我们必须告诉它接受一些泛型类型。但这可能吗？我们定义的协议绝不是一个通用的协议。我的意思是，没有特殊的签名，它是通用的！结果却恰恰相反。您可以在协议中定义一个泛型类型。关联类型只是让你这样做。让我们看一个例子。

```
protocol Adder {
    associatedtype Number
    func add(a: Number, b: Number) -> Number
} 
```

Enter fullscreen mode Exit fullscreen mode

注意区别，我们凭空创建了一个类型，现在我们的函数可以处理它了！

一个`associatedtype`所做的是它实际上告诉编译器，听着亲爱的编译器，我的函数是一群困惑的人，他们不知道他们应该让谁进来，所以暂时原谅他们，我为这些即将进来的人添加一个类型名或占位符，以便你以后可以标记他们。

换句话说，`associatedtype`给你想象中的类型一个名字/ id，这样它就不会在人群中走失。

### 有点正式的定义

如果你想以一种正式的方式来定义 and `associatedtype`，就像苹果公司所做的那样(我刚刚从苹果图书应用程序中分享了这一点)

> 关联类型为用作协议一部分的类型提供占位符名称。在采用该协议之前，不会指定用于关联类型的实际类型。关联类型是用 associatedtype 关键字指定的。

*摘自*
*【Swift 编程语言(Swift 4.2)*
*苹果公司*
*[https://books . Apple . com/us/book/The-Swift-Programming-Language-Swift-5-0/id 881256329](https://books.apple.com/us/book/the-swift-programming-language-swift-5-0/id881256329)*
*本材料可能受版权保护。*

### 回到手头的事情

让我们为协议添加一个实现。这一次，我们来添加`Double`型数字。在我们这样做之前，我们要问这个非常重要的问题- *我们如何告诉我们的通用协议方法我们将要传递什么类型？*

### 键入别名来救援！

我们只需要在符合协议的类中为`associatedtype`添加一个`typealias`。现在我们该怎么做呢？只需将您希望函数接受的类型指定为`typealias`。好的，让我们再看一些代码。

```
class DoubleAdder: Adder {
    typealias Number = Double
} 
```

Enter fullscreen mode Exit fullscreen mode

既然已经讨论过了，让我们继续讨论函数本身。

```
func add(a: Double, b: Double) -> Double {
    return a + b
} 
```

Enter fullscreen mode Exit fullscreen mode

完整的`DoubleAdder`现在看起来像这样。

```
class DoubleAdder: Adder {
    typealias Number = Double

    func add(a: Double, b: Double) -> Double {
        return a + b
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

再次测试，实现通过。

```
let adder = DoubleAdder()
print(adder.add(a: 34.67, b: 89.23)) // 123.9 
```

Enter fullscreen mode Exit fullscreen mode

### 所以接口诶，协议可以通用诶？

原来是这样！具有关联类型的协议可以用作泛型类型。但是您应该使用它们而不是经过测试和验证的现有泛型类型吗？我相信这将需要一些严肃的讨论，我不应该再发这个帖子了。让我们把它放在一个单独的位置，好吗？太好了！到那时再见，好好打扮自己！