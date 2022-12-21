# Android:既有图像又有文本的按钮

> 原文：<https://dev.to/gautemeekolsen/android-button-with-both-image-and-text-452a>

假设您想要一个按钮来显示带有文本的图像。然后，您会添加两个视图，将它们对齐并拥有两个点击监听器吗？

不，有更简单的方法。

```
<?xml version="1.0" encoding="utf-8"?>
<Button 
android:text="Start" 
android:drawableTop="@drawable/start_wrap" 
android:drawableTint="@android:color/white" 
android:drawablePadding="10dp" 
android:background="@android:color/transparent" 
android:textColor="@android:color/white"/> 
```

像这样给按钮添加一个 drawable。如果你想改变尺寸，我喜欢创建一个可绘制的包装器，它负责调整尺寸。像这样:

```
<?xml version="1.0" encoding="utf-8"?> 
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" > 
  <item 
  android:drawable="@drawable/baseline_camera_enhance_24" 
  android:width="40dp" 
  android:height="40dp" 
  /> 
</layer-list > 
```

## 即一切，简单易行