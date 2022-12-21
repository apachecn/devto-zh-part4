# Samsung LogCat Fix

> 原文：<https://dev.to/droidpulkit/samsung-logcat-fix-55o7>

你厌倦了杂乱的 Logcat 吗？我知道了，我有一部三星 Note 9，据三星专家称，他们在 Android Studio 的 Logcat 窗口中添加了各种随机选项。

基本上我的灵感来自这个博客

[https://medium . com/@ vlonjatgashi/how-to-filter-out-Android-log cat-logs-66945 a 4a 0 e0a](https://medium.com/@vlonjatgashi/how-to-filter-out-android-logcat-logs-66945a4a0e0a)

只需创建一个新的日志标记，如下所示

```
^(?!.*(AbsListView|ActivityThread|gies.*|ScrollView|OpenGLRenderer|InputTransport|InputMethodManager|InputMethodManager|zygote64|dalvik|ViewRootImpl*)).*$ 
```

Enter fullscreen mode Exit fullscreen mode

这是给模拟器一堆随机日志

```
^(?!.*(libEGL|libc|EGL_emulation|eglCodecCommon|RenderThread|HostConnection|Gralloc3|OpenGLRenderer|AbsListView|ActivityThread|gies.*|ScrollView|OpenGLRenderer|InputTransport|InputMethodManager|InputMethodManager|zygote64|dalvik|ViewRootImpl*)).*$ 
```

Enter fullscreen mode Exit fullscreen mode

和包名作为应用包名。

享受无杂乱的生活；)