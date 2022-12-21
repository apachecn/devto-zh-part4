# 如何轻松实现新的应用内更新功能到您的 Android 应用

> 原文：<https://dev.to/sanojpunchihewa/how-to-easily-implement-new-in-app-update-feature-to-your-android-app-9e9>

通宵工作来修复一个错误或为你的 Android 应用程序添加一个突破性的功能，却看到只有少数用户更新了他们的应用程序？现在不用担心，Android 现在发布了一个名为[应用内更新](https://developer.android.com/guide/app-bundle/in-app-updates)的新功能，你可以提示用户更新应用。

[![](img/4de5f37b16e20c11ef4a8b1c65ad8f7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5vrqwMR_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5mg3mnkl2dmrxnf6wgly.png) 
*来源:[https://developer . Android . com/guide/app-bundle/in-app-updates](https://developer.android.com/guide/app-bundle/in-app-updates)*

> 虽然有些用户在其设备连接到未计量的连接时会启用后台更新，但可能需要提醒其他用户进行更新。应用内更新是 Play 核心库的一项功能，它引入了一个新的请求流来提示活跃用户更新您的应用。

—[https://developer . Android . com/guide/app-bundle/in-app-updates](https://developer.android.com/guide/app-bundle/in-app-updates)

作为一名 Android 开发人员，我觉得把所有这些代码添加到我的应用程序中很累。因此，我开发了一个[库](https://github.com/SanojPunchihewa/InAppUpdater),它将把应用内更新功能减少到 5 行代码。

**我们开始吧！**

### 实现

#### 第一步:将 jitpack 添加到您的根级 build.gradle 中，放在存储库的末尾

```
allprojects {
    repositories {
        maven { url "https://jitpack.io" }
    }
} 
```

#### 第二步:将依赖关系添加到 app 级 build.gradle

```
dependencies {
    implementation 'com.github.SanojPunchihewa:InAppUpdater:1.0.2'
} 
```

#### 步骤 3:在活动的 onCreate 方法中初始化 UpdateManager

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    // Initialize the UpdateManager
    UpdateManager.Builder().mode(UpdateManagerConstant.IMMEDIATE).start(this);

} 
```

有灵活和即时两种更新模式，

*   灵活(`UpdateManagerConstant.FLEXIBLE`)(默认)—用户可以在更新下载期间使用应用程序，安装和重启需要由用户触发
*   立即(`UpdateManagerConstant.IMMEDIATE` )—用户将被阻止，直到下载和安装完成，自动触发重新启动

#### 第四步:调用 onResume 方法中的 continueUpdate 方法安装等待更新

```
@Override
protected void onResume() {
    super.onResume();
    UpdateManager.continueUpdate(this);
} 
```

***Tadaa！就是这样。现在你的安卓应用*** 有了应用内更新

如果这对你有帮助，请给这篇文章和⭐️的[库](https://github.com/SanojPunchihewa/InAppUpdater)一些❤️

自从我最近发布了[库](https://github.com/SanojPunchihewa/InAppUpdater)😉，如果您发现任何错误或有任何建议，请提出[问题](https://github.com/SanojPunchihewa/InAppUpdater/issues)

另外，如果你觉得工作很难，请留下评论，我很乐意帮助你😃