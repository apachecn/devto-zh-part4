# Dart 中的异步编程

> 原文：<https://dev.to/rahmanfadhil/asynchronous-programming-in-dart-3485>

*阅读原文[此处](https://rahmanfadhil.com/asynchronous-dart)T3】*

颤振比以往任何时候都更受欢迎！但是在你开始学习框架之前，你需要先学习编程语言。我知道这很烦人，而且似乎是浪费时间。但是，这是每个开发人员必须面对的挑战。

Dart 是 Google 开发的一种功能强大的编程语言，旨在帮助开发人员为客户端和服务器构建 web 应用程序，现在又有了 Flutter 移动应用程序。就像 JavaScript 一样，Dart 是一种异步编程语言。这意味着您可以运行一段代码而不会阻塞任何其他任务，即使它运行在一个单独的线程中。

Dart 还使用称为事件循环的东西来使异步代码工作。实际上，您不必真的关心这一点，因为 Dart VM 为您做了一切，但至少你们了解它是如何工作的以及它为什么存在。

## 未来

Dart 中异步编码模式的基本实现之一是 Future。如果你已经熟悉 JavaScript，Future 和 Promise 有完全相同的概念。基本上，你有一段代码，当某个事件被触发时，你想运行它。

一旦你的函数得到了一个未来，你需要决定当未来还没有完成时你想做什么，当它完成时你想做什么，当它完成时有错误时你想做什么。

### 未来如何运作。

看看下面这段代码。

```
void fetchPost() {
  http.get('https://jsonplaceholder.typicode.com/posts/1')
    .then((response) {
      print(response);
    });
} 
```

在这个例子中，我们有一个函数使用`http`库从一个应用编程接口发出一个 http 请求。`http.get`方法返回未来。因此，我们使用`then`注册一个回调，当请求完成时将被触发。

当你运行这个函数时，事件循环得到它，它开始发出请求。在等待来自 API 的响应时，事件循环仍然在做它的工作。也许有其他事件发生，或者用户做了一些事情，你的未来仍然在等待一个数据或者一个错误。

当数据到达时，我们用`then`方法注册的回调被触发。现在，执行您的代码片段，并将响应打印到控制台。

### 处理错误

现在我们知道，我们可以用`then`的方法告诉未来，当数据成功到达时，我们要做什么。但是，如果我们的要求有问题怎么办，比如:连接中断，或者表单数据无效？

嗯，我们可以使用`catchError`方法告诉 Dart 当未来抛出错误时我们想做什么。

```
void fetchPost() {
  http.get('https://jsonplaceholder.typicode.com/posts/1')
    .then((response) {
      print(response);
    })
    .catchError((error) {
      print("Something went wrong: ${error.message}");
    });
} 
```

`catchError`回调得到一个错误作为第一个参数。所以，你知道到底哪里出了问题。

到目前为止一切顺利！现在我将向你展示如何创造你自己的未来。

### 创造我们自己的未来

很多时候，你不需要创造自己的未来。这是因为许多第三方库已经为你创造了未来。例如，网络库、接入设备、摄像头等等。但是，您仍然可以通过使用`Future`构造函数来构建自己的未来。

让我给你看看我在说什么。

```
import 'dart:async';

void main() {
  final myFuture = Future(() {
    print("Hello from the future!");
    return true;
  });

  print("Done!");
} 
```

如果您运行这段代码，您将看到“完成！”首先打印字符串。为什么？因为我们未来内部的代码是异步运行的。如果您已经有了未来的值，您可以使用`Future.value`方法返回一个具有给定值的未来值，如下例所示。有时候对测试很有帮助。

```
final myFuture = Future.value("Hello from the future!"); 
```

或者，如果你想返回一个错误，你可以使用`Future.error`方法。

```
final myFuture = Future.error(Exception()); 
```

### 异步&等待

大多数编程语言的语法中都有`async`和`await`。基本上，它们只是处理异步代码(如 Future 和 Streams)的另一种语法，使其看起来更清晰、更易读。让我给你举个例子。

```
void fetchPost() {
  http.get('https://jsonplaceholder.typicode.com/posts/1')
    .then((response) {
      print(response);
    })
} 
```

还记得这段代码吗？嗯，这个功能看起来还行。但实际上，如果我们想使用多个异步函数，这段代码看起来会更乱。

首先，我们需要给你要用的函数加上`async`关键字 await。如果不这样做，Dart 编译器会提示您一个语法错误。

```
void fetchPost() async {
  // ...
} 
```

然后，将关键字`await`添加到返回未来值的异步函数中，并将结果值存储在一个变量中，如下所示。

```
void fetchPost() async {
  final result = await http.get('https://jsonplaceholder.typicode.com/posts/1');
} 
```

最后，您可以在请求函数之后直接将结果值打印到控制台。

```
void fetchPost() async {
  final result = await http.get('https://jsonplaceholder.typicode.com/posts/1');
  print(result);
} 
```

唯一需要记住的是，你只能在`async`函数中使用`await`关键字。就是这样！

使用 async & await 和不使用它的最大区别是当我们想同时处理多个未来时

**使用异步& await:**

```
import 'dart:convert';
import 'package:http/http.dart';

void fetchUserPosts(token) async {
  // Fetch user data
  final userResponse = await http.get('https://yourapi.com/user?token=$token');
  final user = json.decode(userResponse.body);

  // Fetch posts data
  final postsResponse = await http.get("https://yourapi.com/posts?userId=${user['id']}");
  final userPosts = json.decode(postsResponse.body);

  print(userPosts);
} 
```

**用诡而捉:**

```
import 'dart:convert';
import 'package:http/http.dart';

void fetchUserPosts(token) {
  http.get('https://yourapi.com/user?token=$token')
    .then((userResponse) {
      final user = json.decode(userResponse.body);

      http.get("https://yourapi.com/posts?userId=${user['id']}")
        .then((postsResponse) {
          final userPosts = json.decode(postsResponse.body);
          print(userPosts);
        });
    });
} 
```