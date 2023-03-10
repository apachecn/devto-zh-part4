# 您的第一个 Android 库

> 原文：<https://dev.to/robotsquidward/your-first-android-libraries-47k9>

当你在 Android Studio 中启动一个全新的应用程序时，你就陷入了一个可能只有一个单一的空活动的基本项目中。

如果你试图构建一个超越“`Hello World!`”的项目，特别是如果你计划遵循一些架构上的最佳实践，比如使用 MVVM，那么你很快就会遇到每个项目中包含的基本 Android 库的一些限制。

在创建了一些基本的入门应用程序后，我想我应该分享一下我认为添加到您的 Android 项目中的最佳“第一步”依赖项。

## TL；博士- `app/build.gradle`

在我进入更多细节之前，这里是我为上一个入门应用程序整理的基本`dependencies`列表。

```
dependencies {

    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation"org.jetbrains.kotlin:kotlin-stdlib-jdk7:1.3.31"
    implementation 'androidx.appcompat:appcompat:1.0.2'
    implementation 'androidx.core:core-ktx:1.0.2'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    // enable ViewModelProviders
    implementation "androidx.lifecycle:lifecycle-extensions:2.0.0"
    // enable TextInputLayout
    implementation 'com.google.android.material:material:1.0.0'

    testImplementation 'junit:junit:4.12'
    // enable Mocking with Mockito
    testImplementation 'org.mockito:mockito-core:2.19.0'
    // enable InstantTaskExecutorRule
    testImplementation 'androidx.arch.core:core-testing:2.0.1'

    androidTestImplementation 'androidx.test:runner:1.2.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
    // non-deprecated AndroidJUnit4
    androidTestImplementation 'androidx.test.ext:junit:1.1.1'

} 
```

Enter fullscreen mode Exit fullscreen mode

我评论的行是我添加到 Android Studio 3.4 中基本 Android 空白活动启动应用程序中的行。

让我们仔细看看这些库是什么，以及我们为什么需要它们。

## 产品代码

关键字`implementation`指定了实际运行的产品代码中的依赖关系。

### 生命周期扩展

为 Android [生命周期组件](https://developer.android.com/topic/libraries/architecture/lifecycle)启用一些方便的扩展。即使用 [`ViewModelProviders`](https://developer.android.com/reference/android/arch/lifecycle/ViewModelProviders) 的能力。

```
implementation "androidx.lifecycle:lifecycle-extensions:2.0.0" 
```

Enter fullscreen mode Exit fullscreen mode

任何 Android 项目都需要一个架构模式来遵循。[谷歌推荐将 MVVM](https://developer.android.com/jetpack/docs/guide) 与安卓[的 ViewModel](https://developer.android.com/topic/libraries/architecture/viewmodel) 配合使用。`ViewModelProviders`使您能够以 Google 认可的方式在您的活动和片段中快速设置`ViewModel`。

### 材料

没有许多[材料设计组件](https://material.io/develop/android/)你也能应付过去，但是使用它们更容易。它们支持一些 Android 用户习惯看到的非常有用的 UI 元素。

```
implementation 'com.google.android.material:material:1.0.0' 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，这个库允许我使用 [`TextInputLayout`](https://developer.android.com/reference/com/google/android/material/textfield/TextInputLayout) 作为我的文本字段。这使得内置的错误字段，动画提示，和材料第一的设计，我想在我的应用程序。

## 单元测试

使用`testImplementation`为你的单元测试指定依赖关系。默认情况下包含 JUnit。

### Mockito

任何 Android 初学者项目中包含的`ExampleUnitTest`都不使用 [Mockito](https://site.mockito.org/) 进行测试。然而，随着项目的增长，您会很快意识到一个成熟的模仿库的好处。

```
testImplementation 'org.mockito:mockito-core:2.19.0' 
```

Enter fullscreen mode Exit fullscreen mode

Mockito 使您能够使用`MockitoJUnitRunner`并在测试中模拟数据和函数调用。这确实有助于减少 boiler plate，并在单元测试中关注正确的功能。

### 生命周期测试

如果你正在做正确的事情，并且在 Android 架构组件方面跟随 MVVM，那么你可能想要测试你的`ViewModel`和`LiveData`。

```
testImplementation 'androidx.arch.core:core-testing:2.0.1' 
```

Enter fullscreen mode Exit fullscreen mode

核心测试库使您能够使用`InstrumentationTestRegistry`规则在 JUnit 测试中测试`LiveData`。

```
/**
 * Enables Architecture Components testing in JUnit tests.
 * (Include this at the top of your Unit Test class body.)
 */
@get:Rule
var task = InstantTaskExecutorRule() 
```

Enter fullscreen mode Exit fullscreen mode

## UI 测试

`androidTestImplementation`关键字指的是 UI/仪器测试依赖关系。

### 安卓 JUnit

虽然使用`AndroidJUnit4`并不需要这个，但是这个依赖项包含了最新的、没有被否决的版本。

```
androidTestImplementation 'androidx.test.ext:junit:1.1.1' 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

希望这份指南已经帮助你确定了几乎所有 Android 项目都需要的一些基础依赖。

不可能规定你在下一个 Android 项目中探索无限可能性需要哪些依赖。接下来的事就看你的了。

<small>本文转载自我的博客， [AJ.dev](https://ajkueterman.dev) - [看那里](https://ajkueterman.dev/posts/your-first-android-libraries/)。</small>