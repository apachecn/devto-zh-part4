# 如何查询 Android 终端支持的 OpenGL ES 版本

> 原文：<https://dev.to/shiena/android-opengl-es-2125>

根据 AndroidManifest.xml 的`android:glEsVersion`，定义如下。

> APP 所需的 OpenGL ES 版本。 前 16 位表示主要编号，后 16 位表示次要编号。 例如，要指定 OpenGL ES 版本 2.0，请将该值设置为" 0x00020000 "。 此外，如果指定 OpenGL ES 3.2，请将值设置为" 0x00030002 "。

这个值可以在 build.prop 的`ro.opengles.version`中被调查。

```
adb shell getprop ro.opengles.version 
```

因为这个值是十进制，所以转换成十六进制很容易理解。

| OpenGL ES 版本 | Android:gler vision | ro.opengles.version |
| --- | --- | --- |
| OpenGL ES 1.0 | 0x00010000 | Sixty-five thousand five hundred and thirty-six |
| OpenGL ES 1.1 | 0x00010001 | Sixty-five thousand five hundred and thirty-seven |
| OpenGL ES 2.0 | 0x00020000 | One hundred and thirty-one thousand and seventy-two |
| OpenGL ES 3.0 | 0x00030000 | One hundred and ninety-six thousand six hundred and eight |
| OpenGL ES 3.1 | 0x00030001 | One hundred and ninety-six thousand six hundred and nine |
| OpenGL ES 3.2 | 0x00030002 | One hundred and ninety-six thousand six hundred and ten |

## Reference

*   [<用途-功能> |安卓开发者](https://developer.android.com/guide/topics/manifest/uses-feature-element#glEsVersion)
*   [实验 OpenGL 到 OpenGL ES](https://www.facebook.com/notes/remix-os-fans-indonesia/experiment-opengl-to-opengl-es/318468448503127)