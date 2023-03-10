# 如何从 React Native 0.57 升级到 0.59 |第 1 部分:升级到 0.58

> 原文：<https://dev.to/julietter/how-to-upgrade-from-react-native-0-57-to-0-59-part-1-upgrading-to-0-58-492i>

[![](img/6efd1beb915ff919fc3006634c829a52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7g2QxAOo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Atg4oPcJpJ7dOTE9x0WstQg.jpeg)

#### 嘿你！你需要升级到 0.59！📢

如果你还不知道，如果你想向 Google Play 商店提交更新，所有 React 原生应用都需要在 2019 年 8 月 1 日之前升级到 0.59。这是因为 Google Play 提出了新的 64 位要求。他们对此相当认真:

> 到 2019 年 8 月 1 日，所有使用本机代码的应用程序必须在 32 位版本之外提供 64 位版本，才能发布更新。今年 1 月，我们重申这是必要的，以便为创新让路，并预计未来的 Android 设备仅支持 64 位代码。

幸运的是，React Native 在他们的`0.59`版本中引入了 64 位版本。但问题是，更新起来可能有点棘手。这就是我创建这个教程的原因。如果您想让您的应用程序加快速度，并为即将发布的版本做好准备，请跟随。

#### 关于本教程

在我的情况下，我需要将一个项目从`0.57.4`升级到`0.59.8`(注意:一个更新的`0.59.10`已经发布)。我还需要 Android 和 iOS 版本。

当用两个次要版本升级一个项目时，**我强烈建议一次升级一个次要版本。**(例如`0.57`到`0.58`，然后`0.58`到`0.59`)。如果出现问题，它会简化故障排除。

因此，如果你需要像我一样从`0.57`向上爬，我向你展示**第 1 部分，共 2 部分:升级到 0.58！**

#### 第 1 部分:升级反应原生 0.58 ⬆️

#### 🔑主要变化

*   iOS JavaScriptCore 框架现在需要链接到我们的应用程序。
*   现在支持 Android 的 target 27 SDK。

#### 第一步:更新你的 package.json ⬆️

打开您的`package.json`并更新以下依赖项:

```
//  package.json  "dependencies":  {  "react":  "16.6.3",  "react-native":  "0.58.6",  },  "devDependencies":  {  "babel-core":  "^7.0.0-bridge.0",  "react-test-renderer":  "16.6.3",  } 
```

然后，删除您的`node_modules`并用`npm i`重新安装一个新批次。

#### 第二步:现代化巴别塔🗣

看到上面那个巴别塔升级了吗？版本 7 引入了一种新类型的 Babel 配置文件，这种文件现在更受欢迎，甚至是您可能使用的许多库所必需的。

要更新:

**1。**创建一个新的`babel.config.js`文件。

**2。**移植当前`.babelrc`文件中的任何配置。现在需要导出这些预设，如下例所示。

```
// a basic react native babel.config.js

module.exports = {
     presets: ["module:metro-react-native-babel-preset"]
} 
```

**3。**完成后，删除旧的`.babelrc`文件。

#### 第三步:更新流量⬆️

这里有一个简单的。打开`.flowconfig`并更新流程依赖关系:

```
// .flowconfig

[version]
^0.92.0 
```

如果你使用 Flow 并在这次更新后遇到错误，去他们的 [changelog](https://github.com/facebook/flow/blob/master/Changelog.md) 诊断任何问题。

#### 第四步:链接 JavaScriptCore 框架🔗

**1。**打开你的 XCode 项目。

**2。**选择您的项目:

[![](img/4ba53ef2c00aa03b522ecf5f3a25e6f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1Pykbc79--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Ae-16F4qmNUIbdl6V9JIw9w.png)

**3。**选择您项目的主要目标设置:

[![](img/326199c9189d1d95e48032db1ef183a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A-A0XXjh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2AQAip1dbngymFpYK22VdXyg.png)

**4。**导航至`Build Phases`屏幕:

[![](img/178475a17deffe6e1fe17e4390598520.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RjBO0ZXg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2A3ZhaeWwxJu1CMueVGwU1qA.png)

**5。**在`Link Binary With Libraries`下，增加`JavaScriptCore.framework`

[![](img/9e5e6a60ec8a17ce7a1375a7447c21ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_3e3unMB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Ax7LhxNbYVVm7xVaKLCEHHg.png)

[![](img/212543badea64e98993eabc189df55ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wy6-tk8F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Au6Aqk3Z114iQHG37roeuGw.png)

请注意，在此更改后，您可能需要清理 XCode 缓存。这里有一篇文章可以帮到你。

#### 第五步:更新`android/build.gradle` ⬆️

更新`android/build.gradle`以支持一些新的库/SDK。不要删除任何内容，只更新以下版本号:

```
// android/build.gradle

buildscript {
     ext {
          buildToolsVersion = "28.0.2"
          compileSdkVersion = 28
          targetSdkVersion = 27
          supportLibVersion = "28.0.0"
     }

     dependencies {
          classpath 'com.android.tools.build:gradle:3.2.1'
     }

     task wrapper(type: Wrapper) {
          gradleVersion = '4.7'
     }
} 
```

#### 第六步:更新格拉德·⬆️

使用新的 Gradle 版本 4.7(在上一步中更新)将要求您更新`android/gradle/wrapper/gradle-wrapper.properties`

```
distributionUrl=https\://services.gradle.org/distributions/gradle-4.7-all.zip 
```

#### 第七步:更新`android/app/build.gradle` ⬆️

在`0.58`年，React Native 开始推出 64 位 Android 版本。要添加此生成类型:

**1。**首先，删除`ndk`部分。它将不再被使用:

```
android {
     defaultConfig {
          // Remove the following:
          ndk {
               abiFilters "armeabi-v7a", "x86"
          }
     }
} 
```

**2。**将`“arm64-v8a”`架构添加到以下列表中:

```
android {
  splits {
    abi {
      include "armeabi-v7a", "x86", "arm64-v8a"
    }
  }
  buildTypes {
    variant.outputs.each { output ->
      def versionCodes = ["armeabi-v7a":1, "x86":2, "arm64-v8a": 3]
    }
  } 
```

**3。**最后，由于我们只升级到 React Native `0.58.6`，我们必须指定 Android 应该使用的 React Native 依赖项:

```
dependencies {
     // implementation "com.facebook.react:reactnative:+"
     implementation "com.facebook.react:react-native:0.58.6"
} 
```

#### 步骤 8:修改 Android 构建脚本🚧

对于那些使用 Buck 的人来说，构建脚本已经有了一点改变:

**1。**在`android/app/BUCK`中，用以下内容替换`jarfile` / `for aarfile`循环的:

```
### OLD CODE lib_deps = []

for jarfile in glob(['libs/*.jar']):
  name = 'jars__' + jarfile[jarfile.rindex('/')+ 1: jarfile.rindex('.jar')]
  lib_deps.append(':' + name)
  prebuilt_jar(
    name = name,
    binary_jar = jarfile,
  )

for aarfile in glob(['libs/*.aar']):
  name = 'aars__' + aarfile[aarfile.rindex('/') + 1: aarfile.rindex('.aar')]
  lib_deps.append(':' + name)
  android_prebuilt_aar(
    name = name,
    aar = aarfile,
 )

### NEW CODE load(":build_defs.bzl", "create_aar_targets", "create_jar_targets")

lib_deps = []

create_aar_targets(glob(["libs/*.aar"]))

create_jar_targets(glob(["libs/*.jar"])) 
```

**2。**现在，创建一个新的`android/app/build_defs.bzl`文件:

```
# android/app/build_defs.bzl 
"""Helper definitions to glob .aar and .jar targets"""
def create_aar_targets(aarfiles):
    for aarfile in aarfiles:
        name = "aars__" + aarfile[aarfile.rindex("/") + 1:aarfile.rindex(".aar")]
        lib_deps.append(":" + name)
        android_prebuilt_aar(
            name = name,
            aar = aarfile,
        )
def create_jar_targets(jarfiles):
    for jarfile in jarfiles:
        name = "jars__" + jarfile[jarfile.rindex("/") + 1:jarfile.rindex(".jar")]
        lib_deps.append(":" + name)
        prebuilt_jar(
            name = name,
            binary_jar = jarfile,
        ) 
```

#### 第九步:检查你的裁判👀

`<ScrollView>`、`<CameraRollView>`和`<SwipeableRow>`都得到了一些更新，如果你在这些组件中使用任何引用，可能会给你带来麻烦。

这在我的项目中不是问题，但如果对你来说是，对这些组件使用`createRef()`应该能让你顺利完成。更多信息可以在这里找到[。](https://github.com/react-native-community/releases/issues/81#issuecomment-459252692)

#### 第十步:新的圆形安卓图标！👏

最后但同样重要的是，现在支持圆形 Android 图标！这里有一篇关于如何创造这些的文章。创建完这些文件后，将它们添加到您的`android/app/src/main/AndroidManifest.xml`文件中:

```
<manifest...
     <application...
          android:roundIcon="@mipmap/ic_launcher_round" 
```

#### 第十一步:测试，测试，测试🧪

构建您的应用程序。确保在实际的 iOS 和 Android 设备上运行它们。看到任何新的反对警告吗？最好现在就把他们消灭在萌芽状态。其中一些警告可能来自您的依赖项。看看是否有任何更新。如果没有，您可能需要自己修补。

**修补软件包**🚑

需要打个包补丁？我最喜欢的方法是用[补丁包](https://github.com/ds300/patch-package)。这是一个库，它区分你对一个`node_modules`包所做的更改，保存这些更改，并在每次运行`npm i`时应用这些更改。这里有一个快速教程:

**1。**跑`npm i patch-package`

**2。**将这个安装后脚本添加到您的`package.json`:

```
"scripts":  {  "postinstall":  "patch-package"  } 
```

**3。**进入你的`node_modules`，对你的目标依赖做出任何你需要的改变。

**4。**一旦完成，运行`npx patch-package package-name`。这将为该特定包创建一个补丁文件。您应该将这些文件提交到项目中。

**5。**现在，每当您删除`node_modules`时，您的补丁将在您运行`npm i`后添加。🙌

#### 下一步

为你的项目工作？恭喜你。🎉你已经成功一半了！

敬请关注第 2 部分:升级到 React Native `0.59`！

[![](img/17882124307df624eb816a32c3aa5336.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sZP_0Ld0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/laychg7tuxkzt61a0jpv.png)

👋嗨！我是[朱丽叶](http://www.julietterapala.com)。我在 [Eventric](https://www.eventric.com/) 工作，是一名软件开发员。请在推特上关注我，地址是 [@Juliette](https://twitter.com/JulietteR) 。