# Blockstack Android 提示

> 原文：<https://dev.to/bloco/blockstack-android-tips-36l8>

当使用 [Blockstack Android 库](https://github.com/blockstack/blockstack-android)开发[envelope Android 应用](https://play.google.com/store/apps/details?id=app.envelop)时，我遇到了一些重要的问题。我想把它们分享给那些踏上相同道路的人。

* * *

## 首发出场

该库仍然是预发布的，但它是基于更稳定的 blockstack.js 库构建的。这意味着也不是一个本地库。它使用 j2v8 引擎来解释 javascript 库并与之交互。

两个主要后果是:

*   由于块堆栈库，您的应用程序将增加大约 20Mb 的重量
*   当你初始化一个`BlockstackSession`时，你需要小心。它需要一段时间才能使用。

## 穿线

库线程由`Executor`配置。它定义了 3 个线程，主线程、后台线程和运行 javascript 库的 V8 线程。

默认情况下，V8 线程使用主线程。但是对于一个生产应用程序，这意味着在每次操作时阻塞主线程，这是绝对不推荐的。为了避免这种情况，您应该定义一个新的`Executor`并将其设置在`BlockstackSession`上。这里有一个例子:

```
class BlockstackExecutor(
  private val context: Context
) : Executor {

  private val handlerThread = HandlerThread("BlockstackV8").apply { start() }
  private val handler = Handler(handlerThread.looper)

  override fun onV8Thread(function: () -> Unit) {
    handler.post {
      function.invoke()
    }
  }

  override fun onMainThread(function: (ctx: Context) -> Unit) {
    GlobalScope.launch(Dispatchers.Main) {
      function(context)
    }
  }

  override fun onNetworkThread(function: suspend () -> Unit) {
    GlobalScope.launch(Dispatchers.IO) {
      function()
    }
  }
} 
```

但是现在您需要确保您正在为刚刚创建的线程调用块堆栈操作。例如，如果您正在使用 RxJava，这意味着为该线程构建一个自定义的`Scheduler`，并在`subscribeOn` :
中使用它

```
val blockstackScheduler = Schedulers.from {
  handler.post(it)   
} 
```

例如，下面是我们如何用 RxJava:
包装一个`deleteFile`操作

```
fun deleteFile(fileName: String) =
  Completable
    .create { emitter ->
      try {
        blockstack.deleteFile(fileName, DeleteFileOptions()) {
          if (it.hasErrors) {
            emitter.onError(Exception(it.error))
          } else {
            emitter.onComplete()
          }
        }
      } catch (t: Throwable) {
        emitter.onError(t)
      }
    }
    .subscribeOn(blockstackScheduler) 
```

## 网络错误

在当前的库版本(0.4.3)中，有一个隐藏网络错误的 bug。它们与耗时太长的请求变得难以区分。有一个[未决的公关](https://github.com/blockstack/blockstack-android/pull/153)来修复它。但在那之前，我们使用我们的[自己的叉子](https://github.com/sdsantos/blockstack-android)。

## 上下文

在给出的例子中，`BlockstackSession`用一个`Activity Context`初始化。但是对于几乎所有的块堆栈操作，`Application Context`。这对于从`Activity`类中提取逻辑并正确构建您的代码非常有用。

但是登录调用`redirectUserToSignIn`需要一个带有`Activity Context`的`BlockstackSession`。那是因为它从内部打开了一个 webview。如果您仍然想对所有其他调用使用`Application Context`，只需确保不同的`BlockstackSession`实例共享同一个`SessionStore`。这样，所有的`BlockstackSession`实例从相同的位置读取和存储会话数据。

## APK 签约

这一点与 Blockstack 没有直接关系，但需要密切关注。登录期间，用户首先在 webview 内的 Blockstack 上登录。然后身份验证响应通过一个应用程序链接返回到 Android 应用程序。为了使该过程无缝，应用程序链接应该被自动验证。

Google Play 现在鼓励将 APK 签名流程委托给他们。但是这意味着最终出现在服务器文件`assetlinks.json`中的`sha256_cert_fingerprints`将不是您所期望的。您的上传密钥签名将从 APK 中删除。你需要从 Google Play 仪表盘的`Release management > App signing`下获取正确的指纹。

* * *

这就是我得到的。希望我没有吓到你，其实没那么糟。只是一些将来可能会解决的细节问题。任何其他问题，你都可以联系到[库的 Github](https://github.com/blockstack/blockstack-android) 。我也会盯着那里的。

好奇 Blockstack app 是什么样的？试试我们的[信封安卓应用](https://play.google.com/store/apps/details?id=app.envelop)，看看我们应用的[源代码](https://github.com/envelop-app/envelop-android)。