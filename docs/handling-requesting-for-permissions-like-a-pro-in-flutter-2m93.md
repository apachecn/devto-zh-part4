# 像专业人员一样处理权限请求

> 原文：<https://dev.to/mainawycliffe/handling-requesting-for-permissions-like-a-pro-in-flutter-2m93>

在这一课中，我们将了解如何在 Flutter 中请求和检查 Android 和 IOS 上的权限。

## 包

*   权限处理程序([权限处理程序](https://pub.dev/packages/permission_handler))

### 安装

安装一个 flutter 包的过程非常简单，只需打开 **pubspec** 文件，并将该包添加到您的依赖 bloc 部分。

```
dependencies:
  permission_handler: 
```

Enter fullscreen mode Exit fullscreen mode

> **提示:**您可以使用脱字符版本控制来将包版本限制为特定的主要版本。例如，`permission_handler: ^3.0.0`将其限制在第 3 版，但你将获得较小的更新和补丁。
> 如果你有兴趣，你可以在这里了解更多关于 dart pub package manager 如何工作的[。](https://codinglatte.com/posts/flutter/flutter-a-closer-look-at-how-pub-handles-dependency-versions/)

## 权限

首先，我们需要确定我们需要哪些权限，因为你的 app 需要公开声明它需要的权限。一些不太敏感的权限，如互联网等。是自动授予的。而其他更敏感的权限，如位置、联系人等。在您的应用程序可以使用它们之前，需要用户授权。

### iOS 权限

在 iOS 中，这是通过将权限添加到**信息属性列表文件**即`info.plist`来完成的，您需要的权限是关键，并附有您的应用程序需要使用该功能的原因。

```
<key>NSPhotoLibraryUsageDescription</key>
<string>This app requires to save your images user gallery</string> 
```

Enter fullscreen mode Exit fullscreen mode

在 Flutters 的例子中，是项目根目录下的`iOS/Runner`目录中的`info.plist`。你可以在这里了解更多关于`info.plist`T3 的信息。

你可以在这里找到权限[的完整列表，在这里](https://www.iosdev.recipes/info-plist/permissions/)找到在 iOS [上请求权限的指南。](https://developer.apple.com/design/human-interface-guidelines/ios/app-architecture/requesting-permission/)

### 安卓权限

在 Android 中，您可以通过将`<uses-permission>`标签添加到 android manifest(在`android/src/main/AndroidManifest.xml`目录中)来实现同样的目的。

```
<manifest ...>
    <uses-permission android:name="android.permission.SEND_SMS"/>
    <application ...>
        ...
    </application>
</manifest> 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里了解更多关于 Android 权限的信息，在这里了解最佳实践[。](https://developer.android.com/training/permissions/usage-notes)

## 请求许可

要请求许可，首先您需要导入包:

```
import 'package:permission_handler/permission_handler.dart'; 
```

Enter fullscreen mode Exit fullscreen mode

然后，假设您想要请求联系人许可，您可以这样做。您传递一个您正在请求的权限列表，这允许您一次请求多个您需要的权限。

```
final PermissionHandler _permissionHandler = PermissionHandler();
var result = await _permissionHandler.requestPermissions([PermissionGroup.contacts]); 
```

Enter fullscreen mode Exit fullscreen mode

最后，您可以检查结果，看看用户是否授予了权限。结果对象是一个数组`PermissionStatus`。数组的关键字是您所请求的权限。

可能的权限状态包括:

| 状态 | 为什么 |
| --- | --- |
| **授予** | 你的应用已获得使用相关功能的权限 |
| **被拒绝** | 你的应用已获得使用相关功能的权限 |
| **禁用** | 相关功能被禁用，即当定位功能被禁用时。 |
| **受限**(仅限 iOS) | 由于家长控制等原因，操作系统限制了对特定功能的访问。 |
| **未知** | 未知的权限状态。 |

例如，在上面的例子中，我们可以检查访问联系人的许可是否是这样授予的:

```
if (result[PermissionGroup.contacts] == PermissionStatus.granted) {
  // permission was granted
} 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用 switch 语句对请求的结果做出反应。

```
switch (result[PermissionGroup.contacts]) {
  case PermissionStatus.granted:
    // do something
    break;
  case PermissionStatus.denied:
    // do something
    break;
  case PermissionStatus.disabled:
    // do something
    break;
  case PermissionStatus.restricted:
    // do something
    break;
  case PermissionStatus. Unknown:
    // do something
    break;
  default:
} 
```

Enter fullscreen mode Exit fullscreen mode

## 检查您的应用程序是否获得许可

重要的是，你的应用程序可以检查它是否有权限访问某些功能。这允许你的应用程序根据用户是否授予了权限来决定。最重要的是，它可以帮助您确定某个功能在用户的设备上是可用还是被禁用。

您可以使用`checkPermissionStatus`函数检查权限，该函数接受权限作为参数。

```
final PermissionHandler _permissionHandler = PermissionHandler();

var permissionStatus = await _permissionHandler.checkPermissionStatus(PermissionGroup.location); 
```

Enter fullscreen mode Exit fullscreen mode

这返回了一个`PermissionStatus`类——我们在上面看到过——它详细描述了许可的状态。

```
switch (permissionStatus) {
  case PermissionStatus.granted:
    // do something
    break;
  case PermissionStatus.denied:
    // do something
    break;
  case PermissionStatus.disabled:
    // do something
    break;
  case PermissionStatus.restricted:
    // do something
    break;
  case PermissionStatus.unknown:
    // do something
    break;
  default:
} 
```

Enter fullscreen mode Exit fullscreen mode

## 提示

一些权限，如联系人、日历、麦克风、位置等。在 iOS 和 Android 中很常见。另一方面，有些权限在两种操作系统上都不匹配。而其他的只是在一个操作系统上，即存储、短信、电话等。只在安卓系统上。在向用户请求许可之前，您可能需要确定它是哪个平台。

以相机应用程序为例，如果你想让照片出现在图库中，在 iOS 上，你需要照片权限，而在 android 上，你需要外部存储权限。

```
var permission = Platform.isAndroid ? PermissionGroup.storage : PermissionGroup.photos; 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以检查用户是否授予了您的应用权限:

```
await _permissionHandler.checkPermissionStatus(permission); 
```

Enter fullscreen mode Exit fullscreen mode

或者请求许可:

```
await _permissionHandler.requestPermissions([permission]); 
```

Enter fullscreen mode Exit fullscreen mode

## 资源

*   请求 IOS-[链接](https://developer.apple.com/design/human-interface-guidelines/ios/app-architecture/requesting-permission/)的权限。
*   在 Android 上请求许可-[链接](https://developer.android.com/training/permissions/requesting)。
*   Android 上的许可最佳实践-[链接](https://developer.android.com/training/permissions/usage-notes)。
*   关于信息属性列表文件-[链接](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/AboutInformationPropertyListFiles.html)。
*   Android 应用清单-[链接](https://developer.android.com/guide/topics/manifest/manifest-intro)。