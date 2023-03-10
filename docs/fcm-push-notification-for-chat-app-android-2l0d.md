# 聊天应用程序的 FCM 推送通知(Android)

> 原文：<https://dev.to/supertiger/fcm-push-notification-for-chat-app-android-2l0d>

# Firebase 推送通知(安卓)

因此，我目前正在为我的聊天客户端开发一个 Android 应用程序( [https://nertivia.tk](https://nertivia.tk) )。我是移动开发者世界的新手。我一直在努力为我的聊天设置推送通知，直到现在，我已经找到了一个方法，并希望与你们分享。

# 必填

mongose/MongoDB(我用的是 mongose)
·FCM-node/request(我用的是 FCM-node)
·Express。JS
axios
native script-plugin-firebase
启用 FCM 的 Firebase 账户(免费)

# 设置模式

首先，我将创建一个新的 Mongoose 模式:

```
// models/devices.js
const mongoose = require("mongoose");

const { Schema } = mongoose;

const devicesSchema = new Schema({
  user: { type: Schema.Types.ObjectId, ref: "users" },
  token: { type: String, unique: true },
  platform: { type: String }
});

module.exports = mongoose.model("devices", devicesSchema); 
```

我创建了一个模式，其中包含:
`user`来查找用户创建的所有令牌。(一个用户可能有多个设备，因此来自同一个用户的多个令牌被创建。
`token`保存用户令牌。为了使令牌不被复制，我给对象添加了`unique: true`。
`platform`将用于确定令牌是从 android 设备还是 Apple 设备创建的。目前，我将只关注 android。

# 注册用户令牌

接下来，我创建了一个新的 Express route，它将获取客户机生成的 fcm 令牌。

```
// routes/devices/registerDevice.js
const Devices = require("../../models/Devices");

module.exports = async (req, res, next) => {
  const { token } = req.body;
  if (!token.trim()) {
    return res.status(403).json({ message: "Token not provided." });
  }
  try {
    await Devices.create({
      user: req.user._id,
      platform: "android",
      token
    });
    res.json({ message: "Done" });
  } catch (e) {
    return res.status(403).json({ message: "token already saved." });
  }
}; 
```

首先，我检查令牌是否存在。如果它不存在，我将拒绝该请求。如果它存在，我将把它添加到数据库中。现在，我将手动定义 android 平台，因为这是我的主要关注点。如果令牌已经保存，我将返回一个错误。

# 发送推送通知

这是我在服务器端采取的最后一个步骤，当有人向用户发送消息时发送通知。我不会展示完整的消息逻辑，因为它很长。你可以在我的 GitHub 页面上看到[这里](https://github.com/supertiger1234/Nertivia-Server/blob/ca1801644764565598e6b180cbaaac137c0e30af/routes/messages/sendMessage.js)

```
// routes/messages/sendMessage.js
async function sendPushNotification(user, msg, recipient) {
  const _id = recipient._id;

  // check if notification token exists
  const requestToken = await Devices.find({ user: _id });

  if (!requestToken || !requestToken.length) return;

  const tokens = requestToken.map(t => t.token);

  const msgContent = msg.message;

  const message = {
    registration_ids: tokens,

    notification: {
      title: user.username,
      body:
        msgContent.length >= 500
          ? msgContent.substring(0, 500) + "..."
          : msgContent,
      image: "https://" + domain + "/api/avatars/" + user.avatar
    },
    data: {
      channel_id: msg.channelID
    }
  };

  fcm.send(message, async function(err, response) {
    if (err) {
      console.log("Something has gone wrong!");
    } else {
      // remove all expired tokens from db.
      const failedTokens = response.results
        .map((r, i) => r.error && tokens[i])
        .filter(r => r);
      await Devices.deleteMany({ token: { $in: failedTokens } });
    }
  });
} 
```

`_id`是用户的 id。数据库将寻找与 id 相关联的令牌。如果存在的话，`requestToken`应该包含用户的所有令牌。如果它们不存在，我将返回函数，使它不再执行。

`message`变量包含将被发送到 Firebase，然后发送到用户设备的所有信息。`registration_ids`需要一个令牌数组。我已经为它提供了数据库使用 users _id 找到的令牌。

我也做了一些信息检查。如果消息的可变长度超过 500 个字符，我会将其压缩到 500 个字符并发送出去。这是因为仅仅为了一个通知而发送大量数据是不明智的。

该函数将向变量中提供的所有令牌发送通知。如果一些令牌失败，我将从数据库中删除它们。如果用户注销或卸载了应用程序，令牌通常会失败。

# 注册并发送令牌

这是最后一步，设置客户端。我使用 nativescript-vue，因为我对 vue.js 很有经验。我还没有在 GitHub 上上传这段代码，我很快会上传的。

```
//components/LoginPage.vue
import axios from 'axios'
import { messaging, Message } from 'nativescript-plugin-firebase/messaging';
export default {
  methods: {
    ...LoginClickEvent,
    async registerPushNotifications() {
      messaging.registerForPushNotifications({
        onPushTokenReceivedCallback: async (token) => {
            // posts the token to registerDevice route.
            axios.post('https://nertivia.tk/api/devices', {
                token: token,
            })
        },
        showNotificationsWhenInForeground: true
      }).then(() => console.log("Registered for push"));

      return;
    },
 }
} 
```

上面的代码将在登录通过身份验证后执行。`onPushTokenReceivedCallback`事件将注册并给出令牌。在我们获得令牌后，我们将把它发送到我们使用 axios 创建的`registerDevice`路由。然后`showNotificationsWhenInForeground`将确保应用程序关闭时会出现通知。

现在，您的通知应该已经准备好并出现了。如果你发现任何更好或更有效的方法，请在评论中告诉我。如果你和我一样在挣扎，我希望我的博客对你们有所帮助。