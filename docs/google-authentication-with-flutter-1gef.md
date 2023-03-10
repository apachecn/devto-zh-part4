# 使用 Flutter 的谷歌认证

> 原文：<https://dev.to/codesundar/google-authentication-with-flutter-1gef>

最初发布:[如何用 Flutter 实现 google 登录](https://codesundar.com/flutter-google-login/)

[视频教程](https://www.youtube.com/watch?v=33T8JgK_yVw)

[![Flutter Google Login](img/75b984abef837dc29b7fad2143f0855c.png)](http://www.youtube.com/watch?v=33T8JgK_yVw)

在`pubspec.yaml`
中添加 google_sign_in 依赖项

```
dependencies:
  flutter:
    sdk: flutter
  google_sign_in: ^4.0.2 
```

Enter fullscreen mode Exit fullscreen mode

更新您的`main.dart`如下

```
import 'package:flutter/material.dart';
import 'package:google_sign_in/google_sign_in.dart';

void main() => runApp(MyApp());

class MyApp extends StatefulWidget {
  @override
  State<StatefulWidget> createState() {
    // TODO: implement createState
    return _MyAppState();
  }
}

class _MyAppState extends State<MyApp> {
  bool _isLoggedIn = false;

  GoogleSignIn _googleSignIn = GoogleSignIn(scopes: ['email']);

  _login() async{
    try{
      await _googleSignIn.signIn();
      setState(() {
        _isLoggedIn = true;
      });
    } catch (err){
      print(err);
    }
  }

  _logout(){
    _googleSignIn.signOut();
    setState(() {
      _isLoggedIn = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return MaterialApp(
      home: Scaffold(
        body: Center(
            child: _isLoggedIn
                ? Column(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: <Widget>[
                      Image.network(_googleSignIn.currentUser.photoUrl, height: 50.0, width: 50.0,),
                      Text(_googleSignIn.currentUser.displayName),
                      OutlineButton( child: Text("Logout"), onPressed: (){
                        _logout();
                      },)
                    ],
                  )
                : Center(
                    child: OutlineButton(
                      child: Text("Login with Google"),
                      onPressed: () {
                        _login();
                      },
                    ),
                  )),
      ),
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode