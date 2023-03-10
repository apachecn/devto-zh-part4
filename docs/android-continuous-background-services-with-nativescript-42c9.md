# 带 NativeScript 的 Android 连续后台服务

> 原文：<https://dev.to/ozymandiasthegreat/android-continuous-background-services-with-nativescript-42c9>

于是我着手在 Android 上做专门的语音助手。退一步说，由于我与 Java 关系密切，而且我还没有时间玩 Kotlin，所以 NativeScript 似乎是一个显而易见的选择。

现在这是一项正在进行的工作，但我已经了解了很多关于 Android 的知识，我想与你分享我的一些发现。

首先，在这个任务中，我需要不断地倾听唤醒词，并做出相应的反应。在任何平台上实现这一点的显而易见的选择是某种后台服务或守护程序。

当谷歌搜索 nativescript 和后台服务时，一个优秀的教程和一个示例 repo 出现在顶部([我说的是这个](https://www.nativescript.org/blog/using-android-background-services-in-nativescript))。

唉，这是使用 IntentService，它只按计划运行，一旦任务完成就退出。虽然创建一个持续的后台服务很容易，但是这方面的例子很少(本文旨在解决这个问题)。

## 设置

您可以在此找到完整的工作示例报告[。](https://github.com/OzymandiasTheGreat/Nativescript-ServiceExample)

对于本文，我将假设我们正在使用
typescript hello_world 模板:
`tns create ServiceExample --ts --appid tk.ozymandias.ServiceExample`

适应其他模板/技术应该不难。

## 服务

首先在`app/`下新建一个子文件夹，姑且称之为`service`。这纯粹是为了保持你的项目结构干净整洁。
现在在`app/service/continuous_service.android.ts`下用这些内容
创建一个新文件

```
export const CONTINUOUS_SERVICE_CLASSNAME = "tk.ozymandias.ServiceExample.Continuous_Service";

@JavaProxy("tk.ozymandias.ServiceExample.Continuous_Service")
class Continuous_Service extends android.app.Service {
    private timerId: number;

    onBind(): android.os.IBinder {
        return null;
    }

    onCreate(): void {
        super.onCreate();
        console.log("SERVICE CREATED");

        if (!this.timerId) {
            this.timerId = setInterval(() => {
                console.log("PING");
            }, 1000)
        }
    }

    onStartCommand(intent: android.content.Intent, flags: number, startId: number): number {
        console.log("SERVICE STARTED");
        return android.app.Service.START_REDELIVER_INTENT;
    }

    onDestroy(): void {
        console.log("SERVICE DESTROYED");
        super.onDestroy();
        clearInterval(this.timerId);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在这是一个非常基本的服务，它只是在后台运行，每秒钟向控制台打印“PING”。

在顶部，我们将服务名导出为一个常量，稍后将在一些地方使用它。唉，你需要在至少两个地方将服务名指定为字符串文字。

第一个是显而易见的:注释。
在这里使用一个变量将会抛出关于现有扩展的错误，而不是变量值，它将是未定义的。

第二个会出现在清单上。稍后会详细介绍。

服务实例化时调用一次`onCreate`，每次服务启动时调用`onStartCommand`，服务退出时调用`onDestroy`。

服务如何启动和重启取决于你从`onStartCommand`返回什么
。你可能很想在这里返回`START_STICKY`，但是当你的应用程序被终止时，这将导致系统崩溃，因为系统将试图重启你的服务，并带有`null`的意图。

## 使之连续

到目前为止，我们有一个从你的应用程序开始的功能服务！但是，当应用程序退出或被杀死时，我们如何保持它的运行？

先做一个广播接收机吧。

```
import { CONTINUOUS_SERVICE_CLASSNAME } from "./continuous-service.android";

export const RESTART_RECEIVER_CLASSNAME = "tk.ozymandias.ServiceExample.Restart_Receiver";

@JavaProxy("tk.ozymandias.ServiceExample.Restart_Receiver")
class Restart_Receiver extends android.content.BroadcastReceiver {
    onReceive(context: android.content.Context, intent: android.content.Intent): void {
        console.log("RESTART INTENT RECEIVED");
        const serviceIntent = new android.content.Intent();
        serviceIntent.setClassName(context, CONTINUOUS_SERVICE_CLASSNAME);
        context.startService(serviceIntent);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，让我们稍微修改一下我们的服务，在退出时调用广播接收器，这样它就可以重新启动我们的服务。

```
// At the top
import { RESTART_RECEIVER_CLASSNAME } from "./restart-receiver.android";

// In the onDestroy method in our service
    onDestroy(): void {
        // ...
        const restartIntent = new android.content.Intent();
        restartIntent.setClassName(this, RESTART_RECEIVER_CLASSNAME);
        this.sendBroadcast(restartIntent);
    } 
```

Enter fullscreen mode Exit fullscreen mode

您还应该在我们的服务中实现`onTaskRemoved`方法。
当用户将你的应用从最近视图中移走时，它就会被调用。在这种情况下(可能还有其他情况)，默认情况下不调用`onDestroy`。
所以让我们通过调用`stopSelf`来调用`onDestroy`！

```
// ...
    onTaskRemoved(intent: android.content.Intent): void {
        console.log("TASK REMOVED");
        this.stopSelf();
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有一个持续运行的服务！当应用程序退出或被终止时，我们调用我们的广播接收器，
,它反过来重新启动我们的服务。

不幸的是，在较新版本的 Android 中，当系统
因为内存不足或电池优化而终止你的应用时，`onDestroy`不能保证被调用。

## 前台服务

幸运的是，有一个官方的方法来解决这个问题。我们需要的是让我们的服务成为前台服务。
缺点是我们必须提供一个持久的通知，但是从奥利奥开始，这个通知可以从系统设置
中隐藏，而不会影响我们的服务。

我们需要再次修改我们的服务，这次是
`onCreate`方法:

```
 // In the onCreate method in our service
    onCreate(): void {
        // ...
        const builder: android.app.Notification.Builder = new android.app.Notification.Builder(app.android.context);
        // Need to check api level, NotificationChannel is required but only available on Oreo and above
        if (android.os.Build.VERSION.SDK_INT >= 26) {
            const channel: android.app.NotificationChannel = new android.app.NotificationChannel(
                "persistence", "Service running indicator", android.app.NotificationManager.IMPORTANCE_LOW
            );
            const manager: android.app.NotificationManager = (<android.app.Activity>app.android.context).getSystemService(android.content.Context.NOTIFICATION_SERVICE);
            channel.enableLights(false);
            channel.enableVibration(false);
            manager.createNotificationChannel(channel);
            builder.setChannelId("persistence");
        }
        const notification: android.app.Notification = builder.build();
        this.startForeground(13, notification);
    } 
```

Enter fullscreen mode Exit fullscreen mode

这就形成了一个持续的前台服务，带有一个持久的通知，无论发生什么都可以保持运行(仍然可以从设置中强制停止)。

## 点睛之笔

现在，如果你尝试到目前为止的代码，它会崩溃。
那是因为我们还没有在
中声明任何东西`AndroidManifest.xml`！
我们需要声明的是我们需要的权限(仅在最新版本的 Android 上)、服务和接收者。

事不宜迟，清单如下:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="__PACKAGE__"
    android:versionCode="1"
    android:versionName="1.0">

    <supports-screens
        android:smallScreens="true"
        android:normalScreens="true"
        android:largeScreens="true"
        android:xlargeScreens="true"/>

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

    <application
        android:name="com.tns.NativeScriptApplication"
        android:allowBackup="true"
        android:icon="@drawable/icon"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">

        <activity
            android:name="com.tns.NativeScriptActivity"
            android:label="@string/title_activity_kimera"
            android:configChanges="keyboard|keyboardHidden|orientation|screenSize|smallestScreenSize|screenLayout|locale|uiMode"
            android:theme="@style/LaunchScreenTheme">

            <meta-data android:name="SET_THEME_ON_LAUNCH" android:resource="@style/AppTheme" />

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name="com.tns.ErrorReportActivity"/>
        <service android:enabled="true" android:name="tk.ozymandias.ServiceExample.Continuous_Service" />
        <receiver
            android:name="tk.ozymandias.ServiceExample.Restart_Receiver"
            android:enabled="true"
            android:exported="true"
            android:label="ContinuousServiceRestarter" />
    </application>
</manifest> 
```

Enter fullscreen mode Exit fullscreen mode

## 额外奖励

您可能已经注意到，我们收到的通知是
通用的“应用程序正在运行”通知，点击后会进入设置。
我们可以做得更好！

```
// In the onCreate method in our service
    onCreate(): void {
        // ...
        const appIntent: android.content.Intent = new android.content.Intent(app.android.context, com.tns.NativeScriptActivity.class);
        const pendingIntent: android.app.PendingIntent = android.app.PendingIntent.getActivity(app.android.context, 0, appIntent, 0);
        const builder: android.app.Notification.Builder = new android.app.Notification.Builder(app.android.context);
        builder
            .setContentText("Custom notification, F'Yeah!")
            .setSmallIcon(android.R.drawable.btn_star_big_on)
            .setContentIntent(pendingIntent);
        // ...
    } 
```

Enter fullscreen mode Exit fullscreen mode

您可能需要在文件顶部附近的某个地方
执行`declare const com: any;`操作，否则 typescript 可能会出错。

那么我们在这里做了什么？

我们创建了一个指向我们的应用程序的主要活动的待定意向，所以现在当通知被点击
时，它将打开你的应用程序。
至于通知选项，重要的位是
`setContentText`和`setSmallIcon`。如果这两个都不存在，您仍然会收到一个普通的
通知。

## 这就是所有的乡亲

这是我的第一篇文章，请温柔点。