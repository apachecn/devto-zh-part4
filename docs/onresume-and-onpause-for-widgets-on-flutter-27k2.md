# Flutter 中部件的 onResume()和 onPause()

> 原文：<https://dev.to/pedromassango/onresume-and-onpause-for-widgets-on-flutter-27k2>

大家好，今天我们来看看 Flutter 中的小部件状态。来自 Android 的我们熟悉像 onCreate、onResume 和 onPause 这样的应用程序状态(活动状态),让我们看看这些功能在 Flutter 中的等价物。

#### 让我们建立一个场景

我们正在构建一个银行应用程序，每次打开该应用程序时都应该请求用户的密码。在这种情况下，我们可以从思考应该将显示要求输入密码的对话框的代码放在哪里开始。

#### WidgetsBindingObserver

这个类让我们接收许多系统和应用程序更改的通知。例如，该类中几乎没有可用的函数:

*   **didPopRoute()** 在 Android 上，这是在用户按下返回按钮时调用的。
*   当系统通知应用程序用户改变了系统的语言设置时调用。
*   **didchangeapplifecyclsate(…)** 这个是系统把 app 放到后台/前台时调用的。

我们在使用它时有两个选择:**扩展，**获取所有应用/系统变化的默认行为，或者**实现**，在这里我们必须实现所有句柄。

### 设置它

现在我们知道了 WidgetsBindingObserver，让我们将它用于我们的用例:每次应用程序恢复时请求用户的密码。

1.  扩展**我的主页**状态类:

```
class _MyHomePageState extends State<MyHomePage> with WidgetsBindingObserver {...}
```

2.将观察者添加到当前 **WidgetsBinding** 实例中:

要接收变更通知，我们必须注册我们的观察者，我们还应该在 dispose()函数中取消注册观察者，以释放观察者保留的资源

```
@override
void initState() {
  super.initState();
  WidgetsBinding.*instance*.addObserver(this);
}
```

正在释放资源…

```
@override
void dispose() {
  WidgetsBinding.*instance*.removeObserver(this);
  super.dispose();
}
```

3.监听应用程序何时进入后台或前台状态。在这一步中，我们需要做的就是检查我们从系统接收到的状态，然后对这些变化做出反应。

```
@override
void didChangeAppLifecycleState(AppLifecycleState state) {
  if(state == AppLifecycleState.resumed){
    _showPasswordDialog();
  }
}
```

这样，每当用户离开我们的应用程序，然后又回来，showPasswordDialog()将被调用。

我们还可以检查用户是否正在离开我们的应用程序，例如:

```
@override
void didChangeAppLifecycleState(AppLifecycleState state) {
  if(state == AppLifecycleState.resumed){
    // user returned to our app
  }else if(state == AppLifecycleState.inactive){
    // app is inactive
  }else if(state == AppLifecycleState.paused){
    // user is about quit our app temporally
  }else if(state == AppLifecycleState.suspending){
    // app suspended (not used in iOS)
  }
}
```

就这样，我希望你喜欢这篇文章。查看源代码[这里](https://github.com/pedromassango/flutter_app_lifecycle)。