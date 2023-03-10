# 在现有代码库中使用 SwiftUI

> 原文：<https://dev.to/peterwitham/using-swiftui-in-an-existing-code-base-2npe>

最初发布于[peterwitham.com](https://peterwitham.com)

当谈到苹果平台上的布局框架时，SwiftUI 可能是城里的新手。但是，这并不意味着你需要马上全投入进去。您可以在现有的 Objective-C 和 Swift 代码库中使用新视图，反之亦然，将您的旧视图添加到新的基于 SwiftUI 的应用程序中。

这使得开发人员可以开始使用新技术，而不必完全重新构建/重写代码库。这是很重要的一点，很少有公司或团队能够在没有大量时间投入或雇佣另一个团队与现有团队一起工作的情况下重新开始。原因很简单，当前的生产应用程序仍然需要处理和维护。

如果你是一个独立的开发者，那么你当然有一个更好的机会去切换，但是这通常会导致预期的问题。

SwiftUI 与现有代码库一起使用的能力归功于一种叫做 *HostingController* 的东西，可以把它看作是在应用程序中包含视图的一种方式。

如果在 Xcode 11 中使用 SwiftUI 创建一个新的应用程序，可以看到 HostingController 在运行。看一看*场景中的*。

为了利用 HostingController，您只需要向它提供一个参数，即您想要显示的视图的名称。例如，

```
UIHostingController(rootView: ContentView()) 
```

Enter fullscreen mode Exit fullscreen mode

苹果的每个平台都有自己版本的控制器。

macOS 使用*NSHostingController*
iOS 使用*UIHostingController*T5】watch OS 使用 *WKHostingController*

值得注意的是，在 watchOS 上，你必须子类化 *WKHostingController* 。

如果您想做相反的事情，在基于 SwiftUI 的应用程序中托管现有视图，那么您可以利用另一项新技术，即*可表示协议*。同样，每个平台上有不同的方式，但这是次要的。

macOS 使用*nsviewerepresentable*
iOS 使用*uiviewerepresentable*T5】watch OS 使用 *WKInterfaceObject*

不限于视图，还可以通过使用*UIViewControllerRepresentable*或*NSViewControllerRepresentable*来使用现有的 ViewControllers。

有趣的是，这并不局限于 Swift，如果你有一个 Objective-C 代码库，你可以通过使用 *@objc* 在 Swift 中包装 HostingController 来集成它们。

## 裹

所以说吧，没有什么理由不至少看一下新的 SwiftUI，看看它是否是你前进的一个选择。