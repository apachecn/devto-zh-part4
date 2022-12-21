# Android 中的 StrictMode:为什么、何时以及如何使用

> 原文：<https://dev.to/pedromassango/strictmode-no-android-porque-quando-e-como-utilizar-2h50>

(从“minha conta(我的介质)”移动的文章)

你有没有想过用什么工具来保护你？当你不小心做错了什么事会提醒你
？那就了解严谨模式。

**【严格模式】**是一种工具，它可以检测到您可能会在
意外中做的某些事情，并提醒您这些故障，以便您解决这样的
故障。

StrictMode 最常用于检测主应用线程中的数据库访问或网络访问
。

**注意:**严格模式只应在开发时使用。

### Como usar？

要启用和启用严格模式，我们必须从您的活动或 application 类中运行以下代码:
**[oncreate()**:T3]

```
public void onCreate() {
     super.onCreate();

     if (DEVELOPER_MODE) {
         StrictMode.setThreadPolicy(new StrictMode.ThreadPolicy.Builder()
                 .detectDiskReads()
                 .detectDiskWrites()
                 .detectNetwork()   // ou .detectAll()
                 .penaltyLog()
                 .build());
         StrictMode.setVmPolicy(new StrictMode.VmPolicy.Builder()
                 .detectLeakedSqlLiteObjects()
                 .detectLeakedClosableObjects()
                 .penaltyLog()
                 .penaltyDeath()
                 .build());
     }
 } 
```

Enter fullscreen mode Exit fullscreen mode

以下是上述一些方法的说明:

#### 线程策略

用于检测
应用程序的父线程上的任务执行违规。

**检测磁盘写入()/检测磁盘写入()**捕获主用户界面线程中的读写违规
。

**检测()**用于捕获所有应用程序线程违规。

#### 虚拟机策略

用于在虚拟机的所有线程上强制实施策略。

*   **检测到 leaked qliteobjects()**检测到 SQLite 游标在未关闭的情况下完成。
*   **检测 league closed objects()**检测具有明确终止方法的对象何时结束而不关闭。
*   **检测()**用于捕获所有应用程序线程违规。

您可以通过调用以下任一函数来决定检测到违规时应如何处理:
:

*   **【penaltylong()**在 Android Studio 的 LogCat 窗口中呈现违规情况，正如它们将要出现一样。
*   **penaltyddialog()**每当侦测到违犯时，都会在萤幕上显示对话方块。
*   **penaltyDeath()** 杀死启用严格模式的应用程序进程。

### Nota:

不要觉得有义务修正所有发现的严谨模式，在
活动的正常生命周期中，通常需要很多
的资料存取案例。

使用严谨模式寻找你偶然做的事情。
主线程上的网络接入几乎总是一个问题，在这种情况下，你必须想办法处理它们。

#### **迪卡:**

在您的项目中始终使用严谨模式，它大大提高了您的应用程序的“T0”性能，有助于发现“T1”项目中的问题和故障。