# 依赖注入让我担心

> 原文：<https://dev.to/jessekphillips/dependency-injection-has-me-concerned-3cp8>

我是依赖注入的忠实粉丝，特别是穷人的版本。然而，我非常担心这种模式在使用框架时会被严重滥用。我不是唯一一个看到这个的人[不要责怪依赖注入](https://www.continuousimprover.com/2018/05/dont-blame-dependency-injection.html)想要强调的是，甚至不是框架有问题。当你读到[的缺点](https://softwareengineering.stackexchange.com/questions/371722/criticism-and-disadvantages-of-dependency-injection)时，也有类似的感觉，“不要问你是否应该使用依赖注入，问你正在试图解决什么问题。”

未指定的依赖:这可能是对 DI 框架最糟糕的冒犯。您可以将一个函数传递给 DI 容器，该容器包含该函数所需的接口的内容和构造方式。这很好，因为您可以在单个参数中传递所有参数，如果您需要添加新的依赖项，那么 API 保持不变。这也是有害的，因为大多数 DI 容器在编译时没有被检查，这意味着您的用户没有被告知调用您的函数所需的 API 变化。函数签名不再是自文档或编译器检查。

与 DI 框架的紧密耦合:如果你使用一个框架，甚至是库，很难没有某种形式的耦合。他们甚至有框架来帮助解决这个问题。DI 的另一种方法是构造函数注入。这是所有类在构造函数中定义依赖关系的地方，框架构建树。在这里，你的整个依赖图是由框架定义的，如果你的依赖很深，你将在每种情况下放置 DI，没有框架几乎不可能调用一个函数。

全局状态:有很多关于避免全局状态以及如何避免全局状态的文章。这是 Singleton 被视为反模式的原因之一。对于依赖注入容器，情况有所不同。虽然框架可以用来构建单例，但这不太可能发生。相反，假设使用一个全局容器，每个接口只留下一个具体类型。这不是我如何看待结构良好的代码。

我想我理解那些反对垃圾收集的人，他们说最好手动处理你的内存，多一点。我想对依赖关系说同样的话，弄清楚完成操作需要什么。但是我开始使用 GC 语言，而不是 DI 框架。