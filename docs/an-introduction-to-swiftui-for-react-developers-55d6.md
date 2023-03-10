# React 开发人员 SwiftUI 简介

> 原文：<https://dev.to/bmcmahen/an-introduction-to-swiftui-for-react-developers-55d6>

如果您已经使用 React 有一段时间了，并且快速浏览了一下 Swift UI，您可能会立即发现其中的相似之处。两者都是声明式 UI 框架，强调状态和用于生成视图的模板之间的分离。两者都强调反应性——更新你的状态来更新你的观点。两者都强调创建组件，这些组件可以组合在一起创建可重用的复杂视图。

老实说，学习 SwiftUI 的最大障碍可能是学习 Swift 本身和使用类型化语言。对类型化语言(包括 Typescript)有一些经验是有用的。

在本文中，我将从 React 和 Javascript 开发人员的角度介绍一些非常基本的 SwiftUI 设计模式。您可以在[react-meets-swift ui](https://github.com/bmcmahen/react-meets-swiftui)github 存储库中找到更多这些模式。

#### Swift UI 中的使用状态

让我们来看一个非常简单的 SwiftUI 组件。

```
struct Counter : View {
    @State var counter = 0

    func increment () {
        counter += 1
    }

    var body: some View {
        VStack {
            Text("Count: \(counter)")
            Button(action: increment) {
                Text("Increment")
            }
        }
    }
} 
```

这看起来非常类似于创建一个 React 组件类。我们正在定义一个名为`Counter`的[结构](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html)，它是一个包含属性和函数的类结构。`View`描述了我们的`Counter`必须遵循的一个[协议](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)——也就是说，它必须包含一个返回某个`View`的`body`属性。如果我们从结构中移除`body`属性，编译器会抱怨我们没有遵守`View`协议。

这个`body`属性类似于 React 中的`render`函数。它的内容与 JSX 相似。在本例中，我们在计数器中构建了 4 个视图(VStack、Text、Button 和 Text ),以创建一个包含一个文本标签和一个增加值的按钮的垂直堆栈。

属性定义了我们的本地组件状态。`@State`定义了一个[属性包装器](https://mecid.github.io/2019/06/12/understanding-property-wrappers-in-swiftui/)，这是一个新的 Swift 语言特性。它确保了我们的属性是反应性的。我们定义它的默认值为`0`，它的类型为`Int`是自动推断出来的。为了被动地更新值，我们可以简单地改变`counter`属性，我们的视图将相应地重新呈现。这相当于在 React 中运行`setState`。

#### 将道具传递给组件

传递属性就像在函数中定义参数一样简单。让我们更仔细地看看我们的`Counter`视图的`body`属性。

```
var body: some View {
    VStack {
        Text("Count: \(counter)")
        Button(action: increment) {
            Text("Increment")
        }
    }
} 
```

注意，`Text`和`Button`都是接受参数的视图结构。这些参数相当于 React 的道具。在这种情况下，`Text`接受一个`String`，而我们的按钮接受一个当触摸发生时被调用的功能。

但是包含在`VStack`和`Button`之后的括号中的值呢？这是一个[尾随闭包](https://www.hackingwithswift.com/example-code/language/what-is-trailing-closure-syntax)，也是 Swift 的一个相对较新的特性。基本上，结尾闭包语法是一块语法糖，它允许你在函数声明之后写函数的最后一个参数(当它是一个闭包时)。

而不是:

```
Button(action: increment, content: () -> Text("Increment")) 
```

我们可以写:

```
 Button(action: increment) {
    Text("Increment")
} 
```

为了理解如何创建接受道具的自定义视图，让我们创建另一个视图来帮助我们呈现柜台。它应该从父组件接受一个`count`属性。

```
struct CountDisplay : View {
    var count: Int
    var body: some View {
        HStack {
          Text("Your count total:")
          Text("\(count)")
        }
    }
} 
```

让我们将`count`道具从父视图传递给我们的`CountDisplay`。

```
var body: some View {
    VStack {
        CountDisplay(count: counter) Button(action: increment) {
            Text("Increment")
        }
    }
} 
```

类似于反应过来，不是吗？

#### 使用绑定更新子视图中的父状态

React 中的一个常见模式是将`onChange`回调函数传递给子组件，这样父组件就可以更新和更改其本地状态，然后再传播给子组件。您可以在 SwiftUI 中做同样的事情，但是 SwiftUI 做得更好—它允许子组件更新父状态。

让我们重构我们的`Counter`例子，这样我们的`Button`就出现在一个子组件中。

```
struct IncrementButton : View {
    @Binding var counter: Int
    func increment () {
        counter += 1
    }

    var body: some View {
      Button(action: increment) {
          Text("Increment")
      }
    }
} 
```

所以现在我们的孩子`IncrementButton`接受了一个`counter`道具，它实际上可以自己更新那个道具。没有`onChange`回拨的必要！它会更新它所来自的父组件中的属性。相当酷！我们使用特殊的`@Binding`属性装饰器来确保`bound`变量作为道具传递给我们的组件。

我们如何传递一个绑定变量？让我们重做我们的父组件。

```
struct ContentView : View {
    @State var count = 0

    var body: some View {
        VStack(spacing: 1.0) {
            Text("Press the button below")
            IncrementButton(counter: $count) }
    }
} 
```

我们只是在要传递的状态变量前加上一个`$`符号。

#### swift ui 中的其他 React 设计模式

显然，这只是在 SwiftUI 中实现 React 设计模式的开始。但在接下来的几个月里，我会探索更多的模式，并记录我的观察。

**您可以在 [react-meets-swiftui](https://github.com/bmcmahen/react-meets-swiftui) 存储库中查看其他 React 模式。**如果您是 React 开发人员，并且想学习 SwiftUI 的基础知识，这是一个很好的起点。

(这是一篇发表在我 benmcmahen.com 博客上的文章。点击这里[可以在线阅读。)](https://www.benmcmahen.com/swiftui-for-react-developers/)