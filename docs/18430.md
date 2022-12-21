# Xcode 不允许我们未经允许就使用网络摄像头

> 原文：<https://dev.to/0xkoji/xcode-doesn-t-allow-us-to-use-webcam-without-asking-5bf2>

今天我写了一个简单的脚本，通过 openFrameworks 使用网络摄像头。我已经有一段时间没有为网络摄像头写剧本了。

然后，我得到了下面的错误。

```
This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSCameraUsageDescription key with a string value explaining to the user how the app uses this data. 
```

Enter fullscreen mode Exit fullscreen mode

很明显，Xcode 告诉我，我不允许你使用网络摄像头而不告知原因。当需要使用手机摄像头时，这与 Swift/Objective-C 相同。

但是，对我来说有点惊讶。

解决方法是在`openFrameworks-Info.plist`
中添加以下内容

```
<key>NSCameraUsageDescription</key>
    <string>Need to access your camera to capture a picture and record a video.</string>
    <key>NSMicrophoneUsageDescription</key>
    <string>Need to access your microphone to record a video.</string> 
```

Enter fullscreen mode Exit fullscreen mode