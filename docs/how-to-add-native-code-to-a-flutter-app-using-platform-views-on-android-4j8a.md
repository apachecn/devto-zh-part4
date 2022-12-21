# 如何使用 Android 上的平台视图向 Flutter 应用程序添加本机代码

> 原文：<https://dev.to/orkon/how-to-add-native-code-to-a-flutter-app-using-platform-views-on-android-4j8a>

在本教程中，我们学习如何在 Android 上的 Flutter 应用程序中添加原生组件。对于 iOS 来说，过程是相似的，但是本教程不涉及它。我们将添加一个原生 WebView 组件作为示例，并用 Kotlin 编写代码。

## 第一步。在 Flutter 中定义包装组件

该组件将在整个应用程序中使用，与本机代码的通信将封装在内。在本教程中，我们如下创建一个`WebView`组件:

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

在这段代码中，我们将`WebView`声明为有状态小部件。小部件的状态在`WebViewState`中定义。在`build`方法中，我们检查平台是 Android，并创建一个`AndroidView`。需要`viewType`才能将本机代码连接到这个`AndroidView`。一旦创建了视图，我们实例化`WebViewController`并将视图控制权交给父代码。

在`WebViewController`中，我们为组件的每个实例创建了一个具有唯一名称的方法通道。本机代码和 Flutter 之间的通信通过双向异步方法通道进行。在`loadUrl`中，我们向通道发送一条消息来调用本地组件的`loadUrl`方法，并给出一个要加载的 URL。

## 第二步。定义本机组件

转到`android`->-`app`->-`src`->-`main`->-`kotlin`->->-`yourPackageName`。这是我们放置本地代码的地方。首先，我们在`WebViewPlugin.kt` :
中定义一个原生插件

```
import io.flutter.plugin.common.PluginRegistry.Registrar

object WebViewPlugin {
    fun registerWith(registrar: Registrar) {
        registrar
                .platformViewRegistry()
                .registerViewFactory(
                        "webview", WebViewFactory(registrar.messenger()))
    }
} 
```

这里的字符串`webview`应该与上一步中使用的`viewType`相匹配。这段代码告诉应用程序`WebViewFactory`用于创建该视图类型的实例。

接下来，我们在`WebViewFactory.kt` :
中定义工厂

```
import android.content.Context
import io.flutter.plugin.common.BinaryMessenger
import io.flutter.plugin.common.StandardMessageCodec
import io.flutter.plugin.platform.PlatformView
import io.flutter.plugin.platform.PlatformViewFactory

class WebViewFactory(private val messenger: BinaryMessenger) : PlatformViewFactory(StandardMessageCodec.INSTANCE) {

    override fun create(context: Context, id: Int, o: Any?): PlatformView {
        return MyWebView(context, messenger, id)
    }
} 
```

工厂创建了`MyWebView`的实例，这是组件的本地实现。我们在`MyWebView.kt` :
中定义它

```
import android.content.Context
import android.view.View
import android.widget.TextView
import io.flutter.plugin.common.MethodCall
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugin.common.MethodChannel.MethodCallHandler
import io.flutter.plugin.common.MethodChannel.Result
import io.flutter.plugin.common.BinaryMessenger
import io.flutter.plugin.platform.PlatformView
import android.webkit.*
import android.net.http.SslError
import android.webkit.WebChromeClient
import android.webkit.WebView
import android.graphics.Bitmap
import android.webkit.WebViewClient

class MyWebView internal constructor(context: Context, messenger: BinaryMessenger, id: Int) : PlatformView, MethodCallHandler {
    private val webView: WebView
    private val methodChannel: MethodChannel

    override fun getView(): View {
        return webView
    }

    init {
        webView = WebView(context)

        methodChannel = MethodChannel(messenger, "webview$id")
        methodChannel.setMethodCallHandler(this)
    }

    override fun onMethodCall(methodCall: MethodCall, result: MethodChannel.Result) {
        when (methodCall.method) {
            "loadUrl" -> loadUrl(methodCall, result)
            else -> result.notImplemented()
        }
    }

    private fun loadUrl(methodCall: MethodCall, result: Result) {
        val url = methodCall.arguments as String
        webView.loadUrl(url)
        result.success(null)
    }

    override fun dispose() {
      // TODO dispose actions if needed
    }
} 
```

在这个类中，我们创建了 Android 提供的 native `WebView`和一个方法通道。我们对方法通道使用相同的名称，以便将消息路由到正确的 Flutter 实例。

我们还分派不同的呼叫并处理`loadUrl`。这个类是您可以实现附加功能的地方。例如，根据您的需求配置 webview。

## 第三步。注册本机代码

最后一步是修改`android`->-`app`->->-`main`->->-`com`->-`yourPackageName`->-`MainActivity.kt`。我们添加一个注册调用来注册我们的插件:

```
import android.os.Bundle

import io.flutter.app.FlutterActivity
import io.flutter.plugins.GeneratedPluginRegistrant

class MainActivity: FlutterActivity() {
  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    GeneratedPluginRegistrant.registerWith(this)
    WebViewPlugin.registerWith(this.registrarFor("com.yourPackageName"))
  }
} 
```

现在插件完成了，你可以在你的 Flutter 代码中使用它了。

*最初发布于[https://60 devs . com/how-to-add-native-code-to-flutter-app-using-platform-views-Android . html](https://60devs.com/how-to-add-native-code-to-flutter-app-using-platform-views-android.html)2019 年 5 月 14 日*