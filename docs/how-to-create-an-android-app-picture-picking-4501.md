# 如何创建一个 Android 应用程序:图片采集

> 原文：<https://dev.to/edrome/how-to-create-an-android-app-picture-picking-4501>

### 再次嗨(x2)大家好！

上一篇文章我们讨论了日期选择器和姓名文本视图，因为我们已经查看了单选按钮，然后我们将检查个人资料照片更改。我们将使用 mikhaellopez 包中的圆形图像视图；Glide，一种加载和缓存图像的简单方法；Image Cropper 是一个非常有用的库，如果你想裁剪一个图像使它适合一个组件的话；德克斯特，一个非常简单的请求许可的方法。

让我们从应用构建 gradle
中的以下实现开始

```
implementation 'com.mikhaellopez:circularimageview:4.0.1'
implementation 'com.github.bumptech.glide:glide:4.9.0'
implementation 'com.karumi:dexter:5.0.0'
annotationProcessor 'com.github.bumptech.glide:compiler:4.9.0'
api 'com.theartofdev.edmodo:android-image-cropper:+' 
```

在我们的 content main 上，转到 text edition 并添加 CiruclarImageView 组件，定义宽度和高度属性的大小。还可以选择一个图像来可视化设计布局中的组件。

```
<com.mikhaellopez.circularimageview.CircularImageView
    android:id="@+id/profile_imageView"
    android:layout_width="100dp"
    android:layout_height="100dp"
    android:layout_marginStart="8dp"
    android:layout_marginLeft="8dp"
    android:layout_marginEnd="8dp"
    android:layout_marginRight="8dp"
    android:layout_marginBottom="8dp"
    android:contentDescription="@string/nav_header_desc"
    android:src="@drawable/baseline_person_outline_24px"
    android:onClick="showPictureDialog"
    app:layout_constraintBottom_toTopOf="@+id/profile_username_edittext"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toEndOf="@+id/profile_photo_textview" /> 
```

我将默认图像设置为圆形图像。我是从 [material.io](https://material.io/resources/icons/?icon=person_outline&style=baseline) 资源免费得到的。

该组件需要一个逻辑，允许用户拍照或从画廊中选择一张照片。该功能是通过使用 android imagecropper 完成的，附加的，进入清单，权限将被添加。

```
<manifest>

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.CAMERA" />

    <application>
        ....
        <activity
            android:name="com.theartofdev.edmodo.cropper.CropImageActivity"
            android:theme="@style/Base.Theme.AppCompat" />
    </application>

</manifest> 
```

为了保持项目的可读性，我们将创建一个名为 utils 的包，照片选择逻辑将位于这个包中。通过右键单击主包，然后单击新建和包，可以创建包。输入一个名称，在我的例子中是 utils。

[![Creating Package](img/30f72d0d32b1b08e9c592ae861d19d91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xtpVb6J5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y44zi8rytww2e5sjwh23.png)

一旦创建了包，再次右键单击 utils 包并选择 Java 类选项

[![Creating Class](img/522cab2792d7ea4df6abc6b80e214d85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kYAawbxJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t6v5ok40bqgpj0prp93g.png)

为这个类取一个名字(我的名字是 photo)，在这个过程之后，我们将写下一个小函数来调用对话框，让用户从各种来源中拍照或挑选照片。该方法是静态的，因为它不需要创建一个对象并将其存储在内存中。

```
public static void choosePhoto(Activity activity) {
    CropImage.activity()
             .setGuidelines(CropImageView.Guidelines.ON)
             .start(activity);
} 
```

随后，在主活动 java 类中，我们将添加下面解释的 severus 方法。

首先，单击圆形图像视图时要调用的方法。正如我在上一篇文章中所说的，要创建一个被称为 onClick 事件的方法，它必须是公共的、void 的，并带有一个类型为 View 的形参。在这个方法中，它将调用我们的方法向用户显示对话框，让用户选择照片的来源。

```
public void showPictureDialog(View view) {
    photo.choosePhoto(this);
} 
```

当图像被选中时，我们覆盖一个 onActivityResult 方法，该方法在选中时将图像放到圆形图像视图上。

```
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {

    super.onActivityResult(requestCode, resultCode, data);
    if (resultCode == this.RESULT_CANCELED) {
        return;
    } else if (requestCode == CropImage.CROP_IMAGE_ACTIVITY_REQUEST_CODE) {
        CropImage.ActivityResult result = CropImage.getActivityResult(data);
        if (resultCode == RESULT_OK) {
            Uri resultUri = result.getUri();
            try {
                Bitmap bitmap = MediaStore.Images.Media
                                 .getBitmap(this.getContentResolver(),resultUri);
                Glide.with(this).load(bitmap).into(imageview);
            } catch (IOException e) {
                e.printStackTrace();
            }
        } else if (resultCode == CropImage.CROP_IMAGE_ACTIVITY_RESULT_ERROR_CODE) {
            Exception error = result.getError();
        }
    }
} 
```

此外，用户需要给我们足够的权限来访问他的媒体内容。Dexter 是另一个通过实现一些方法来简化这个任务库。(我将解释最简单的部分，所以如果你想深入了解所有可用的函数，请参考它的文档，我将在本文末尾列出所有的库)

```
private void  requestMultiplePermissions(){
    Dexter.withActivity(this)
          .withPermissions(
                    Manifest.permission.CAMERA,
                    Manifest.permission.WRITE_EXTERNAL_STORAGE,
                    Manifest.permission.READ_EXTERNAL_STORAGE)
           .withListener(
               new MultiplePermissionsListener() {
                   @Override
                   public void onPermissionsChecked(MultiplePermissionsReport report) {
                       // check if all permissions are granted
                       if (report.areAllPermissionsGranted()) {
                           Toast.makeText(getApplicationContext(), 
                                       "All permissions are granted by user!", 
                                       Toast.LENGTH_SHORT).show();
                        }

//                      check for permanent denial of any permission
                        if (report.isAnyPermissionPermanentlyDenied()) {
                            // show alert dialog navigating to Settings

                        }
                    }

                    @Override
                    public void onPermissionRationaleShouldBeShown(
                                   List<PermissionRequest> permissions, 
                                   PermissionToken token) {
                        token.continuePermissionRequest();
                    }
                }).
                withErrorListener(new PermissionRequestErrorListener() {
                    @Override
                    public void onError(DexterError error) {
                        Toast.makeText(getApplicationContext(), 
                                      "Some Error! ", 
                                      Toast.LENGTH_SHORT).show();
                    }
                })
          .onSameThread()
          .check();
} 
```

我们使用主对象 Dexter，告诉他们除了读写存储之外，我们还需要 camera 上的权限。实现一个监听器是可能的，当用户拒绝访问或者你想向用户显示一个权限对话框时，你可以实现动作。最后，当一个错误发生时，你可以显示一个链接到更多例程的消息。我们刚刚创建的方法将在 onCreate 方法中被调用，重要的是要注意，你必须初始化图像视图，否则你将得到一个空指针异常。

就这样，现在你有了一个全功能的图片拾取器，可以用相机拍摄，也可以从内部/外部存储器中选择。

正如我所说的，这里是所有库文档的链接。

[Dexter](https://github.com/Karumi/Dexter)
T3】GlideT5[circular imageview](https://github.com/lopspower/CircularImageView)
[Image Cropper](https://github.com/ArthurHub/Android-Image-Cropper)
[material . io](https://material.io/develop/)

这是 github 项目的链接