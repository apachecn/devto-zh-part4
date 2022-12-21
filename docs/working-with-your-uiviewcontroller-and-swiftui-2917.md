# 使用 UIViewController 和 SwiftUI

> 原文：<https://dev.to/kevinmaarek/working-with-your-uiviewcontroller-and-swiftui-2917>

所以你开始玩 SwiftUI，你乐在其中？嗯，我也是！它一出来，我就开始想象我会喜欢用这个框架实现我的观点的所有用例。

如果你想知道如何使用新的 SwiftUI 视图和旧的 UIViewController，这是给你的！

为了配合你的 ViewControllers，苹果增加了这个神奇的协议:`UIViewControllerRepresentable`。
工作原理？
好吧，你有两个选择(目前) :

### **让你的 UIViewController 可表示。**

符合`UIViewControllerRepresentable`就好。这样做，您必须使您的 UIViewController `final`，防止您的类被继承或被覆盖。
这是它的样子:

```
extension ViewController: UIViewControllerRepresentable {
    public typealias UIViewControllerType = ViewController

    public func makeUIViewController(context: UIViewControllerRepresentableContext<ViewController>) -> ViewController {
        return ViewController()
    }

    public func updateUIViewController(_ uiViewController: ViewController, context: UIViewControllerRepresentableContext<ViewController>) {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### **使用包装纸。**

另一种选择是制作一种 ViewControllerWrapper。这是我选择采用的方法，因为它不要求类是`final`。
创建一个符合`UIViewControllerRepresentable`的 Struct，在`makeUIViewController`方法:
中返回一个初始化的 ViewController

```
struct ViewControllerWrapper: UIViewControllerRepresentable {

    typealias UIViewControllerType = ViewController

    func makeUIViewController(context: UIViewControllerRepresentableContext<ViewControllerWrapper>) -> ViewControllerWrapper.UIViewControllerType {
        return ViewController()
    }

    func updateUIViewController(_ uiViewController: ViewControllerWrapper.UIViewControllerType, context: UIViewControllerRepresentableContext<ViewControllerWrapper>) {
        //
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 SwiftUI 视图中简单地调用它，就像这样:

```
struct MyView : View {
    var body: some View {
        ViewControllerWrapper()
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用“包装器”解决方案可以轻松地添加任何参数来初始化 ViewController。

你在这里。希望这能有所帮助！

快乐编码:)