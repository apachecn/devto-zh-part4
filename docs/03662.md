# 颤振性能提示(#1):常数构造器

> 原文：<https://dev.to/pedromassango/flutter-performance-tips-1-const-constructors-4j41>

当我们使用`setState()` Flutter 调用 build 方法并在其中重建每一个部件树。避免这种情况的最好方法是使用 const 构造函数。

在构建自己的小部件或使用 Flutter 小部件时，尽可能使用 const 构造函数。这有助于 Flutter 只重建应该更新的小部件。

因此，如果你有一个`StatefulWidget`，并且你正在使用`setState((){})`来更新那个小部件，你有这样的小部件:

```
class _MyWidgetState extends State<MyWidget> {

  String title = "Title";

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(title),
      ),
      body: Column(
        children: <Widget>[
          const Text("Text 1"),
          const Padding(
            padding: const EdgeInsets.all(8.0),
            child: const Text("Another Text widget"),
          ),
          const Text("Text 3"),
        ],
      ),
      floatingActionButton: FloatingActionButton(
        child: const Icon(Icons.add),
        onPressed: () {
          setState(() => title = 'New Title');
        },
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果运行这段代码并按下浮动操作按钮，所有定义为 const 的小部件都不会被重新构建。