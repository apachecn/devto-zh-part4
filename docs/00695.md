# 不要忘记构建那个应用程序📲

> 原文：<https://dev.to/atornblad/don-t-forget-to-build-that-app-2md6>

在本系列的第一部分，我描述了我如何以及为什么开始构建名为 *[叫妈妈](https://play.google.com/store/apps/details?id=se.atornblad.callmom)* 和 *[叫爸爸](https://play.google.com/store/apps/details?id=se.atornblad.calldad)* 的应用程序。这一部分描述了构建第一个有用版本所需的初始工作，包括一些 Java 指针。如果你只是想要摘要和一些链接，去底部的[摘要部分](#summary)。

# 不要忘了创建那个应用程序📲

为了这个任务，我用 Java 写了这个应用。学习对我来说完全陌生的技术，比如[科特林](https://dev.to/t/kotlin)或[反应土著](https://dev.to/t/reactnative)，当时并不是我的主要关注点，尽管我后来对这两者都有所了解。所以我安装了 [Android Studio](https://developer.android.com/studio) ，启动它，并开始一步步向前迈进。

从现代 Java 和 C#的世界来到 Java 7 *(我开始使用 Android Studio 时的默认 Java 版本)*感觉像是时光倒流了一大步。

*   没有[λ表达式](https://en.wikipedia.org/wiki/Anonymous_function)
*   困难的日期/时间支持
*   没有[流 API](https://winterbe.com/posts/2014/07/31/java8-stream-tutorial-examples/) ，甚至没有接近 [LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/) 及其集合扩展方法的东西
*   字符串上没有 switch 语句
*   没有用于局部变量的[类型推断](https://en.wikipedia.org/wiki/Type_inference)
*   [泛型中的类型擦除](https://en.wikipedia.org/wiki/Generics_in_Java#Problems_with_type_erasure)，这使得反射成为一场噩梦
*   没有[空合并](https://en.wikipedia.org/wiki/Null_coalescing_operator)或[空条件](https://en.wikipedia.org/wiki/Safe_navigation_operator)操作符
*   无值类型*(不可变数据结构)*
*   没有显式可空或不可空的变量声明

幸运的是，我能够通过在 Android Studio 中激活 Java 8 兼容性并在清单文件中将`minSdkVersion`设置为 API 级别 24 [来补救**其中的一些**。](https://developer.android.com/guide/topics/manifest/uses-sdk-element)

## 遨游未知

Android 已经存在了十多年，对于用户和开发者来说都是一个真正成熟的平台。不幸的是，这也意味着在[博客](https://www.sitepoint.com/writing-your-first-android-app-2/)、[视频教程](https://www.youtube.com/watch?v=JkwWWyJEIzw)和 [StackOverflow 问题](https://stackoverflow.com/questions/7830369/can-i-use-eclipse-to-create-android-apps)上有很多过时的信息。即使在官方文件中，当涉及到如何实现具体的事情时，也有矛盾和含糊不清的地方。一个版本的最佳实践很快就会遭到反对，或者在新版本的 Android SDK 中被弃用。同时，鼓励开发人员始终以最新版本为目标。

# 兼容早期 Android 版本🤜🤛

幸运的是，有一些兼容性库可以让开发人员瞄准前沿设备并使用新的功能，同时自动退回到旧的等效 API 或在旧设备上模拟新的行为。所以这个问题解决了。问题只是解决了两次。

## 支持库

当学习如何使用 [RecyclerView](https://developer.android.com/guide/topics/ui/layout/recyclerview) 和 [CardView](https://developer.android.com/guide/topics/ui/layout/cardview) 来允许用户从列表中选择正确的联系人进行呼叫时，我是根据我在官方文档中找到的内容，通过添加对[支持库](https://developer.android.com/tools/support-library/features.html#v7)的引用来完成的。有一段时间一切都很好，我使用支持库做了很多不同的事情，比如在所有支持的 Android 版本上正确显示通知。

后来，当我想添加持久数据存储时，我必须添加对 [AndroidX](https://developer.android.com/jetpack/androidx) 的引用。过了一会儿，编译器开始抱怨不同的`RecyclerView`实现之间的冲突。冲突来自于我在代码中引用这些类，Android Studio 要求自动添加`import`语句，以及我选择了错误的语句。

## Android Jetpack

最近，Android 开发已经看到了对各种事物的架构和标准化组件的大量改进，如数据存储、用户界面元素、通知、媒体和安全。与平台 API 分开， [Android Jetpack](https://developer.android.com/jetpack) 套件还包括如何实现版本兼容性的更新。从 [AndroidX 概述](https://developer.android.com/jetpack/androidx)页面:

> AndroidX 通过提供特性奇偶校验和新的库完全取代了支持库。

这一切都很好，但例如，在我写这篇文章的时候，`RecyclerView`的顶部搜索结果仍然指向旧版本的[。这是需要注意的事情。](https://developer.android.com/reference/android/support/v7/widget/RecyclerView)

如果你正在开发一个依赖于旧版本支持库的应用程序，有一些方法可以轻松自动地将[迁移到 AndroidX。根据我的经验，自动迁移工作得很好。此外，Android Studio 的新版本试图强迫*(甚至强迫)*你使用新的兼容性库。](https://developer.android.com/jetpack/androidx/migrate)

为了确保多个 Android 版本的用户体验一致，以下是一些需要考虑的技巧:

*   让你的活动延伸到`AppCompatActivity`而不是`Activity`:

```
import androidx.appcompat.app.AppCompatActivity;

public class MyActivity extends AppCompatActivity {
} 
```

Enter fullscreen mode Exit fullscreen mode

*   当存在合适的方法时，使用`ContextCompat`而不是直接调用`Context`方法:

```
import androidx.core.content.ContextCompat;

// Start other activities from inside an activity like this:
ContextCompat.startActivity(this, intent, options);
// And not like this:
this.startActivity(intent, options)

// Get some resources from inside an activity like this:
Drawable picture = ContextCompat.getDrawable(this, R.drawable.pic);
// And not like this:
Drawable picture = getDrawable(R.drawable.pic);

// Check permissions like this:
int permissionState = ContextCompat.checkSelfPermission(this, CALL_PHONE);
// And not like this:
int permissionState = checkSelfPermission(CALL_PHONE); 
```

Enter fullscreen mode Exit fullscreen mode

*   用`NotificationManagerCompat`代替`NotificationManager`:

```
import androidx.core.app.NotificationManagerCompat;

// Get the Notification Manager like this:
NotificationManagerCompat notificationManager = NotificationManagerCompat.from(this);
// And not like this:
NotificationManager = getSystemService(NOTIFICATION_SERVICE); 
```

Enter fullscreen mode Exit fullscreen mode

# 持久数据💾

为了处理用户选择给哪个联系人打电话以及通知频率的问题，我需要持久地存储数据，这样在应用程序重启之间就不会丢失任何东西。我还需要存储用户最近一次呼叫的时间，以便能够计算下一次通知的日期和时间。

起初，我用一个房间数据库来存储一切，最终创建了许多`AsyncTask`解决方案，以便在需要时主动读取数据或在用户输入后写入数据。这种方法是我在寻找答案时所能找到的。然而，使用 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) 方法来订阅整个应用程序中已更改的数据要高效和直接得多。此外，房间数据库可能不是应用程序需要存储的所有数据的最佳存储方式。

## 共享优先

当存储非常简单的数据时，比如单个字符串或数值，将它们保存在一个房间数据库中可能是多余的。读写房间数据不能在 UI 线程中完成，所以你必须使用`LiveData`、`AsyncTask`或其他异步机制来读取或存储值。

[shared preferences](https://developer.android.com/training/data-storage/shared-preferences)API 提供了一个键值存储，您可以直接从您的`Activity`代码中使用它，而无需生成工作线程或担心同步问题。要读取数据，首先调用`getSharedPreferences`方法。

```
// First open the preferences file called "prefs"
// If it doesn't exist, it gets created automatically
SharedPreferences prefs = getSharedPreferences("prefs", MODE_PRIVATE);

// Read an integer named "launches" from the preferences
// If that values doesn't exist, let it return zero
int numberOfAppLaunches = prefs.getInt("launches", 0);

// Read a string named "username"
// If that value doesn't exist, let it return null
String username = prefs.getString("username", null); 
```

Enter fullscreen mode Exit fullscreen mode

你的应用程序可以维护多个不同的首选项文件*(第一个参数`getSharedPreferences` )* 来分离数据组。在我的应用程序中，我还没有使用过这个功能，但它对避免名称冲突很有用。

要更新应用程序的`SharedPreferences`，首先需要创建一个`Editor`对象，将新值放入编辑器，并调用`apply()`，它会异步保存对首选项文件的更改，而不会干扰 UI 线程。

```
// Open the preferences file called "prefs"
SharedPreferences prefs = getSharedPreferences("prefs", MODE_PRIVATE);

// Create an Editor
SharedPreferences.Editor prefsEditor = prefs.edit();

// Update the value
numberOfAppLaunches += 1;
prefsEditor.putInt("launches", numberOfAppLaunches);

// Save changes to the preferences file
prefsEditor.apply(); 
```

Enter fullscreen mode Exit fullscreen mode

## 房间数据库

对于存储更复杂的数据，您应该考虑使用[Room persistence 库](https://developer.android.com/jetpack/androidx/releases/room)。这使您可以访问轻量级数据库引擎 [SQLite](https://www.sqlite.org/index.html) ，它隐藏在一个抽象层之后，帮助您专注于设计您的数据模型，而不是被更复杂的事情边缘化，如简单的`SELECT`查询之外的连接和 SQL 查询语法。与 [LiveData 架构](https://developer.android.com/topic/libraries/architecture/livedata)相结合，您将获得基于[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)的完全反应式数据流。

从定义数据类开始。每个数据类都被注释为一个`@Entity`，并转化为 SQLite 数据库中的一个表。这是一个简单的`MomData`实体类的样子:

```
import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity
public class MomData {
    @PrimaryKey(autoGenerate = true)
    public long id;

    public String name;
    public String number;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后定义您的数据访问方法。这些是 Java 接口，标注为`@Dao`，应该反映应用程序中的每一个数据用例，比如从数据库表中检索所有实例，通过 id 获取一个特定实例，搜索与某些输入匹配的实例，更新现有实例或将实体实例添加到数据库:

```
import androidx.room.Dao;
import androidx.room.Insert;
import androidx.room.OnConflictStrategy;
import androidx.room.Query;

@Dao
public interface MomDao {
    @Query("SELECT * FROM MomData")
    MomData[] getAllMoms();

    @Query("SELECT * FROM MomData WHERE id = :id")
    MomData getMomById(long id);

    @Query("SELECT * FROM MomData WHERE name = :whatName")
    MomData[] getAllMomsWithName(String whatName);

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    void addOrUpdate(MomData mom);
} 
```

Enter fullscreen mode Exit fullscreen mode

该数据访问接口允许您的应用程序:

*   用`getAllMoms`方法列出数据库中的所有 mom
*   通过使用 id 和`getMomById`方法获得一个特定的 mom
*   用`getAllMomsWithName`方法列出所有有特定名字的妈妈
*   用同样的`addOrUpdate`方法添加一个新的 mom，或者更新一个现有的 mom`@Insert`注释的`onConflict`参数告诉 Room，如果 id 匹配现有的行，就替换数据库中的行，或者如果`MomData`对象是新的，就创建一个新的行

如您所见，创建查询需要一些 SQL 知识，如果您发现自己需要更复杂的`JOIN`或`WHERE`子句，您可能想研究其他 ORM 解决方案，如[绿岛](http://greenrobot.org/greendao/)，它有一个基于 [QueryBuilder](http://greenrobot.org/greendao/documentation/queries/) 的概念。

最后，创建一个抽象类，它扩展了`RoomDatabase`类，为您正确处理连接:

```
import androidx.room.Database;
import androidx.room.RoomDatabase;

// Add all your app's entity classes to the entities array
@Database(entities = { MomData.class }, version = 1)
public abstract class CallMomDatabase extends RoomDatabase {
    // Create an abstract DAO getter for each DAO class
    public abstract MomDao getMomDao();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，要使用数据库，您需要创建一个`RoomDatabase.Builder`对象，如果数据库还不存在，它将创建数据库，并建立一个到它的连接:

```
// From inside a method in an Activity:
RoomDatabase.Builder<CallMomDatabase> builder =
    Room.databaseBuilder(this, CallMomDatabase.class, "callmomdb");
CallMomDatabase db = builder.build();

// Get a list of all moms
MomData[] allMoms = db.getMomDao().getAllMoms();

// Close the connection to clean up
db.close(); 
```

Enter fullscreen mode Exit fullscreen mode

然而，不允许从应用的 UI 线程执行任何数据库查询，这意味着上面的代码不能从任何类似`onClick`的方法调用。

我对此的第一个解决方案是创建许多`AsyncTask`实现，以便在我需要读取或写入数据库时创建新的工作线程。这基本上运行良好，但是我不得不自己考虑线程同步问题，这总是一件痛苦的事情。我不推荐用这种方式构建你的应用。当我了解到 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) 时，只需增加一点点代码，数据库连接就可以变得更干净、更健壮。

## LiveData——一个能看到风景的房间

确保你的应用程序的视图始终显示来自你的模型的正确数据可能是棘手的，尤其是当你不得不考虑[活动生命周期](https://developer.android.com/guide/components/activities/activity-lifecycle)的时候。你的`Activity`对象可以在你控制之外的任何时候被创建和销毁、暂停和恢复，甚至当用户做一件简单的事情，比如把他们的手机从纵向转向横向。知道何时、如何保存视图状态以及何时读回视图状态并不完全是小事。

幸运的是，Android Jetpack 提供了[生命周期感知组件](https://developer.android.com/topic/libraries/architecture/lifecycle.html)的概念，解决了这个问题的很大一部分。一个这样的组件是 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) ，它用于将可变值*(一个简单的值或对象)*包装在生命周期感知的可观察对象中。任何观察者，比如一个`Activity`或者一个`Fragment`，都会在它们生命周期中的正确时间，在需要的时候收到更新的值。尽管`LiveData`对象可以用于任何来源的任何类型的数据，但是它们对于处理存在于`Room`数据库中的实体特别有用。

首先，您需要重构`Dao`接口来利用`LiveData`机制。您需要将任何需要观察的数据的返回类型包装在一个`LiveData<>`泛型类中。

```
import androidx.lifecycle.LiveData;

@Dao
public interface MomDao {
    @Query("SELECT * FROM MomData")
    LiveData<MomData[]> getAllMoms();

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您应该创建一个`ViewModel`实现来包含视图需要呈现的所有数据。您可以在这里移动代码来构建您的`Database`对象，但是如果您的应用程序有多个`ViewModel`类，您可能希望将代码移动到某个帮助器方法并实现单例模式。

```
import androidx.lifecycle.LiveData;
import androidx.lifecycle.ViewModel;

public class MainActivityViewModel extends ViewModel {
    private LiveData<MomData[]> allMoms;
    private final CallMomDatabase database;

    public MainActivityViewModel() {
        RoomDatabase.Builder<CallMomDatabase> builder =
            Room.databaseBuilder(this, CallMomDatabase.class, "callmomdb");
            database = builder.build();
    }

    public LiveData<MomData[]> getAllMoms() {
        if (allMoms == null) {
            allMoms = database.getMomDao().getAllMoms();
        }
        return allMoms;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`database.close()`不再被调用。这是因为`LiveData`需要数据库连接保持打开。最后，在您的`Activity`中，您需要创建一个`Observer`来监听数据的变化，并相应地更新您的视图。针对 Java 8，最易读的方法是使用[方法引用](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)，在本例中是`this::allMomsChanged`引用:

```
import androidx.annotation.Nullable;
import androidx.lifecycle.ViewModelProviders;

public class MainActivity extends AppCompatActivity {
    private MainActivityViewModel model;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        // Get an instance of the view model
        model = ViewModelProviders.of(this).get(MainActivityViewModel.class);

        // Start observing the changes by telling what method to call
        // when data is first received and when data changes
        model.getAllMoms().observe(this, this::allMomsChanged);
    }

    private void allMomsChanged(@Nullable final MomData[] allMoms) {
        // This is where you update the views using the new data
        // passed into this method.
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Room`和`LiveData`之间的协作确保了无论何时数据库中的数据发生变化，上面的`allMomsChanged`方法都会被自动调用，以允许 UI 反映数据的变化。

# 设置报警⏰

提醒应用程序，如 *[叫妈妈](https://play.google.com/store/apps/details?id=se.atornblad.callmom)* 和 *[叫爸爸](https://play.google.com/store/apps/details?id=se.atornblad.calldad)* ，需要能够在特定时间提醒用户，即使他们的设备正在睡觉，并且即使设备重新启动，提醒也需要正确工作。android 中有一种机制叫做[警报管理器](https://developer.android.com/training/scheduling/alarms)，你可以用它来唤醒应用程序并按计划运行代码。`AlarmManager`类有许多不同的方法来设置这些闹钟，而`AlarmManagerCompat`可以帮助你以一种跨 Android 版本一致的方式设置闹钟。在选择使用哪种方法时，你需要小心，因为如果你的闹钟设计得不好，你的应用程序可能会耗尽设备的电池。

## 设置闹钟

我决定对这些应用程序使用`AlarmManagerCompat.setAlarmClock`方法，因为警报的主要目的是通知用户有预定的呼叫。`setAlarmClock`方法限制每 15 分钟最多发出一次警报，所以如果你的应用程序需要调度代码运行而不通知用户，或者需要每 15 分钟运行一次以上，你应该使用`AlarmManager`或`AlarmManagerCompat`类的一些其他方法，或者使用一些不同的方法。

```
import android.app.AlarmManager;
import android.app.PendingIntent;
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import androidx.core.app.AlarmManagerCompat;

public class MyAlarms extends BroadcastReceiver
    private AlarmManager alarmManager;
    private Context appContext;
    private final static int REQUEST_CODE = 1;

    // The current application context must be passed into this constructor
    public MyAlarms(Context appContext) {
        this.appContext = appContext;

        // Get the AlarmManager
        alarmManager = (AlarmManager)context.getSystemService(Context.ALARM_SERVICE);
    }

    public void setAlarm(long timeInMilliseconds) {
        // Create an intent that references this class
        Intent intent = new Intent(context, getClass());

        // Create a pending intent (an intent to be used later)
        // If an identical pending intent already exists, the FLAG_UPDATE_CURRENT
        // flag ensures to not create duplicates
        PendingIntent pendingIntent = PendingIntent.getBroadcast(
            appContext, REQUEST_CODE, intent, PendingIntent.FLAG_UPDATE_CURRENT);
        );

        // Set the alarm to call the onReceive method at the selected time
        AlarmManagerCompat.setAlarmClock(alarmManager, timeInMilliseconds, pendingIntent, pendingIntent);
    }

    @Override
    public void onReceive(Context context, Intent intent) {
        // This method will get called when the alarm clock goes off
        // Put the code to execute here
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

要设置闹铃，创建一个`MyAlarms`的实例并调用`setAlarm`方法，为所需的闹铃时间传入毫秒时间戳:

```
// From inside an Activity or a Service:
MyAlarms myAlarms = new MyAlarms(this);

// Set the alarm to go off after an hour
// An hour = 60 minutes * 60 seconds * 1000 milliseconds
long afterAnHour = System.currentTimeMillis() + 60 * 60 * 1000;
myAlarms.setAlarm(afterAnHour); 
```

Enter fullscreen mode Exit fullscreen mode

## 检测设备重启

使用`AlarmManager`的一个问题是，当用户重启设备时，所有预定的警报都会丢失。为了让闹铃在重启后也能正常工作，你的应用需要检测设备重启，当重启完成后，再次安排闹铃。这要求您在设置报警时，将报警时间保存在一些永久性存储器中，例如`SharedPreferences`，以便在检测到重启时从存储器中读取，并再次安排相同的报警。

操作系统向所有监听`BOOT_COMPLETED`动作的应用发送广播消息。要让您的应用程序得到通知，首先声明`RECEIVE_BOOT_COMPLETED`权限，并在`AndroidManifest.xml`文件中为您的接收器添加一个`intent-filter`:

```
<manifest ...>
  <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"> />
  ...

  <application ...>
    ...

    <receiver android:name=".MyAlarms" android:enabled="true">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
      </intent-filter>
    </receiver>

  </application>
</manifest> 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的`BroadcastReceiver`实现中，扩展`onReceive`方法来检查接收到什么类型的消息，并根据需要重新安排警报。另外，在安排闹钟时，将闹钟时间保存在 SharedPreferences 中。

```
public class MyAlarms extends BroadcastReceiver
    private AlarmManager alarmManager;
    private Context appContext;
    private final static int REQUEST_CODE = 1;
    private final static long TIME_NOT_SET = 0;

    public MyAlarms(Context appContext) {
        this.appContext = appContext;
        alarmManager = (AlarmManager)context.getSystemService(Context.ALARM_SERVICE);
    }

    public void setAlarm(long timeInMilliseconds) {
        Intent intent = new Intent(context, getClass());
        PendingIntent pendingIntent = PendingIntent.getBroadcast(
            appContext, REQUEST_CODE, intent, PendingIntent.FLAG_UPDATE_CURRENT);
        );
        AlarmManagerCompat.setAlarmClock(alarmManager, timeInMilliseconds, pendingIntent, pendingIntent);

        // Open shared preferences and save the alarm time
        SharedPreferences prefs = appContext.getSharedPreferences("alarms", Context.MODE_PRIVATE);
        SharedPreferences.Editor prefsEditor = prefs.edit();
        prefsEditor.putLong("alarmtime", timeInMilliseconds);
        prefsEditor.apply();
    }

    @Override
    public void onReceive(Context context, Intent intent) {
        // Check if this broadcast message is about a device reboot
        if (Intent.ACTION_BOOT_COMPLETED.equals(intent.getAction())) {
            // Yes it is! Get the last saved alarm time from shared preferences
            SharedPreferences prefs = appContext.getSharedPreferences("alarms", Context.MODE_PRIVATE);
            long savedAlarmTime = prefs.getLong("alarmtime", TIME_NOT_SET);

            // Is there a saved alarm time?
            if (savedAlarmTime != TIME_NOT_SET) {
                // Reschedule the alarm!
                setAlarm(savedAlarmTime);
            }
        }
        else {
            // This is not a device reboot, so it must be the alarm
            // clock going off. Do what your app needs to do.
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 显示通知🚩

这些应用的主要目的是在该打电话的时候通知用户。首先，你需要创建至少一个[通知通道](https://developer.android.com/training/notify-user/channels)，这样你的应用程序才能在 Android Oreo *(版本 26)* 和更高版本中运行。通过创建频道，用户可以根据内容允许或拒绝通知。确保为您的渠道提供好的名称和描述。

## 通知公司

通知是 Android 历史上变化很大的概念之一，因此根据用户设备运行的 Android 版本，有很多不同的处理方式。幸运的是，AndroidX 包含了`NotificationCompat`和`NotificationManagerCompat`类，可以减轻一些痛苦。

```
public class MyNotifications {
    private final static String CHANNEL_ID = "MAIN";
    private final static int ID = 12345;
    private final static int IMPORTANCE = NotificationManager.IMPORTANCE_DEFAULT;

    // You should definitely get the NAME and DESCRIPTION from resources!
    private final static String NAME = "Call reminders";
    private final static String DESCRIPTION = "These notifications remind you to call your mom";

    public void createChannel(Context context) {
        // Only do this if running Android Oreo or later
        if (Build.VERSION.SDK_INT <>= Build.VERSION_CODES.O) return;

        // Get the NotificationManager
        NotificationManager notificationManager = context.getSystemService(NotificationManager.class);

        // Create and configure the channel
        NotificationChannel channel = new NotificationChannel(CHANNEL_ID, NAME, IMPORTANCE);
        channel.setDescription(DESCRIPTION);
        channel.setShowBadge(true);
        channel.setLockscreenVisibility(Notification.VISIBILITY_PUBLIC);

        // Create the channel
        notificationManager.createNotificationChannel(channel);
    }

    // When a channel has been created, call this method to show the
    // notification, and pass a PendingIntent that will get started
    // when the user clicks the notification; preferably you will
    // pass an Activity intent to start.
    public void showNotification(Context context, String title, String text, PendingIntent intentToStart) {
        NotificationCompat.Builder builder = new NotificationCompat.Builder(context, CHANNEL_ID)
            .setSmallIcon(R.drawable.my_notification_icon)
            .setCategory(NotificationCompat.CATEGORY_REMINDER)
            .setContentTitle(title)
            .setContentText(text)
            .setContentIntent(intentToStart)
            .setOnlyAlertOnce(true)
            .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);

        NotificationManagerCompat notificationManager = NotificationManagerCompat.from(context);
        notificationManager.notify(ID, builder.build());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 总结🔖

*   为了兼容性和现代 UI 元素，忽略旧的支持库，使用 [AndroidX](https://developer.android.com/jetpack/androidx)
*   使用 [SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences) 实现简单的键值持久存储
*   用[室](https://developer.android.com/training/data-storage/room)做更复杂的持久数据存储
*   通过 [LiveData](https://developer.android.com/topic/libraries/architecture/livedata) 使用房间实体和其他应用程序状态
*   为了使警报在设备重启后仍然有效，请收听 [BOOT_COMPLETED 消息](https://developer.android.com/reference/android/content/Intent.html#ACTION_BOOT_COMPLETED)
*   使用 [NotificationCompat](https://developer.android.com/guide/topics/ui/notifiers/notifications#compatibility) 正确显示通知

*封面照片由[达莉亚·内布里亚希娜](https://unsplash.com/photos/pz-TH3jzic8)在[Unsplash](https://unsplash.com/)T5 拍摄*