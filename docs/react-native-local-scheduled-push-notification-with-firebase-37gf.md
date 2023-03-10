# 使用 Firebase 对本地、本地、预定、推送通知做出反应

> 原文：<https://dev.to/vikrantnegi/react-native-local-scheduled-push-notification-with-firebase-37gf>

随着应用程序使用量的不断增加，应用程序开发人员试图找到新的方法来让用户参与到他们的应用程序中。推送通知是让你的用户参与进来并获得信息的一种方式。

推送通知是应用程序用来推动用户再次参与和保留的沟通渠道。推送通知技术已经从单一的消息传递系统发展成为丰富的交互式媒体。

有两种类型的通知，远程和本地通知。

### 远程通知

使用远程通知，您可以使用公司的一台服务器，通过 Apple 推送通知服务(FCM)将数据推送到用户设备。

### 本地通知

Android 和 IOS 都支持从本地应用程序触发通知的能力。这些可以立即显示，也可以安排在以后显示。

在本文中，我们将使用本地通知功能在每天的特定时间向用户发送每日提醒。

## 先决条件

本教程需要 React Native 的基础知识。要设置您的开发机器，请遵循这里的官方指南。

为了在 React 本机应用程序中实现推送通知，我们将使用 [react-native-firebase](https://github.com/invertase/react-native-firebase) 。

React Native Firebase 具有通知模块，支持远程(FCM)和本地通知。

为了确保我们在同一页上，以下是本教程中使用的版本:

*   节点 v10.15.0
*   国家预防机制 6.4.1
*   纱线 1.16.0
*   react-原生 0.59.9
*   react-native-firebase 5.2.3

## 入门

要使用`react-native-cli`创建新项目，请在终端中键入以下内容:

```
$ react-native init localReminders 
```

```
$ cd localReminders 
```

#### 安装 React Native Firebase

按照此处的安装说明[安装`react-native-firebase`。](https://rnfirebase.io/docs/v5.x.x/installation/initial-setup)

确保您已经完成了 iOS 和 Android 设置。

#### 安装模块

React Native Firebase 仅向您的应用程序提供对核心功能的访问。

为了在我们的 react 本地应用程序中通知用户，我们还需要安装**云消息**和**通知**模块。

按照[这里](https://rnfirebase.io/docs/v5.x.x/messaging/ios)的说明安装**云消息**。

按照这里的说明[安装**通知**。](https://rnfirebase.io/docs/v5.x.x/notifications/ios)

同样，确保你遵循了 iOS 和 Android 的步骤。

## 初始代码设置

在您的`App.js`文件中添加这些导入:

```
import React, { Component } from "react";
import { Alert } from "react-native";
import firebase from "react-native-firebase";

import Dashboard from "./src/Dashboard"; 
```

在您的`src`文件夹中创建一个空的`Dashboard.js`文件。我们将在教程的后面部分用代码填充它。

现在创建一个名为`App`的`class`组件，如下所示:

```
export default class App extends Component {
  componentDidMount() {
    // Create notification channel required for Android devices
    this.createNotificationChannel();

    // Ask notification permission and add notification listener
    this.checkPermission();
  }

  createNotificationChannel = () => {};

  checkPermission = async () => {};

  render() {
    return <Dashboard />;
  }
} 
```

我们在这个类组件中创建了一些助手方法，我们调用了`componentDidMount()`生命周期方法。在渲染中，我们将返回`Dashboard`组件。

让我们回顾一下并向这些方法添加代码，看看它们能做什么，以及我们为什么需要它们。

## 创建 Android 通知通道

从 Android 8.0(API 26 级)开始，通知必须指定一个通知通道，否则不会出现。

要让 React Native Firebase 无缝地跨所有版本的 Android 工作，您需要在显示通知之前创建一个通道。这段代码可以多次重新运行，所以每次应用程序启动时都这样做是安全的。

为了创建这个通知通道，我们创建了一个定制的助手方法`createNotificationChannel()`。用以下内容更新它:

```
createNotificationChannel = () => {
  // Build a android notification channel
  const channel = new firebase.notifications.Android.Channel(
    "reminder", // channelId
    "Reminders Channel", // channel name
    firebase.notifications.Android.Importance.High // channel importance
  ).setDescription("Used for getting reminder notification"); // channel description

  // Create the android notification channel
  firebase.notifications().android.createChannel(channel);
}; 
```

Android 通道接受三个参数`channelId`、`name`和`importance`。

完整的参考文档请参见 [**AndroidChannel**](https://rnfirebase.io/docs/v5.x.x/notifications/reference/AndroidChannel) 和[**AndroidNotifications**](https://rnfirebase.io/docs/v5.x.x/notifications/reference/AndroidNotifications)。

## 通知权限和监听者

在发送任何通知之前，我们需要确保我们拥有通知权限，然后，当我们拥有该权限时，我们可以添加通知侦听器。这个通知监听器将监听任何通知，并在收到时向我们返回`notification`。

用下面的代码更新您的`checkPermission()`方法:

```
checkPermission = async () => {
  const enabled = await firebase.messaging().hasPermission();
  if (enabled) {
    // We've the permission
    this.notificationListener = firebase
      .notifications()
      .onNotification(async notification => {
        // Display your notification
        await firebase.notifications().displayNotification(notification);
      });
  } else {
    // user doesn't have permission
    try {
      await firebase.messaging().requestPermission();
    } catch (error) {
      Alert.alert(
        "Unable to access the Notification permission. Please enable the Notification Permission from the settings"
      );
    }
  }
}; 
```

当通知监听器接收到一个通知时，我们用接收到的`notification`调用`displayNotification()`方法，这将显示通知。

我们现在可以接收和显示通知。现在是时候安排一个将在未来某个时间触发的本地通知了。

## 仪表盘屏幕

我们将创建一个**仪表板**屏幕，它将有一个开关来启用和禁用通知，以及一个时间选择器来设置接收通知的时间。它看起来会像下面这样。

[![](img/e7b52256e6be43ac443c381ac44962fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JAOgi4KQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cx2mdkb5bpzr7m6uegmo.png)

为了制作这个 UI 屏幕，我们将添加一些第三方助手库。

1.  [react-native-elements](https://react-native-training.github.io/react-native-elements/)-UI 组件库
2.  [时刻](https://momentjs.com/) -处理日期对象
3.  一个适用于 Android 和 iOS 的 React-Native datetime-picker

让我们从添加所需的导入开始:

```
import React, { Component } from "react";
import { Platform, StyleSheet, Text, SafeAreaView, View } from "react-native";
import { ListItem } from "react-native-elements";
import firebase from "react-native-firebase";
import DateTimePicker from "react-native-modal-datetime-picker";
import moment from "moment"; 
```

现在我们将创建和默认导出`Dashboard`类组件。

现在，添加一些本地状态:

```
state = {
  enableNotification: true,
  isDateTimePickerVisible: false,
  notificationTime: moment({ hour: 17 })
}; 
```

现在我们想在用户登陆仪表板时设置提醒，所以我们将在`componentDidMount()`生命周期方法:
上创建并调用一个`setReminder()`方法

```
componentDidMount() {
  this.setReminder();
}

setReminder = async () => {
  const { notificationTime, enableNotification } = this.state;

  if (enableNotification) {
    // schedule notification
    firebase.notifications().scheduleNotification(this.buildNotification(), {
      fireDate: notificationTime.valueOf(),
      repeatInterval: 'day',
      exact: true,
    });
  } else {
    return false;
  }
}; 
```

在`setReminder()`方法中，我们首先使用`notificationTime`和`enableNotification`状态。

如果`enableNotification`为真，我们使用 [`scheduleNotification()`](https://rnfirebase.io/docs/v5.x.x/notifications/reference/Notifications#scheduleNotification) 方法调度通知。

`scheduleNotification()`有两个参数，`notification`和`schedule`。

对于`notification`，我们将调用另一个自定义方法`buildNotification()`，它将返回通知，在`schedule`中，我们将传递一个带有`fireDate`、`repeatInterval`和`exact`键的对象。

`fireDate`是首次显示通知的日期，以毫秒为单位。对于`repeatInterval`，我们将使用`day`，因为我们希望每天都显示通知。`exact`选项仅适用于 Android，它表示是否应该严格遵守`fireDate`,即提醒，或者是否可以稍微偏离以节省电池。

否则我们将只返回`false`。

现在，我们将添加`buildNotification()`方法，该方法将在收到通知时构建通知:

```
buildNotification = () => {
  const title = Platform.OS === "android" ? "Daily Reminder" : "";
  const notification = new firebase.notifications.Notification()
    .setNotificationId("1") // Any random ID
    .setTitle(title) // Title of the notification
    .setBody("This is a notification") // body of notification
    .android.setPriority(firebase.notifications.Android.Priority.High) // set priority in Android
    .android.setChannelId("reminder") // should be the same when creating channel for Android
    .android.setAutoCancel(true); // To remove notification when tapped on it

  return notification;
}; 
```

让我们为通知`switch`和时间选择器添加一些帮助方法。它们将被各自的组件用来设置本地状态，这些状态将在以后使用:

```
enableNotification = value => {
  this.setState({
    enableNotification: value
  });
};

showDateTimePicker = () => {
  this.setState({ isDateTimePickerVisible: true });
};

hideDateTimePicker = () => {
  this.setState({ isDateTimePickerVisible: false });
};

handleDatePicked = date => {
  this.hideDateTimePicker();

  this.setState({
    notificationTime: moment(date)
  });
}; 
```

最后，我们将返回仪表板 UI 的`render()`:

```
render() {
  const { enableNotification, isDateTimePickerVisible, notificationTime } = this.state;
  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.cardTitleView}>
        <Text style={styles.cardTitle}>Add Reminder</Text>
      </View>
      <ListItem
        title="Notification"
        bottomDivider
        titleStyle={styles.titleStyle}
        switch={{ onValueChange: this.enableNotification, value: enableNotification }}
      />
      <ListItem
        title="Time"
        titleStyle={styles.titleStyle}
        onPress={this.showDateTimePicker}
        rightElement={<Text style={{ opacity: 0.7 }}>{moment(notificationTime).format('LT')}</Text>}
      />
      <DateTimePicker
        isVisible={isDateTimePickerVisible}
        onConfirm={this.handleDatePicked}
        onCancel={this.hideDateTimePicker}
        mode="time" // show only time picker
        is24Hour={false}
        date={new Date(notificationTime)}
        titleIOS="Pick your Notification time"
      />
    </SafeAreaView>
  );
} 
```

是时候加入一些风格让 UI 更吸引人了。

```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: "#EEEFF0"
  },
  cardTitleView: {
    paddingHorizontal: 15,
    paddingTop: 15,
    paddingBottom: 8
  },
  cardTitle: {
    fontSize: 15,
    color: "#585858",
    fontWeight: "600"
  },
  titleStyle: {
    fontSize: 20,
    color: "#585858"
  }
}); 
```

就是这样。我们已经完成了发展。在下一节中，我们将在 iOS 模拟器中测试它。

## 运行 app

通过启用通知并将时间设置为未来 1 分钟来测试应用程序。

[![](img/e5cbbf99b94bdda1509d1005efc14572.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YRFvREeu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4howshbmpeec1kjc9fax.gif)

> 注意:有时通知可能不会在准确的时间显示。请等待整整一分钟，通知才会出现。

## 结论

我们现在可以在 iOS 和 Android 的 React 原生应用中安排每日本地推送通知。

除了 firebase，还有其他库可以用来实现本地推送通知，比如[react-native-push-notification](https://github.com/zo0r/react-native-push-notification)。

这里是 Github repo [本地提醒](https://github.com/vikrantnegi/local-reminders)。

> 最初发表于[媒体](https://medium.com/better-programming/react-native-local-scheduled-push-notification-with-firebase-8c775b71c35c)

PS:封面图片由 [Jamie Street](https://unsplash.com/@jamie452?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 Unsplash 上拍摄