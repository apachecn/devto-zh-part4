# 快速关闭:揭开@escaping 和@ autoclosure 属性的神秘面纱

> 原文：<https://dev.to/chicio/swift-closure-demystifying-escaping-and-autoclosure-attributes-34c>

在这篇文章中，我将讨论快速结束以及@escaping 和@ autoclosure 属性的潜力。

正如 Swift 官方文档中所报道的，以及我们在我之前的一篇[帖子](https://www.fabrizioduroni.it/2017/06/02/swift-closure-syntax.html)中看到的，闭包是:

> 可以在代码中传递和使用的自包含功能块。它们可以从定义它们的上下文中捕获和存储对任何常量和变量的引用。

在这篇文章中，我将向你展示两个有趣的闭包特性:`@autoclosure`和`@escaping`。

`@escaping`闭包作为参数传递给函数，但不在函数内部执行。所以，基本上闭包是在函数返回后执行的。经典的例子是存储在函数外部变量中的闭包。

`@autoclosure`属性可以应用于函数的闭包参数，并根据您传递的表达式自动创建一个闭包。
这两种属性结合起来潜力巨大。让我们看一个例子，使用闭包和这两个属性可以避免多个 if/switch。掌握了这两个属性之后，您就可以开始“滥用”闭包，并在任何地方使用它们了！！😜(也许还是保持冷静比较好，即使看到这个属性也不要滥用闭包😌).

[![swift closure everywhere](img/a565713d64e5034c2157d2ac968f9954.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1E3cZa2U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.fabrizioduroni.it/asseimg/posts/closure-everywhere.jpg)

例如，我们可以有一个`UITableView`,我们希望对每个显示的单元格执行不同的操作。
如果我们不使用闭包和属性`@autoclosure`和`@escaping`，我们需要使用位置来区分单元格，或者最终转换一些用于表示单元格数据的类的专门化。
假设每个单元格显示一个`Operation`类的实例，定义如下:

```
class Operation {
    let name: String
    let action: () -> ()

    init(name: String, action: @autoclosure @escaping () -> ()) {
        self.name = name
        self.action = action
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，基本上，在构造函数中，我们期待一些将包含在闭包中的东西，这要归功于`@autoclosure`属性，
并且我们将它存储为类的实例变量。我们可以存储它，因为我们也在使用`@escaping`属性。
现在，在我们的控制器中，我们可以定义一个操作数组，该数组将成为我们的`UITableViewController`的数据源。
我们可以在每个`Operation`实例的构造函数中传递与我们要执行的操作相对应的函数。
通过访问数据源数组中的相应元素，该功能将在表视图委托方法`public func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath)`中执行，无需识别所选的确切单元格类型。
在这里您可以找到完整的`OperationsViewController` :

```
class OperationsViewController: UITableViewController {
    var operations: [Operation] = []

    override func viewDidLoad() {
        super.viewDidLoad()
        self.operations.append(Operation(name: "Operation 1", action: self.showOrangeDetail()))
        self.operations.append(Operation(name: "Operation 2", action: self.showGreenDetail()))
    }

    //MARK: TableView Datasource

    public override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.operations.count
    }

    public override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell: UITableViewCell = tableView.dequeueReusableCell(withIdentifier: "OperationCell")!
        cell.textLabel?.text = self.operations[indexPath.row].name
        return cell
    }

    //MARK: TableView Delegate

    public override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        self.operations[indexPath.row].action()
    }

    //MARK: Actions

    private func showOrangeDetail() {
        self.performSegue(withIdentifier: "OrangeSegue", sender: nil)
    }

    private func showGreenDetail() {
        self.performSegue(withIdentifier: "GreenSegue", sender: nil)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里下载完整的例子。

所以基本上:没有如果，没有开关，只爱❤️对于`@autoclosure`和`@escaping`😍。

*原载于[https://www . fabrizioduroni . it](https://www.fabrizioduroni.it/2017/06/14/swift-closure-demystifying-autoclosure-escaping.html)2017 年 6 月 14 日。*