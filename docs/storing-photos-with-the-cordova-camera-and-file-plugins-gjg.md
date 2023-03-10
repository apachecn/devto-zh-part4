# 使用 Cordova 相机和文件插件存储照片

> 原文：<https://dev.to/ionic/storing-photos-with-the-cordova-camera-and-file-plugins-gjg>

在基于科尔多瓦的 Ionic 应用程序中存储照片是一项挑战。涉及到应用开发栈的几个概念和层次，包括选择最佳的相机插件配置，将文件保存到永久存储，以及了解如何在 WebView 中渲染图像。

在这篇文章中，我们将使用 [Ionic 原生相机插件](https://ionicframework.com/docs/native/camera)拍摄照片，然后使用 [Ionic 原生文件插件](https://ionicframework.com/docs/native/file)将照片保存到应用程序的文件系统中。

> 注意:关于使用电容器的相机和文件插件的类似概念的指南，见 Josh Morony 的伟大指南[这里](https://www.joshmorony.com/using-the-capacitor-filesystem-api-to-store-photos/)。

## 插件安装

首先，将两个本地插件安装到 Ionic 项目中:

```
# Camera plugin
ionic cordova plugin add cordova-plugin-camera
npm install @ionic-native/camera

# File plugin
ionic cordova plugin add cordova-plugin-file
npm install @ionic-native/file 
```

## 相机插件:配置和拍摄照片

安装了这两个插件后，我们现在可以配置相机选项了。我们使用`FILE_URI`作为目的地类型，将捕获的图像保存到临时文件存储中。避免使用以 base64 编码的字符串形式返回图像的`DATA_URL`，因为将图像数据加载到字符串中是非常占用内存的，经常会导致应用程序崩溃。

```
const options: CameraOptions = {
  quality: 100,
  destinationType: this.camera.DestinationType.FILE_URI,
  encodingType: this.camera.EncodingType.JPEG
} 
```

接下来，调用`getPicture`方法打开设备上的摄像头。一旦应用程序用户拍摄了照片并确认应该使用它，新创建的图像的路径就会存储在`tempImage`变量中。

```
const tempImage = await this.camera.getPicture(options); 
```

## 将照片保存到文件系统

正如我们所看到的，使用相机插件来拍照是很简单的。然而，将它们永久保存到文件系统会变得很快。为什么我们需要使用文件系统插件来存储照片呢？原来，相机插件捕捉的图像只存储在应用程序的临时存储中。这使得内存使用率很低，这在移动设备上当然很重要，在实现让用户在存储照片之前预览照片等功能时也很有用。然而，这意味着当应用程序关闭时，图像将被移动操作系统删除。

作为以下代码的一部分，我将展示文件系统值的示例(全部用于 iOS，尽管 Android 看起来非常相似)，每个变量代表照片保存实现的每个步骤。在调试使用文件插件的代码时，看到这些值很有帮助。下面是`tempImage` :
的一个示例值

```
file:///var/mobile/Containers/Data/Application/E4A79B4A-E5CB-4E0C-A7D9-0603ECD48690/tmp/cdv_photo_003.jpg 
```

让我们看一下文件路径的每个部分:

*   `file:///`:文件协议，表示这是设备上的文件。
*   `E4A79B4A-E5CB-4E0C-A7D9-0603ECD48690`:这是应用的唯一 id (GUID)。你的会和我的不一样。
*   `/tmp/`:本 app 内*的临时目录。*
*   `cdv_photo_003.jpg`:照片的文件名。

接下来，让我们将照片保存到文件系统，从提取临时图像文件路径的不同部分开始，这样我们就可以使用文件插件了。

```
// Extract just the filename. Result example: cdv_photo_003.jpg
const tempFilename = cameraImage.substr(tempImage.lastIndexOf('/') + 1);

// Now, the opposite. Extract the full path, minus filename. 
// Result example: file:///var/mobile/Containers/Data/Application
// /E4A79B4A-E5CB-4E0C-A7D9-0603ECD48690/tmp/
const tempBaseFilesystemPath = tempImage.substr(0, tempImage.lastIndexOf('/') + 1);

// Get the Data directory on the device. 
// Result example: file:///var/mobile/Containers/Data/Application
// /E4A79B4A-E5CB-4E0C-A7D9-0603ECD48690/Library/NoCloud/
const newBaseFilesystemPath = this.file.dataDirectory; 
```

接下来，通过将照片从临时存储器复制到永久文件存储器，将照片保存到文件系统，同时保持原始文件名。还有其他方法可以保存文件，但这是最简单的。注意，保存文件后，照片现在位于`Library/NoCloud/`目录下，而不是`tmp/`目录下。

```
await this.file.copyFile(tempBaseFilesystemPath, tempFilename, 
                         newBaseFilesystemPath, tempFilename);

// Result example: file:///var/mobile/Containers/Data/Application
// /E4A79B4A-E5CB-4E0C-A7D9-0603ECD48690/Library/NoCloud/cdv_photo_003.jpg
const storedPhoto = newBaseFilesystemPath + tempFilename; 
```

## 渲染照片

现在图像已经存储到文件系统中，您可能希望在同一个应用程序中显示它。不过，有一个不那么明显的障碍:Cordova 和 Capacitor 应用程序托管在本地 HTTP 服务器上，并使用`http://`协议提供服务。然而，正如您所记得的，文件是使用`file://`协议“托管”的，因此我们必须使用 [Ionic Native WebView 提供的](https://ionicframework.com/docs/building/webview#file-protocol) `convertFileSrc( )`方法将图像从设备文件路径重写到本地 HTTP 服务器。

```
const displayImage = this.webview.convertFileSrc(storedPhoto); 
```

然后，使用 Ionic `ion-image`组件渲染图像。

```
<ion-img src="{{ displayImage }}" 
```

下面是完整的例子:

```
const options: CameraOptions = {
  quality: 100,
  destinationType: this.camera.DestinationType.FILE_URI,
  encodingType: this.camera.EncodingType.JPEG
}

const tempImage = await this.camera.getPicture(options);
const tempFilename = cameraImage.substr(tempImage.lastIndexOf('/') + 1);
const tempBaseFilesystemPath = tempImage.substr(0, tempImage.lastIndexOf('/') + 1);

const newBaseFilesystemPath = this.file.dataDirectory;

await this.file.copyFile(tempBaseFilesystemPath, tempFilename, 
                         newBaseFilesystemPath, tempFilename);

const storedPhoto = newBaseFilesystemPath + tempFilename;
const displayImage = this.webview.convertFileSrc(storedPhoto); 
```

### 有角度的 URL 清理

使用 Angular 时，如果使用[属性数据绑定](https://angular.io/guide/template-syntax#property-binding-property)，则需要一个额外的步骤。如果图像无法渲染，很可能是被阻止了。在 DevTools 控制台中，将出现一个错误，提到不安全/不支持的 URL。

我们知道我们的图像是安全的，所以通过使用上面的`webview.convertFileSrc( )`来解决这个问题，然后也使用 DOMSanitizer `bypassSecurityTrustUrl`函数:

```
import { DomSanitizer } from '@angular/platform-browser';

const resolvedImg = this.webview.convertFileSrc(storedPhoto);
const safeImg = this.sanitizer.bypassSecurityTrustUrl(resolvedImg); 
```

HTML 模板看起来像:

```
<img [src]="safeImg" /> 
```

了解了 Cordova 文件插件的工作原理后，我们就能够在 Ionic 应用程序中正确存储照片了。

## 更多资源查看

要查看本教程代码的 Cordova 和电容器版本，请查看在我们最近的[“在 Ionic Appflow 中构建电容器应用”网络研讨会](https://www.youtube.com/watch?v=tkgNuSG5FJQ)中提到的[演示应用](https://github.com/ionic-team/webinar-capflow)。

你可以在西蒙的 [Ionic 4 图像指南](https://devdactic.com/ionic-4-image-upload-storage/)和乔希的[使用电容文件系统 API 存储照片指南](https://www.joshmorony.com/using-the-capacitor-filesystem-api-to-store-photos/)中继续探索相机和文件插件。