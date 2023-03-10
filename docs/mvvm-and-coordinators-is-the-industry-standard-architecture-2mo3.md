# MVVM 和协调员是行业标准架构

> 原文：<https://dev.to/tom0pearson/mvvm-and-coordinators-is-the-industry-standard-architecture-2mo3>

当我最近申请 iOS 开发人员的工作时，面试的第一阶段就接到了很多电话。在某个时候，我们总是会开始讨论我使用什么样的架构模式或者对什么样的架构模式感到舒服。每次我都会说，我和协调员一起使用 MVVM，更多的时候，它似乎像是从我嘴里喷出的神奇的话语——我通常会听到“哦，我们也使用它”,这无疑让我占了上风。这是在伦敦，但如果这也适用于大多数其他国家/科技中心，我不会感到惊讶。

所以看起来公司已经广泛采用了它，而不是 MVC/MVP/VIPER 或其他什么。我对原因的思考:

*   与 MVC 紧密相关，便于整个团队和新人采用
*   允许反应式编程(RxSwift/Combine)很容易地插入，因为有指定的地方应该发生绑定，并且反应式编程似乎将成为未来
*   为放置某些代码提供指导，这比 MVC 更有助于组织事物，但比 VIPER 更灵活、更实用

### 协调人长什么样

我将只描述协调器，因为我认为视图模型在别处[被广泛覆盖](https://www.swiftbysundell.com/posts/different-flavors-of-view-models-in-swift)。

对于什么是或者不是协调者，没有固定的规则。我得到了如下适合我大部分需求的东西

```
protocol Coordinator {
    var parentViewController: UIViewController { get }
    func start()
} 
```

它相当简单，但是封装了一个协调器需要的所有东西，一些表示将要创建的新视图控制器的东西，以及一个启动所有东西的函数。

然后，我们将为每个不同的工作流创建一个新的协调器。例如，一个登录协调器看起来类似于:

```
class LoginCoordinator: Coordinator {
    let parentViewController: UIViewController

    init(parentViewController: UIViewController) {
        self.parentViewController = parentViewController
    }

    func start() {
        let loginViewController = LoginViewController()
        parentViewController.present(loginViewController, animated: true)
    }
} 
```

这是一个简单的例子。如果您希望将协调者限制为仅由其他协调者呈现，那么他们可以更进一步，这样他们就不知道哪个 ViewController 是哪个协调者的根。在这种情况下，您将创建一个“主”协调器，并且协调器协议将有一个子协调器数组，以便它们保存对它们正在呈现的协调器的引用。

随着工作流的需求变得越来越复杂，协调者很容易变得臃肿。这个例子也没有使用视图模型，但是如果我们使用了一个，协调者将负责实例化它并把它传递给视图控制器，所以我相信你能理解事情会很快失控。

### 其他技能需求

*   TDD，但也只是一般的测试
*   反应式编程- RxSwift/Combine

### 进一步阅读

[查看模型](https://www.swiftbysundell.com/posts/different-flavors-of-view-models-in-swift)

[https://www.objc.io/books/app-architecture/](https://www.objc.io/books/app-architecture/)——《MVVM+协调员》是一本关于不同建筑模式的好书