# Swift 最小编码规则

> 原文：<https://dev.to/noahkuwae/swift-minimal-coding-rules-8a4>

# 这是给谁的？

*   入门级开发人员(或与他们一起工作的人)
*   希望快速编码，但保持最低的代码质量
*   已经引入 SwiftFormat 和 SwiftLint

您可以找到各种各样的编码规则，但是其中许多都很长，可能会让人不知所措。这条规则简单到足以快速开始，即使对于初学者也很容易理解。

# 规则

## 使用易懂的名称

命名是代码中最重要的一点。不要急着想名字。

## 从视图控制器中分离类

不要把与视图无关的代码放到视图控制器中。
不要将与 app 状态无关的代码放入 AppDelegate。
符合单一责任原则。

## 分离故事板和视图控制器

在团队发展中，你应该避免冲突。

## 将通用代码放入协议中

不要重复自己。
采用面向协议编程。
[https://dev . to/noahkuwae/protocol-oriented-programming-pop-brief-summary-4i5b](https://dev.to/noahkuwae/protocol-oriented-programming-pop-brief-summary-4i5b)

## 将常用 UI 部件放入 xib

还是那句话，不要重复。

## 符合可可 MVC

这应该由每个团队来决定，但是 MVC 是最基本的，也是最容易理解的架构。

## 通过扩展为每个协议分隔类(或结构)

这增加了代码的可读性。

之前:

```
class SampleViewController: UIViewController, UITableViewDelegate, UITableViewDataSource {
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell { ... }
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int { ... }
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) { ... }
...
} 
```

Enter fullscreen mode Exit fullscreen mode

之后:

```
class SampleViewController: UIViewController { ... }

extension SampleViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell { ... }
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int { ... }
}

extension SampleViewController: UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) { ... }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理错误

如果你现在不处理它们，添加一些类似`// TODO`的注释。

## 对意外情况使用 assert 或 fatalError

如果你仅仅使用`return`，你将很难在以后修复它。

## 将你的代码拆分成方法

如果你想为一段代码写一个注释，这是创建一个方法的标志。

# 引用

[编写质量代码的 15 条规则| | InformIT](http://www.informit.com/articles/article.aspx?p=2223710)

# 注

这可能会发生变化，因为目前我正在使用这一规则运行一个项目(在 2019 年)。项目期间可能会有一些更新。