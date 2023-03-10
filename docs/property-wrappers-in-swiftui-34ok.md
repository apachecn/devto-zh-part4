# SwiftUI 中的属性包装器

> 原文：<https://dev.to/kevinmaarek/property-wrappers-in-swiftui-34ok>

在 Swift 5 和 SwiftUI 中，苹果引入了属性包装器。你可能在一些 SwiftUI 文章或教程上见过其中一个:`@State`、`@BindableObject`、`@EnvironmentObject`。
在本帖中，我们将尝试解释这些包装器是什么以及何时使用它们。

SwiftUI 中的一切都是基于结构的。和结构是不可变的，所以它们是固定值。
当一个属性有一个`@State`属性包装器时，它只是告诉你的结构，内存管理不会由结构本身来处理，而是由另一个内存管理器实体来处理:SwiftUI 框架。
让我们描绘一个实际的用例:

```
struct ContentView : View {
    var buttonTapped: Bool = false
    var body: some View {
        Button(action: {
            self.buttonTapped.toggle()
        }) {
            Text("Tap the button")
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们希望 buttonTapped 值在按钮的点击上切换。Xcode 不让我这么做，它说:

> 不能对不可变值使用可变成员:“self”是不可变的

当然，我们在一个结构体中！
这就是你想要使用`@State`包装器的地方。属性的内存、创建和存储不会由结构本身管理。
它实际上做的是，它将被通知值已经改变，它将用新值重新创建结构，因此，视图将被无效，并用更新的值刷新。

值前的符号表示双向绑定。将$值传递给视图将使能够读取该值并更新它。可能对文本字段有用，例如，它将读取和写入您传递的值。就这样:

```
struct ContentView : View {
    @State var input: String = ""
    var body: some View {
        TextField($input)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于这些简单的情况来说,`@State`非常有用。当你的视图有简单的属性，如字符串，整型或布尔值。但是当你想使用更复杂的对象时怎么办呢？
假设您有这个注册表单，它有 3 个字段:电子邮件、用户名和密码。
第一件事是用这些字符串属性创建一个结构，对吗？

结构注册{
var email:String = " "
var username:String = " "
var password:String = " "
}

并用一个`@State`包装器将它存储在视图中。

```
struct ContentView : View {
    @State var registration: Registration = Registration()
    var body: some View {
        Text(“Hello”)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

实际上，只要您只在一个视图中使用这个结构，它就会工作得很好，但是让我们想象一下，您有一个视图，比如 InfoView，其中有 email 和 username 字段，而另一个视图是 PasswordView，其中有 password 字段。那就是你会被卡住的地方。两种视图都需要相同的注册模型。注册对象将被传递给这两个视图，但是记住，结构只有一个所有者，因为结构是作为值而不是引用工作的。注册是一个结构。你不能有一个像对象实例一样的结构实例。如果多个视图(或任何类型的对象)拥有一个结构，那么每个视图都有它自己的版本。

因此，为了使我们的注册表单完整，我们需要将该结构转换成一个类。所以我们可以用参考文献。把它传给我们需要的每一个视图。但是事情是这样的:使用`@State`包装器，视图(结构)用更新的值被重新创建，但是你不能用一个类来做。该类不会像@State 属性那样被“监听”。
该导入联合收割机了！
在[swift ui 入门和组合](https://dev.to/kevinmaarek/getting-started-with-swiftui-and-combine-dd8)的帖子中，我们发现了可绑定的协议。这是我们在这种情况下需要使用的。我们将使我们的注册类可绑定。

```
class Registration: Bindable {
    var email: String = ""
    var username: String = ""
    var password: String = ""
} 
```

Enter fullscreen mode Exit fullscreen mode

与任何可绑定对象一样，您将需要一个 didChange 属性，并在更新任何值时调用它。
我们不需要传递一个注册对象或者一个错误。我们只需要通知对象的值已经改变。

```
class Registration: Bindable {
    var didChange = PassthroughSubject<Void, Never>()
        var email: String = "" {
        didSet {
            didChange.send()
        }
    }
    // do the same for each property
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要告诉 SwiftUI 注意这些变化，就像@State 一样，通过重新创建视图来更新 UI。
所以只需将视图中的@State 替换为@ObjectBinding :

```
struct ContentView : View {
    @ObjectBinding var registration: Registration = Registration()
    var body: some View {
        Text(“Hello”)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们解决了 SwiftUI 监听复杂的引用类型对象来更新视图的问题。但是如何将这个对象传递给另一个视图，就像我们实际使用的两页注册表单一样？
好吧，苹果为通过视图传递对象做了一些很酷的事情:`Environment`。这是一种共享对象的方式，使它们在需要的地方可用。

它是这样工作的:
你只需用`@EnvironmentObject` :
添加你的注册对象

```
struct ContentView : View {
    @EnvironmentObject var registration: Registration
    var body: some View {
        Text(“Hello”)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您不需要初始化它，因为您的视图将假设它的值将在环境中可用。向环境中添加一个值非常简单:

```
ContentView.environmentObject(Registration()) 
```

Enter fullscreen mode Exit fullscreen mode

您可以在 sceneDelegate 或任何调用视图的地方这样做。每个使用注册对象的视图都需要用@EnvironmentObject 包装一个注册属性。

我希望这篇文章能帮助你理解 SwiftUI 的机制以及如何使用属性包装器。如果你有任何问题，不要犹豫，留下你的评论。

编码快乐！:)