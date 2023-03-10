# Android 安全性:不要在生产中启用 WebView 调试

> 原文：<https://dev.to/ashishb/android-security-don-t-leave-webview-debugging-enabled-in-production-5fo9>

可以通过`WebView.setWebContentsDebuggingEnabled(true)`启用 WebView 调试。在生产 Android 应用程序中启用 WebView 调试并不是一个好主意。任何获得解锁手机的人都可以永久访问该应用程序的数据。

考虑到这一点，Tripit 应用程序公开了 WebView 调试，通过使用它，我可以读取私有数据目录中的所有文件。例如，通过将用户解锁的手机连接到我的笔记本电脑，我可以提取 TripIt OAuthToken。

首先通过 ADB 连接手机，在 chrome 浏览器中打开 chrome://inspect，然后在那里输入以下内容。

```
window.location="file:///data/data/com.tripit/shared_prefs/com.tripit.xml"
document.getElementsByTagName("html")[0].innerHTML 
```

现在，你可以看到所有类似
的条目

```
<string name=”oauthTokenSecret”>f731d36cdbf9006f917307…</string> 
```

这些授权令牌可以被复制并用于永久访问用户的 TripIt 帐户。

原帖[ashishb.net](https://ashishb.net/security/android-security-dont-leave-webview-debugging-enabled-in-production/)