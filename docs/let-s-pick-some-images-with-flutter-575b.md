# 让我们用 Flutter 挑选一些图像

> 原文：<https://dev.to/pedromassango/let-s-pick-some-images-with-flutter-575b>

在这篇短文中，我想和你分享如何使用 Flutter 团队插件在 Flutter 中拾取图像。

我们将使用由 Flutter 团队制作的`image_picker`包(这意味着我们完全支持 Android 和 iOS 平台)...

1.  在 pubspec.yaml 中添加`image_picker`包:

```
dependencies:
  image_picker: ^0.6.1+4 
```

Enter fullscreen mode Exit fullscreen mode

1.  导入包。

```
import 'package:image_picker/image_picker.dart'; 
```

Enter fullscreen mode Exit fullscreen mode

基本上我们需要做的是选择图像源(相机或图库)并将其传递给`ImagePicker.pickImage()`的函数。建议为您的用户提供选择他们想从哪里挑选的能力。

因此，让我们创建一个属性来存储选中的图像:

```
File _pickedImage; 
```

Enter fullscreen mode Exit fullscreen mode

下一步是显示一个带有标题和两个按钮的对话框，一个用于从相机中选择图像，另一个用于从图库中选择图像。当用户点击其中一个按钮时，我们需要返回图像源，我们通过弹出结果(`Navigator.pop(context, ImageSource.camera)`)来完成。

```
final imageSource = await showDialog<ImageSource>(
    context: context,
    builder: (context) =>
        AlertDialog(
          title: Text("Select the image source"),
          actions: <Widget>[
            MaterialButton(
              child: Text("Camera"),
              onPressed: () => Navigator.pop(context, ImageSource.camera),
            ),
            MaterialButton(
              child: Text("Gallery"),
              onPressed: () => Navigator.pop(context, ImageSource.gallery),
            )
          ],
        )
); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了用户选择的图像源，我们必须调用`pickImage`函数，它将显示 gallery/come 来获取图像。

```
if(imageSource != null) {
  final file = await ImagePicker.pickImage(source: imageSource);
  if(file != null) {
    setState(() => _pickedImage = file);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们检查用户是否通过点击对话框的一个按钮选择了一个图像源，通过检查图像源是否不为空，如果不为空，那么我们使用库来获取图像。如果用户没有选择任何图像，`pickImage`将返回选中的图像文件或 null。

注意:这个库已经为最新的 android 版本请求了权限，你不需要担心这个。

下面是完整的函数代码:

```
void _pickImage() async {
  final imageSource = await showDialog<ImageSource>(
      context: context,
      builder: (context) =>
          AlertDialog(
            title: Text("Select the image source"),
            actions: <Widget>[
              MaterialButton(
                child: Text("Camera"),
                onPressed: () => Navigator.pop(context, ImageSource.camera),
              ),
              MaterialButton(
                child: Text("Gallery"),
                onPressed: () => Navigator.pop(context, ImageSource.gallery),
              )
            ],
          )
  );

  if(imageSource != null) {
    final file = await ImagePicker.pickImage(source: imageSource);
    if(file != null) {
      setState(() => _pickedImage = file);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们展示我们的形象。

```
Center(
  child: _pickedImage == null ?
  Text("No Image") :
  Image(image: FileImage(_pickedImage)),
) 
```

Enter fullscreen mode Exit fullscreen mode

这就是图像...

### 视频呢？

这个库也支持挑选视频。你需要做的就是调用`ImagePicker.pickVideo(source_here)`。来源与`pickImage`功能相同。

如果您使用`pickVideo`，请确保不要使用`Image`小工具，因为它只是用来显示图像的。您可以使用[video _ player](https://pub.dev/packages/video_player)插件播放视频。

在这个库中找到完整的代码。

我希望你喜欢这篇文章。下一集见。