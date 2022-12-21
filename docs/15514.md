# 组合第一个例子

> 原文：<https://dev.to/gualtierofr/combine-first-example-3ie9>

原文在[http://www.gfrigerio.com/combine-first-example/](http://www.gfrigerio.com/combine-first-example/)

这是一篇关于 Combine 的帖子，它是苹果公司的玻璃钢新框架。有两个很棒的 WWDC 会议可以观看:

[介绍联合收割机](https://developer.apple.com/videos/play/wwdc2019/722/)
[实践中的联合收割机](https://developer.apple.com/videos/play/wwdc2019/721/)

这是官方文件

我要做的是实现 Combine，如实践中的 Combine 会话所示，根据一些 UITextFields 的值来启用或禁用按钮。我认为台上的人在解释框架方面做得很好，但是拥有一个包含实际代码的 GitHub 项目有助于理解他们的例子，因为他们展示了代码片段，而不是整个项目。这就是这篇文章的内容。

### 结合起来

首先让我简单介绍一下 Combine 和两个主要概念:发布者和订阅者。顾名思义，您有一个发布者，它发布或共享一段时间内的一系列值。订阅者订阅或接收来自发布者的值，并可以控制接收它们的方式。
一点背景知识:发布者是一个具有两种相关类型的协议:输出(String，Bool 等。)而这种失败，从来没有在我们的例子中出现过，或者出现过错误。它有一个 subscribe 函数，因此与两个关联类型匹配的订阅者可以订阅发布者。
订阅者是一个有两种关联类型的协议，输入(必须匹配发布者的输出)和失败。
发布者可以链接在一起，或者组合在一起产生另一个发布者，使用不同的类型(我们需要 Combine 调用的操作符，你会看到我们可以使用。我们将使用 AnyCancellable 来避免保留周期。如果你熟悉 RxSwift，那就是 DisposeBag 的概念。

### 样本项目

你可以在 GitHub [这里](https://github.com/gualtierofrigerio/CombineTest)找到示例代码

我们有 3 个 UITextField 连接到一个视图控制器，还有一个 UIButton，我们只希望在用户名有效(> 3 个字符)和密码匹配且有效(同样> 3 个字符)时启用它。

让我们假设我们想要在没有联合的情况下达到同样的目标。我们可以有一个函数来启用按钮，并且我们可以在每次 3 个 UITextField 之一发生变化时调用该函数。但是，如果我们需要调用 web 服务来确定用户名是否有效或存在，该怎么办呢？我们应该等待 web 服务响应，然后再次检查两个密码字段的值，并启用或禁用按钮。事情可能会变得复杂，每次 UITextField 之一发生变化时，您都需要手动调用一些东西。不是一个好主意…

好了，我们要使用联合收割机，让我们做吧！首先，我将向您展示 ViewController 的实现。我决定使用一个标准的 UIViewController 并避免 SwiftUI，这样我们就可以专注于组合并将其应用到我们熟悉的东西上。

```
private var viewModel = RegistrationViewModel()

@IBAction func usernameDidChange(_ sender: UITextField) {
    viewModel.username = sender.text ?? ""
}

@IBAction func passwordDidChange(_ sender: UITextField) {
    viewModel.password = sender.text ?? ""
}

@IBAction func passwordRepeatDidChange(_ sender: UITextField) {
    viewModel.passwordRepeat = sender.text ?? ""
} 
```

因此，正如您所看到的，每次 UITextField 发生变化时，我们都会更新视图模型中的变量。我更喜欢将组合逻辑放在不同的类中(实际上是一个结构),这样 ViewController 只处理 UI。我们并不是在每次文本字段改变时都直接调用函数来执行验证，Combine 会处理这个问题。

```
class RegistrationViewModel {

    @Published var username:String = ""
    @Published var password:String = ""
    @Published var passwordRepeat:String = ""

} 
```

等一下，@发表在关键字 var 之前的是什么？这是 Swift 5.1 中的新功能，它被称为属性包装器(又称属性委托)，允许编译器自动包装特定类型的属性。在我们的示例中,@Published 向属性添加了一个发布者，所以 username 有一个发布者。当您使用属性包装器时，可以通过使用$来访问新包装的变量，因此对于 username，您可以通过$username 来访问发布者。属性包装器在 SwiftUI 中被大量使用，我喜欢这个概念，所以我将在以后的文章中讨论它们。

好了，现在你知道@Published 是什么意思了，我们有 3 个发布者，每次通过我们之前看到的 IBAction 设置 username、password 和 passwordRepeat 时，每个发布者都会发布一个新值。让我们用更多的变量将它们合并在一起，从密码开始。我们希望检查两个字段是否相等，并且希望密码至少有 4 个字符。

```
var validPassword:AnyPublisher {
    return $password.combineLatest($passwordRepeat) { (password, passwordRepeat)  in
        var isValid = false
        isValid = (password == passwordRepeat) && password.count > 3
        return isValid
        }.eraseToAnyPublisher()
} 
```

什么是 AnyPublisher？这是一种可以取消的发布器(我们通过 eraseToAnyPublisher 获得这种行为)，正如我们之前所说，这是内存管理所必需的。我希望你熟悉泛型，这里我们说这个 AnyPublisher 将要发布一个 Bool，并产生一个 Never 类型的错误(这样它就不会失败)。正如我之前提到的，我们使用$password 来访问包装版本的 password，它有一个发布者。然后，我们使用 combineLatest 将这个发布者和第二个发布者组合在一起，在本例中是$passwordRepeat，因此是第二个密码字段。在闭包中，我们有两个字段的值，并返回一个 Bool。然后是修饰语。eraseToAnyPublisher 返回 AnyPublisher，而不是 Publisher。

现在让我们来看看用户名发布者。理论上，我们可以将它与 validPassword 结合使用，但是我想在实践中实现他们在组合会话中谈到的相同修饰符。

```
var validUsername:AnyPublisher {
    return $username
        .debounce(for: 0.2, scheduler: RunLoop.main)
        .removeDuplicates()
        .map{$0.count > 3 ? true : false}
        .eraseToAnyPublisher()
} 
```

去抖避免了在变化发生时发布它们，但是引入了延迟。这样，如果用户输入得非常快，我们就不会继续发布值并检查用户名是否有效。在这个例子中，检查非常简单，但是如果你需要调用 REST API，而你又不想每次输入一个新字符就调用它，那么你最好等一会儿。。消除重复交易，你猜怎么着，重复交易。如果您键入 username，username，然后再次键入 username，那么两次检查相同的值是没有意义的，因此发布者不会一次又一次地发布相同的值。
$username 是一个字符串类型的发布者，由于值是一个字符串，所以我们需要。map 将其转换为 Bool 作为我们想要公开的发布者。再一次。eraseToAnyPublisher 创建可取消的 AnyPublisher。

我们已经接近尾声了，但是让我们看看另一种将两家出版商合并在一起的方法。

```
var validCredential:AnyPublisher {
    return Publishers.CombineLatest(validUsername, validPassword) { (validUsername, validPassword) in
        return validUsername && validPassword
        }.eraseToAnyPublisher()
} 
```

CombineLatests 返回一个合并了两个发布者的值的新发布者，类似于我们之前看到的 password，但是我发现它更容易阅读，所以我更喜欢这个实现。如果你看视频的话，这是他们在舞台上谈论的一个。这一次我们不需要 map，因为两个发布者都发布了一个 Bool，我们需要一个 Bool 类型的 any 发布者。

我们差不多完成了，但是我们需要回到我们的视图控制器。ViewModel 公开 validCredential，ViewController 可以订阅它，因此每次发布新值时，不管是真还是假，都可以启用或禁用按钮。

```
private var registerButtonSubscriber:AnyCancellable?

override func viewDidLoad() {
    super.viewDidLoad()
    registerButtonSubscriber = viewModel.validCredential
        .receive(on: RunLoop.main)
        .assign(to: \.isEnabled, on: registerButton)

} 
```

让我从描述 AnyCancellable 类型的 registerButtonSubscriber var 开始。我们使用它来取消 deinit()上的订阅，并避免保留周期。现在，我确信你渴望的那一行代码，我们终于可以订阅 validCredential publisher，在主线程上接收它的更新(这在我们处理 UI 时很重要),并将发布的值分配给 registerButton 的 isEnabled 属性。

如果您尝试这个示例项目，您会看到每当您在 3 个 UITextFields 中的一个上键入内容时，这个按钮都会被激活和禁用。很酷，不是吗？
当然不止这些，我还没有谈过像 PassthroughSubject 这样的其他出版商，我认为在我过去写的关于 RxSwift 和 Promises 的项目中，Combine 会很棒，所以请继续关注更多！