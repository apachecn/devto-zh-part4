# 用 Python 自动化 Android

> 原文：<https://dev.to/petercour/automate-android-with-python-14j0>

[![](img/98e1debc5ca2fa52651fba8881efa75f.png)](https://i.giphy.com/media/D63BmEGlxJYLC/giphy.gif)

安卓手机可以通过 Python 脚本控制。为此，必须安装 adb 并连接手机。

然后，您可以使用 adb shell 命令来控制手机，就像

```
#!/usr/bin/python3
import os

...

os.system("adb shell input tap 350 715") 
```

点击屏幕上的。您也可以键入文本

```
#!/usr/bin/python3
os.system('adb shell input text "insert%syour%stext%shere"') 
```

结合 time.sleep(秒)，你可以自动化任何 android 应用程序。记住，你不必同时使用电话。

## 脚本

您可以将它包装在函数周围以使它变得简单。使用 usb 电缆连接您的设备，键入

```
adb usb 
```

然后在你最喜欢的聊天程序中运行下面的脚本

```
#!/usr/bin/python3
import os

def android_tap(x,y):
    os.system("adb shell input tap " + x + " " + y)

def android_type(text):
    os.system('adb shell input text "' + text + '"')

android_type("great") 
```

adb 程序可以做更多的事情，像这样的包装器可以让你的 android 手机脚本化

学习 Python:

*   [https://pythonbasics.org](https://pythonbasics.org)
*   [https://pythonprogramminglanguage.com](https://pythonprogramminglanguage.com)