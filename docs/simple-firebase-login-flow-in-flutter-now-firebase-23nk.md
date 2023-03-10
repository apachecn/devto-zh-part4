# Flutter 中的简单 Firebase 登录流程，现在是 Firebase

> 原文：<https://dev.to/aaronksaunders/simple-firebase-login-flow-in-flutter-now-firebase-23nk>

# 简单的 Firebase 登录流程在旋舞，现在 Firebase

在第一部分中，我们用以下组件创建了一个简单的应用程序

*   默认主应用入口点
    *   使用 [FutureBuilder 小部件](https://api.flutter.dev/flutter/widgets/FutureBuilder-class.html)在呈现 UI 之前等待数据，这是整个应用程序使用的概念
*   登录页面
*   主页
*   认证服务
    *   演示在 Flutter 文档中讨论的提供者的使用[简单的应用状态管理](https://flutter.dev/docs/development/data-and-backend/state-mgmt/simple#accessing-the-state)

现在，在第二部分中，我们将把 firebase 集成到应用程序中。

> 网上有很多关于为 Flutter 设置 Firebase 的例子，所以我将直接跳到代码中，而不是从基础开始。
> 参见[Google CodeLabs Flutter for Firebase](https://codelabs.developers.google.com/codelabs/flutter-firebase/index.html?index=..%2F..index#5)了解在 iOS 或 Android 上设置项目的逐步说明

### 在 Firebase 中创建一个测试用户

由于我们只是在构建应用程序，现在还没有在应用程序中创建用户的功能，请登录到您的 [Firebase 控制台](%5Bhttps://console.firebase.google.com/u/0/%5D(https://console.firebase.google.com/u/0/))并向您的项目添加一个用户。请确保在 Firebase 控制台中更新项目时启用电子邮件身份验证。

### 向项目添加 Firebase 功能的步骤

*   将 Firebase 方法添加到`AuthService`
*   启动时从`AuthService`中访问`getUser`属性，以确定在`main.dart`中加载哪个页面
*   修改`HomePage`以显示登录者的电子邮件地址`FirebaseUser`
*   修改`LoginPage`来调用`AuthService`上的`loginUser`方法，使用 Firebase API 登录一个用户，看看我们是否可以登录一个真实的`FirebaseUser`
*   最后，正确处理登录和启动时查找当前用户时的错误

#### 认证服务:添加 Firebase API 功能

首先是身份验证服务，我们只是包装了一些基本的 firebase 函数，我们需要这些函数来进行身份验证，并确定是否已经有来自上一个会话的用户

```
import 'package:firebase_auth/firebase_auth.dart';
import 'dart:async';

import 'package:flutter/cupertino.dart';

class AuthService with ChangeNotifier {
  final FirebaseAuth _auth = FirebaseAuth.instance;

  ///
  /// return the Future with firebase user object FirebaseUser if one exists
  ///
  Future<FirebaseUser> getUser() {
    return _auth.currentUser();
  }

  // wrapping the firebase calls
  Future logout() async {
    var result = FirebaseAuth.instance.signOut();
    notifyListeners();
    return result;
  }

  ///
  /// wrapping the firebase call to signInWithEmailAndPassword
  /// `email` String
  /// `password` String
  ///
  Future<FirebaseUser> loginUser({String email, String password}) async {
    try {
      var result = await FirebaseAuth.instance
          .signInWithEmailAndPassword(email: email, password: password);
      // since something changed, let's notify the listeners...
      notifyListeners();
      return result;
    }  catch (e) {
      // throw the Firebase AuthException that we caught
      throw new AuthException(e.code, e.message);
    }
  }
} 
```

正如你从上面的代码中看到的，我们仍然有相同的方法来访问我们的`AuthService`,现在唯一的不同是我们已经用对你已经设置好的 Firebase 后端的真实调用来替换这个调用。

请注意，我们不再需要为当前用户保留一个属性，因为 Firebase 会为我们管理它。我们需要做的就是调用方法`getUser`，如果有用户，我们将得到一个对象，否则它将返回 null。

最需要注意的是，当登录状态在登录或注销过程中发生变化时，我们会调用`notifyListeners()`。

#### 修改`main.dart`

不需要对文件进行真正的修改，因为我们使用的是相同的外部 API，唯一的区别是现在我们返回了一个`FirebaseUser`对象，所以让我们给代码添加一个特定的类型，并修改一些东西

```
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: FutureBuilder<FirebaseUser>(
        future: Provider.of<AuthService>(context).getUser(),
        builder: (context, AsyncSnapshot<FirebaseUser> snapshot) { //          ⇐ NEW
          if (snapshot.connectionState == ConnectionState.done) {
            // log error to console                                            ⇐ NEW
            if (snapshot.error != null) {
              print("error");
              return Text(snapshot.error.toString());
            }
            // redirect to the proper page, pass the user into the 
            // `HomePage` so we can display the user email in welcome msg     ⇐ NEW
            return snapshot.hasData ? HomePage(snapshot.data) : LoginPage();
          } else {
            // show loading indicator                                         ⇐ NEW
            return LoadingCircle();
          }
        },
      ),
    );
  }
} 
```

我们已经添加了与`AsyncSnapshot`相关联的对象类型`FirebaseUser`，现在我们正在检查错误，以防最初加载 Firebase 时出现问题。

我们还向`HomePage`小部件的构造函数添加了一个新参数，它是从对`AuthService`的`getUser`调用中返回的`FirebaseUser`对象。我们将在下一节看到新参数是如何使用的。

最后，我们添加了一个名为`LoadingCircle`的新部件，当应用程序启动并访问`Firebase`来检查新用户时，它会给我们一个良好的用户体验；参见下面的`LoadingCircle`小部件的代码。

> 参见文档 [`CircularProgressIndicator`](%5Bhttps://api.flutter.dev/flutter/material/CircularProgressIndicator/CircularProgressIndicator.html%5D(https://api.flutter.dev/flutter/material/CircularProgressIndicator/CircularProgressIndicator.html))

```
class LoadingCircle extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        child: CircularProgressIndicator(),
        alignment: Alignment(0.0, 0.0),
      ),
    );
  }
} 
```

#### 在`home_page.dart`中修改主页控件

我们需要首先通过添加一个新的构造函数来修改小部件，该构造函数将保存从`main.dart`
中的 FutureBuilder 传入的 firebase 用户

```
class HomePage extends StatefulWidget {
  final FirebaseUser currentUser;    // ⇐ NEW

  HomePage(this.currentUser);        // ⇐ NEW

  @override
  _HomePageState createState() => _HomePageState();
} 
```

现在，我们可以通过小部件访问当前用户的信息；我们可以在渲染`HomePage`时通过如下所示的修改来访问它。我们将在构建方法中添加一些小部件:

```
 children: <Widget>[
       SizedBox(height: 20.0),                         // ⇐ NEW
       Text(                                           // ⇐ NEW
         'Home Page Flutter Firebase  Content',
         style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
       ),
       SizedBox(height: 20.0),                         // ⇐ NEW
       Text(                                           // ⇐ NEW
          `Welcome ${widget.currentUser.email}`,
           style: TextStyle(    
              fontSize: 18,
              fontWeight: FontWeight.bold,
              fontStyle: FontStyle.italic),
        ),
       SizedBox(height: 20.0),
       RaisedButton(
           child: Text("LOGOUT"),
           onPressed: () async {
             await Provider.of<AuthService>(context).logout();
           })
     ], 
```

#### 修改`login_page.dart`中的 LoginPage 小工具

因为 API 签名没有改变，所以我们只需要对这个函数做很少的修改就可以得到想要的结果，但是最好做一些更好的错误检查。

对于`Future`,我们需要用`try` `catch`块包装调用，因为 Firebase 发生的任何错误都将作为异常抛出。然后，我们将在一个对话框中显示错误消息，参见方法`_buildErrorDialog`的代码和下面的其余更改。

为错误异常
添加新的导入

```
import  'package:firebase_auth/firebase_auth.dart'; 
```

对登录按钮的`onPressed`方法进行适当的修改。

```
 onPressed: () async {
       // save the fields..
       final form = _formKey.currentState;
       form.save();

       // Validate will return true if is valid, or false if invalid.
       if (form.validate()) {
         try {
           FirebaseUser result =
               await Provider.of<AuthService>(context).loginUser(
                   email: _email, password: _password);
           print(result);  
         } on AuthException catch (error) {
           // handle the firebase specific error
           return _buildErrorDialog(context, error.message);
         } on Exception catch (error) {
           // gracefully handle anything else that might happen.. 
           return _buildErrorDialog(context, error.toString());
         }
       }
     }, 
```

为新的私有`_buildErrorDialog`方法添加代码，该方法将显示调用`AuthService`登录方法时出现的错误。

```
 Future _buildErrorDialog(BuildContext context, _message) {
    return showDialog(
      builder: (context) {
        return AlertDialog(
          title: Text('Error Message'),
          content: Text(_message),
          actions: <Widget>[
            FlatButton(
                child: Text('Cancel'),
                onPressed: () {
                  Navigator.of(context).pop();
                })
          ],
        );
      },
      context: context,
    );
  } 
```

### 结论

此时，您应该有一个具有基本登录流的正常运行的应用程序，用户将使用您在 Firebase 控制台中添加的测试用户的凭据登录到 Firebase。

尝试输入无效的密码凭据和不完整的电子邮件地址，错误应该会适当地显示出来。

### 资源

源代码可以在这里找到:[https://github.com/aaronksaunders/simple_firebase_auth](https://github.com/aaronksaunders/simple_firebase_auth)