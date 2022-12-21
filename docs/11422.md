# SwiftUI 中的导航

> 原文：<https://dev.to/gualtierofr/navigation-in-swiftui-1hg2>

已编辑，删除了不推荐使用的代码

原帖[http://www.gfrigerio.com/navigation-in-swiftui/](http://www.gfrigerio.com/navigation-in-swiftui/)

早在 7 月份，我写了一篇关于 SwiftUI 中导航的文章，试图解释 NavigationLink 和 DynamicNavigationDestinationLink 之间的区别。后者已经被弃用，所以如果这篇文章现在没有用了，你仍然可以在这里找到它[http://www.gfrigerio.com/navigation-in-swiftui-deprecated/](http://www.gfrigerio.com/navigation-in-swiftui-deprecated/)我更新了 [GitHub](https://github.com/gualtierofrigerio/SwiftUINavigationExample) 上的库，删除了被弃用的代码，并提供了一些不同类型的导航链接的例子

## 导航链接

如果你跟踪了 2019 年 6 月制作的一些教程，你可能会看到它被称为 NavigationButton，但很快就被弃用了，现在它被称为 NavigationLink。它类似于一个按钮，但是允许你指定一个目的地，一旦按钮被按下，新的视图被推入堆栈。请注意，您必须是 NavigationView 的一部分才能使用 NavigationLink，您不需要在所有视图中都指定 NavigationView，但是如果您不是导航堆栈的一部分，则不能使用 NavigationLink。
让我们看一个例子

```
NavigationLink(destination: MyModal()) {
    Text("push view")
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，一旦你点击推送视图，一个新的视图就会被推送到屏幕上。当你需要响应屏幕上的点击并知道去哪里时，NavigationLink 非常有用，但有时你想以编程方式显示一个新视图，例如，你可能需要等待异步操作结束后再推送一个新视图，而你不能用“简单”的 NavigationLink 做到这一点。所有的例子都可以在这个[文件](https://github.com/gualtierofrigerio/SwiftUINavigationExample/blob/master/SwiftUINavigationExample/ContentView.swift)中看到

### 导航链接激活

您必须以编程方式推送新视图的可能性之一是使用 navigation link(destination:is active:)。

```
@State var showView = false
...
NavigationLink(destination: MyModal(), isActive: $showView) {
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，您可以通过将 showView 设置为 true 来打开视图 MyModal，例如在一个按钮操作中，或者当发生一些事情时，比如您获取了一些数据，现在已经准备好了。如您所见，有一个视图与链接相关联，所以我找到的唯一方法是传递一个空的视图生成器，所以屏幕上什么也没有。这是唯一的缺点，并且 DynamicNavigationDestinationLink 不要求创建空视图，所以我希望它们将来能提供一种以更优雅的方式以编程方式推送视图的方法。

### 带标签的导航链接

如果你有一些不同的视图，你需要以编程的方式推送，我不认为有一些 Bool 变量是一个很好的解决方案，因为你需要一个视图，你可能会得到一个重复的代码或者一系列的 If 语句。幸运的是，NavigationLink 的第三个实现提供了一个更好的解决方案，navigation link(destination:tag:selection:)。

```
@State var tag:Int? = nil
...
NavigationLink(destination: MyModal(), tag: 1, selection: $tag) {
}
...
Button(action: {
    self.tag = 1
}, label: {
    Text("show view tag 1")
}) 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们需要一个空视图，但是这次我们可以为选择指定一个标签和一个绑定变量。一旦变量标记被设置为 1，MyModal 就被推送到导航堆栈上。您可以使用 Int 或其他符合 Hashable 的类型，这样您就可以拥有一个不同视图的 enum，如果您想要更优雅和可读性更好的东西，可以将它用于标记。

### 工作表

如果您想呈现一个表而不是推动一个视图，您可以使用。视图定义末尾的工作表。

```
@State var showModal = false
...
var body: some View {
// your view here
}.sheet(isPresented: $showModal) {
    MyModal()
} 
```

Enter fullscreen mode Exit fullscreen mode

这类似于使用 isActive 的 NavigationLink，一旦将 showModal 设置为 true，就会显示工作表。一个工作表没有等价的标签，但是可以有多个包含不同变量的工作表。希望你喜欢我关于如何在 SwiftUI 中以编程方式推送视图的快速介绍，快乐编码:)