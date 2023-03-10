# React Native -为 Android 添加应用图标

> 原文：<https://dev.to/jsjoeio/react-native-adding-app-icons-for-android-47e7>

最近开始涉猎 React Native 为新工作做准备。从 React 传来，事情感觉似曾相识。然而，感觉与任何与 web 相关的东西相比，那里的文档/文章/教程更少(这是有道理的——React Native 甚至没有发布 v1)。

我一直在做的事情之一是为我正在开发的 Android 版本的应用程序添加一个应用程序图标。这篇文章的目的是分享我到目前为止学到的一切，以便它可能会帮助你避免我遇到的任何挑战。

> 警告:如前所述，我是第一次对当地人做出反应，所以请对我在这里说的话有所保留。如果你发现一些不正确的地方，请在评论中告诉我。

所以让我们开始吧！🕺🏼

### 我需要知道什么？

为了将应用程序图标(通常称为“启动图标”)添加到 Android 版本的 React 原生应用程序中，您需要了解一些事情:

*   我的图标应该是方形还是圆形？
*   图标在我的应用程序中的位置？
*   我怎么做它们？

我们一次解决一个。

### 我的图标应该是方形还是圆形？

答案是*视情况而定*。

根据 OEM 设备，系统将决定是使用“方形”图标还是圆形图标。当我在 Pixel 2 模拟器上运行我的应用程序时，方形图标被放置在圆形图标中，看起来很丑🤮

看完文档后，我找到了解释为什么会发生这种情况的部分:

> 如果圆形遮罩需要不同的图标资源，例如，如果您的徽标的品牌依赖于圆形，则必须只使用 android:roundIcon 属性。

我使用的标志/图标是圆形的，因此我有一个问题。

除了方形和圆形图标，你可能还想创建一个“适应性图标”。根据 [Android 开发者文档](https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive)，Android 8.0 中引入了“自适应启动器图标”。截至本文撰写时，最新版本是 Android 9.0(也称为 Pie)。

总而言之，你可能两者都需要，这取决于你的图标的形状。你一定要看看适应性图标。

### 图标在我的应用程序中的位置？

幸运的是，这个答案非常简单。

假设您使用 React Native CLI 创建了项目，它们将位于此处:

```
|- android
|-- app
|--- src
|---- main
|----- res 
```

你会有一堆不同的目录，前缀是`mipmap`，后缀是不同的大小，比如`hdpi`和`mdpi`。

这些将包含不同大小的图标。

### 如何制作我的图标？

我推荐在图马中设计你的图标。您应该创建两个层:

1.  背景层-图标的背景
2.  一个前景层-图标

我在 108 x 108 的分辨率下创建了我的，然后以 1 倍的分辨率导出为 png。

然后我关注了 [@_s_farias](https://dev.to/_s_farias) 的帖子叫做[“如何使用 Android Studio 为 Android 创建自适应图标”](https://dev.to/_s_farias/how-to-create-adaptive-icons-for-android-using-android-studio-459h)。这使得创建图标变得非常容易，因为 Android Studio 为我生成了所有的图标，甚至创建了*大部分必要的子目录。😄*

*有一点需要注意:

*   我不得不在`res`中手动创建一个`drawable`目录。然后我把文件从`mipmap-xxxhdpi`复制到`drawable`目录。我不知道这是不是正确的，但是构建成功了，它工作了🤷🏼‍♂️(希望有人能解释正确的做法)。

### 常见错误

*   忘记更新`AndroidManifest.xml`文件

一开始，我遇到了错误，因为我忘记更新这个文件中的这两行:

```
 android:icon="@mipmap/ic_launcher"
      android:roundIcon="@mipmap/ic_launcher_round" 
```

*   `resource drawable/ic_launcher_foreground`未找到

您的构建失败，因为您没有创建`drawable`目录。请参见上一节中的“注意事项”。

无论如何-感谢阅读-希望这有助于你对原生 Android 图标的反应！🙏🏼

# 学习公开