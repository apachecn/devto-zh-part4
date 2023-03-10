# 如何在 iOS 上使用平台视图向 Flutter 应用程序添加本机代码

> 原文：<https://dev.to/orkon/how-to-add-native-code-to-a-flutter-app-using-platform-views-on-ios-4367>

在本教程中，我们学习如何在 iOS 上向 Flutter 应用程序添加本机组件。安卓指南见[我的 previus 贴](https://60devs.com/how-to-add-native-code-to-flutter-app-using-platform-views-android.html)。

## 第一步。在 Flutter 中定义包装组件

该组件将在整个应用程序中使用，与本机代码的通信将封装在内。在本教程中，我们如下创建一个`WebView`组件。请注意，除了支持 iOS 的额外分支之外，代码与 Android 完全相同。

```
import 'dart:async';
import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

typedef void WebViewCreatedCallback(WebViewController controller);

class WebView extends StatefulWidget {
  const WebView({
    Key key,
    this.onWebViewCreated,
  }) : super(key: key);

  final WebViewCreatedCallback onWebViewCreated;

  @override
  State<StatefulWidget> createState() => WebViewState();
}

class WebViewState extends State<WebView> {
  @override
  Widget build(BuildContext context) {
    if (defaultTargetPlatform == TargetPlatform.android) {
      return AndroidView(
        viewType: 'webview',
        onPlatformViewCreated: _onPlatformViewCreated,
      );
    } else if (defaultTargetPlatform == TargetPlatform.iOS) {
      return UiKitView(
        viewType: 'webview',
        onPlatformViewCreated: _onPlatformViewCreated,
      );
    }
    // TODO add other platforms
    return Text(
        '$defaultTargetPlatform is not yet supported by the map view plugin');
  }

  void _onPlatformViewCreated(int id) {
    if (widget.onWebViewCreated == null) {
      return;
    }
    widget.onWebViewCreated(new WebViewController(id));
  }
}

class WebViewController {
  WebViewController(int id) {
    this._channel = new MethodChannel('webview$id');
  }

  MethodChannel _channel;

  Future<void> loadUrl(String url) async {
    return _channel.invokeMethod('loadUrl', url);
  }
} 
```

在这段代码中，我们将`WebView`声明为有状态小部件。小部件的状态在`WebViewState`中定义。在`build`方法中，我们检查平台是 iOS，并创建一个`UiKitView`。需要`viewType`才能将本机代码连接到这个`UiKitView`。一旦创建了视图，我们实例化`WebViewController`并将视图控制权交给父代码。

在`WebViewController`中，我们为组件的每个实例创建了一个具有唯一名称的方法通道。本机代码和 Flutter 之间的通信通过双向异步方法通道进行。在`loadUrl`中，我们向通道发送一条消息来调用本地组件的`loadUrl`方法，并给出一个要加载的 URL。

## 第二步。定义本机组件

转到`ios`->-T1。这是我们放置本地代码的地方。首先，我们向`AppDelegate.swift` :
添加一个注册呼叫

```
let controller = window?.rootViewController as! FlutterViewController
let webviewFactory = WebviewFactory(controller: controller)

registrar(forPlugin: "webview").register(webviewFactory, withId: "webview") 
```

这里的字符串`webview`应该与上一步中使用的`viewType`相匹配。这段代码告诉应用程序`WebViewFactory`用于创建该视图类型的实例。

接下来，我们在`WebViewFactory.swift` :
中定义工厂

```
import Foundation

public class WebviewFactory : NSObject, FlutterPlatformViewFactory {
    let controller: FlutterViewController

    init(controller: FlutterViewController) {
        self.controller = controller
    }

    public func create(
        withFrame frame: CGRect,
        viewIdentifier viewId: Int64,
        arguments args: Any?
    ) -> FlutterPlatformView {
        let channel = FlutterMethodChannel(
            name: "webview" + String(viewId),
            binaryMessenger: controller
        )
        return MyWebview(frame, viewId: viewId, channel: channel, args: args)
    }
} 
```

工厂创建了`MyWebview`的实例，这是组件的本地实现。我们在`MyWebview.swift` :
中定义它

```
 import Foundation
import UIKit
import WebKit

public class MyWebview: NSObject, FlutterPlatformView, WKScriptMessageHandler, WKNavigationDelegate {
    let frame: CGRect
    let viewId: Int64
    let channel: FlutterMethodChannel
    let webview: WKWebView

    init(_ frame: CGRect, viewId: Int64, channel: FlutterMethodChannel, args: Any?) {
        self.frame = frame
        self.viewId = viewId
        self.channel = channel

        let config = WKWebViewConfiguration()
        let webview = WKWebView(frame: frame, configuration: config)

        self.webview = webview

        super.init()

        channel.setMethodCallHandler({
            (call: FlutterMethodCall, result: FlutterResult) -> Void in
            if (call.method == "loadUrl") {
                let url = call.arguments as! String
                webview.load(URLRequest(url: URL(string: url)!))
            }
        })
    }

    public func view() -> UIView {
        return self.webview
    } 
```

在这个类中，我们创建了 iOS 提供的 native `WKWebView`和一个方法通道。我们对方法通道使用相同的名称，以便将消息路由到正确的 Flutter 实例。

我们还分派不同的呼叫并处理`loadUrl`。这个类是您可以实现附加功能的地方。例如，根据您的需求配置 webview。

现在插件完成了，你可以在你的 Flutter 代码中使用它了。

*最初发布于[https://60 devs . com/how-to-add-native-code-to-flutter-app-using-platform-views-IOs . html](https://60devs.com/how-to-add-native-code-to-flutter-app-using-platform-views-ios.html)T3】*