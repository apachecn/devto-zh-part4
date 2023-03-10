# 如何用 Vue.js 搭建群聊 App

> 原文：<https://dev.to/pubnub/how-to-build-a-group-chat-app-with-vue-js-5d2g>

聊天无处不在，已经成为我们日常生活中最重要的交流媒介之一。[聊天应用](https://www.pubnub.com/developers/chat-resource-center/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09)的使用案例数量巨大，并且还在继续增长。随着今天的技术进步，我们希望我们的信息能够实时、即时地发送和接收。这不是一件好事，而是一项要求。

有[成千上万种方法来构建一个聊天应用，](https://www.pubnub.com/blog/building-chat-the-current-landscape/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09)并且有许多事情要考虑:基础设施、可伸缩性、可靠性和安全性等等。有大量的服务、框架和技术可供选择，做出这样的决定可能会让人不知所措！

在本教程中，我们将在 Vue.js 中构建一个实时群聊应用程序。我们将使用 [PubNub](https://www.pubnub.com/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09) 为我们的应用程序供电，它会为我们处理繁重的工作；我们所要担心的是开发应用程序，而不是底层基础设施。

[![Vue Group Chat App](img/1c965cec01c83105dbfd1d7768108e88.png)](https://ocastroa.github.io/Vue_Dist/dist/)

### 教程概述

我们的应用程序将使用 **publish** 向群聊中所有连接的用户发送消息，并使用 **subscribe** 接收消息。我们的消息将使用**历史**存储，因此用户可以看到过去或错过的消息。为了做到这一切，我们将使用 [PubNub Vue.js SDK](https://www.pubnub.com/docs/vue-javascript/pubnub-javascript-sdk/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09) 。本教程分为两部分:**发布/订阅** (Pub/Sub)和**历史**。

您可以在 [GitHub 资源库](https://github.com/ocastroa/Vue_Chat.git)中查看完整的项目。

## 设置发布/订阅消息

在我们开始开发这个应用之前，注册一个免费的 PubNub 账户。你可以在[管理面板](https://admin.pubnub.com/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09)中获得你唯一的 pub/sub 密钥。

接下来，我们需要安装两个依赖项: **vuex** 和 **pubnub-vue** 。我们可以用 **NPM** 来这样做。

*   *npm 安装 vuex - save*
*   *npm 安装 pubnub-view-save*

由于 PubNub Vue.js SDK 是 [PubNub JavaScript SDK](https://www.pubnub.com/docs/web-javascript/pubnub-javascript-sdk/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09) 的包装器，它提供了所有相同的特性。添加了一些额外的功能，如**触发事件**，以简化与 Vue 的集成。我们将使用的一个触发事件是 **$pnGetMessage** 。我们使用带有反应属性的 **$pnGetMessage** ，这样消息一收到就显示出来。订阅一个频道我们使用 **$pnSubscribe** ，发布到一个频道我们使用 **$pnPublish** 。

### 初始化 PubNub 客户端 API

在 **main.js** 文件中，为每个用户创建一个唯一的 UUID，并用您的密钥替换 pub/sub 密钥。我们包括两个插件: **PubNubVue** 和 **Vuex。**

```
import Vue from 'vue'
import App from './App'
import store from './store';
import PubNubVue from 'pubnub-vue'

Vue.config.productionTip = false;

const publish_Key = 'YOUR_PUBLISH_KEY_HERE';
const subscribe_Key = 'YOUR_SUBSCRIBE_KEY_HERE';

// Make a unique uuid for each client
const myUuid = fourCharID();
const me = {
  uuid: myUuid,
};

try{
  if(!publish_Key || !subscribe_Key){
    throw 'PubNub Keys are missing.';
  }
}
catch(err){
  console.log(err);
}

Vue.use(PubNubVue, {
  subscribeKey: subscribe_Key,
  publishKey: publish_Key,
  ssl: true
}, store);

/* eslint-disable no-new */
new Vue({
  el: '#app',
  store,
  components: { App },
  template: '<App/>',
  created
}) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们通过调用函数 **fourCharID** 为用户生成一个随机的 4 字符 UUID。

```
function fourCharID() {
  const maxLength = 4;
  const possible = 'abcdef0123456789';
  let text = '';

  for (let i = 0; i < maxLength; i++) {
    text += possible.charAt(Math.floor(Math.random() * possible.length));
  }

  return text;
} 
```

Enter fullscreen mode Exit fullscreen mode

建议在生产应用中使用**标准 128 位 UUID** ，但 UUID 也可以是普通字符串，就像这个应用一样。恒' *me* 持有 UUID。为了将常量提交到**的 Vuex 存储器**，我们添加了函数**创建的**。

```
function created(){
  this.$store.commit('setMe', {me});
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将在创建 Vue 实例时执行。

### 设置 Vuex 商店

对于 **store.js** 文件，我们设置了保存和管理应用程序状态的**集中存储**。该文件将包含一个[全局状态对象](https://vuex.vuejs.org/guide/state.html)以及一组[突变](https://vuex.vuejs.org/guide/mutations.html)和 [getter](https://vuex.vuejs.org/guide/getters.html) 函数。因为外部组件不能直接访问状态，所以每当我们需要更新状态时，我们提交一个**突变**。

```
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

const state = {
  me: {},
};

const mutations = {
  setMe(state, {me}) {
    state.me = me;
  },
}

const getters = {
  getMyUuid: (state) => state.me.uuid,
};

export default new Vuex.Store({
  state,
  getters,
  mutations,
}); 
```

Enter fullscreen mode Exit fullscreen mode

在三个组件中引用了 *getMyUuid* getter，它是 Uuid 的一个**全局 getter** 。

### 聊天容器组件

ChatContainer 组件是 UI 的最高父节点。标记包括用于 ChatContainer 子组件的定制 HTML 标签，以及用于被动呈现数据的 **Vue 特定标记**。

```
<template>
  <div class="chat-container">
    <div class="heading">
      <h1>{{title + '- User: ' + uuid }}</h1>
    </div>
    <div class="body">
      <div class="table">
        <chat-log></chat-log>
        <message-input></message-input>
      </div>
    </div>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

**h1** 花括号绑定了两个 JavaScript 变量， *title* 和 *uuid* ，被动地对表达式求值，并将文本输出显示为标签中的文本内容。变量 *title* 从函数**数据**中获取其值。

```
data() {
  return {
    title: 'PubNub & Vue Global Chat',
  };
}, 
```

Enter fullscreen mode Exit fullscreen mode

在我们讨论变量 *uuid* 之前，让我们讨论一下导入和上面的两个属性**数据**。

```
import ChatLog from '@/components/ChatLog';
import MessageInput from '@/components/MessageInput';
import {mapGetters} from 'vuex'; 
```

Enter fullscreen mode Exit fullscreen mode

因为我们在标记中使用了**聊天记录**和**消息输入**标签，所以我们需要导入**聊天记录**和**消息输入**组件，以便正确呈现标签。我们还导入了 **mapGetters** 来从商店获取 UUID。

```
export default {
  name: 'chat-container',
  components: {
     ChatLog,
     MessageInput,
  }, 
```

Enter fullscreen mode Exit fullscreen mode

**名称属性**是“*聊天容器*，它与标记中的 kebab-case HTML 标签名称一致。我们包含了**组件** **属性**来告诉 Vue 哪些组件在标记中被引用。现在，回到 **h1** 花括号中的变量 *uuid* ，我们需要设置**计算属性**，该属性使用 **mapGetters** 将 getter ' *getMyUUID* 映射到 *uuid* 。

```
computed: {
 ...mapGetters({
     uuid: 'getMyUuid',
  ),
}, 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将 getter 映射到不同的名称( *uuid)* ，所以我们使用一个对象。最后，我们包括功能**安装**订阅频道' *vueChat '。*

```
mounted() {
  this.$pnSubscribe({
      channels: ['vueChat'],
  });
}, 
```

Enter fullscreen mode Exit fullscreen mode

一旦 Vue 实例被挂载到 **DOM** ，我们就使用 **$pnSubscribe** 订阅频道。

### 消息输入组件

我们将该组件分为 4 个部分:

1.  首先，我们检查消息体是否为空。
2.  然后，我们从商店中获取用户 UUID，并将其赋给变量 *userUUID* 。
3.  我们将消息连同 *userUUID* 一起发布到信道“*vueChat”*。
4.  最后，我们重置文本输入。

这是组件的模板。

```
<template>
  <div class="message-input">
    <textarea
      ref="messageInput"
      placeholder="message..."
      maxlength="20000"
      @keydown.enter="submitMessage"
    ></textarea>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

当用户键入消息正文并按 enter 键时，将调用函数 **submitMessage** 来检查消息正文是否为空。我们在**方法**中包含了这个函数。(注意:**消息输入组件**的其余代码将在**提交消息**的内部)。

```
methods: {
  submitMessage(event) {
    if (!event.shiftKey) {
       event.preventDefault();
     } else {
        return;
     }

     // If the message body is empty, do not submit
     if (event.target.value.length === 0) {
       return;
     } 
```

Enter fullscreen mode Exit fullscreen mode

我们访问 getter， *getMyUUID* ，并将其赋给变量 *userUUID* 。

```
const userUUID = this.$store.getters.getMyUuid; 
```

Enter fullscreen mode Exit fullscreen mode

如果用户按回车键，并且消息正文不为空，我们将文本和用户的 UUID 发布到' *vueChat* '。

```
this.$pnPublish({ 
  channel: 'vueChat', 
  message: {
    text: event.target.value,
    uuid: userUUID,
    },
  }) 
```

Enter fullscreen mode Exit fullscreen mode

一旦用户按下 enter 键，我们就重置文本输入。

```
event.target.value = ''; 
```

Enter fullscreen mode Exit fullscreen mode

### 消息气泡组件

聊天日志将在消息气泡中显示发送和接收的消息。我们稍后将讨论聊天日志组件，但是现在，让我们把重点放在消息气泡组件上。当您发送消息时，消息气泡会显示在聊天日志的右侧，而不会显示您的 UUID。从其他用户收到的消息显示在聊天日志的左侧，用户 UUID 显示在气泡上方。这遵循了很多群聊 app 的设计逻辑。

```
<template>
  <div
    class="message-bubble"
    :class="me"
  >
    <span
      class="from"
      :class="me"
    >uuid</span>
    <br :class="me">
    <span
      class="message-text"
    >text </span>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

类' *me* '被绑定到一个类，比如' *message-bubble* '或者' *from* '，只有在你发送消息的时候。当' *me* '被绑定到一个类时，消息气泡的定位和样式将会改变，如上所述。计算属性用于检查用户是您自己还是其他人。

```
export default {
  name: 'message-bubble',
  props: ['uuid','text'],
  computed: {
    me() {
      let result = false;
      // Check if the client uuid of the message received is your client uuid
      if (this.$store.getters.getMyUuid === this.uuid) {
        result = true;
      }

      // Render the message bubble on the right side if it is from this client
      return result ? 'me' : '';
    },
  },
  data() {
    return {};
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

除了 computed 属性之外，脚本中的另一个重要部分是注册到 MessageBubble 组件的属性。属性中的两个值，' *uuid* 和' *text* '，将被传递给 MessgeBubble 的父组件 ChatLog。

### 聊天记录组件

聊天日志会显示频道收到的消息。在我们处理模板之前，让我们先做脚本部分。

```
import MessageBubble from '@/components/MessageBubble';
import {mapGetters} from 'vuex';
function scrollBottom() {
  this.$el.scrollTo(0, this.$el.scrollHeight);
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将在标记中使用 *message-bubble* 标签，所以我们需要在脚本中导入 MessageBubble 组件，以便正确呈现标签。 **scrollBottom** 功能会在收到消息时自动将聊天记录滚动到底部。这个函数在[手表](https://vuejs.org/v2/api/#watch)属性中被调用。

```
watch: {
  vueChatMsg: function(){
    this.$nextTick(scrollBottom);
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

我们用**。$nextTick** 确保 **scrollBottom** 函数仅在 DOM 更新后被调用。接下来，让我们添加 name 属性、组件和数据函数。

```
name: 'chat-log',
components: {MessageBubble},
data() {
  return {
    vueChatMsg: this.$pnGetMessage('vueChat'),
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

数据函数返回 *vueChatMsg，*，其中包含来自通道的新消息有效载荷。如前所述，由于我们使用的是 **$pnGetMessage** ，消息一收到就会显示。我们将通道名称作为一个参数。 *vueChatMsg* 属性保存一个对象数组，数组中的每个对象都是[订阅消息响应](https://www.pubnub.com/docs/vue-javascript/api-reference-publish-and-subscribe#subscribe/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09)。对于发布的每条消息，都会向数组中添加一条新的消息响应。消息响应中的对象包括通道名称、发布者、消息负载、订阅者等信息。我们只想要包含' *uuid* '和'*文本*'的消息有效负载。我们将在模板中实现这个逻辑。

```
<template>
  <div
    class="chat-log"
    ref="chatLogContainer"
  >
  <message-bubble
    v-for="msg in vueChatMsg" 
    v-bind:key="msg.id"
    v-bind:uuid="msg.message.uuid"
    v-bind:text="msg.message.text"
  ></message-bubble>
  </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

我们用 **v-for** 来迭代 *vueChatMsg* ，用“ *msg.id* 作为唯一键。我们使用 **v-bind** 来绑定两个属性值， *uuid* 和 *text* 。记得我们在子组件 MessageBubble 中声明了 prop。所以对于 for 循环的每次迭代，我们只迭代消息有效负载，并将' *msg.message.uuid* '和' *msg.message.text* '绑定到其对应的 prop 属性。

我们来快速总结一下以上内容。每次收到消息响应时，它都会作为一个新元素添加到由数据函数返回的对象数组 *vueChatMsg* 中。当这种情况发生时，在*消息气泡*标签中，我们使用 v-for 迭代数组中的新元素。因为我们只想要消息有效载荷，所以 v-for 只检查包含有效载荷的'*消息*。有效载荷值' *uuid* 和' *text* '被绑定到其适当的属性。然后，这两个值都被发送回子组件 MessageBubble。

这就是本教程的发布/订阅部分。确保代码是正确的，并且您已经安装了适当的插件。从[回购](https://github.com/ocastroa/Vue_Chat.git)中获取四个组件的 **CSS** 部分。通过在终端中键入“ **npm install** ”和“ **npm run dev** ”来运行您的程序，您的程序应该在本地主机端口上启动。在消息输入中输入一条消息，您应该会在聊天日志的右侧看到一个蓝色的气泡。打开另一个选项卡，或者最好是窗口，复制并粘贴 URL。现在，在消息输入中输入一条新消息，你会在聊天记录的右边看到蓝色的气泡。但是现在，你也应该在另一个聊天记录上看到新消息了。这条新消息应该是聊天日志左侧的灰色气泡。摆弄两个聊天窗口，并看到消息实时出现在两个屏幕上。

## 用历史 API 存储聊天消息

虽然一切都已准备就绪，可以使用了，但是还有一个问题。如果你重新加载页面，你会发现所有的消息都消失了。这是因为没有打开[存储&回放](https://www.pubnub.com/docs/vue-javascript/api-reference-storage-and-playback/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09)功能。要打开它，进入[的 PubNub 管理仪表板](https://admin.pubnub.com/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09)，点击你的应用程序。点击**按键**并向下滚动至**应用附加模块**。继续向下滚动，直到到达**存储&回放**并将开关切换到上的**。保持默认值不变。**

[![Storage and Playback Toggle Switch](img/7b3cc854d2bd4c7828d7c93263798026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gga1Io5t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.pubnub.com/wp-content/uploads/2019/05/storage-and-playback-toggle.png)

现在它打开了，信息会保存在存储器中，以后还可以检索。还可以从历史记录中删除消息，以符合 [GDPR 法规。](https://dev.to/products/security/gdpr/)如果您克隆了回购，则重新加载聊天应用程序的页面，消息将按照从旧到新的顺序显示。如果您没有克隆回购协议，这些消息将不会出现，因为获取通道历史消息的**历史函数**尚未添加到代码中。不管怎样，在下一节中，我们将实现 history 函数，以便可以存储和检索消息。

## 设置历史

获取我们频道的历史消息并不难。我们需要对三个文件做小的修改: **store.js** 、 **ChatContainer.vue** 和 **ChatLog.vue** 。先说 **store.js** 。

### 修改 Vuex 状态

在**状态**中，我们需要添加一个新的属性**历史**，用一个空数组作为值。

```
const state = {
  ...
  history: [],
}; 
```

Enter fullscreen mode Exit fullscreen mode

在**突变**中，我们增加了一个新的突变， **addHistory** ，以*状态*作为第一个自变量，*历史*作为第二个自变量。

```
const mutations = {
  ...
  addHistory(state, {history}){
    history.forEach(element => {
      state.history.push(element.entry);
    });
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

我们迭代数组 *history* ，它包含了从通道中检索到的历史消息。数组中的每个元素包含两个键， *timetoken* 和 *entry* 。我们只想要*条目*，因为它包含用户输入的文本和他们的 UUID。这就是为什么在每次迭代中，我们将 **element.entry** 推送到我们在**状态**中添加的**历史**数组。我们将只添加一行到 **getters** 。

```
const getters = {
  ...
  getHistoryMsgs: (state) => state.history,
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 修改聊天容器

由于我们需要使用历史功能，所以导入 **PubNubVue** 。

```
import PubNubVue from 'pubnub-vue'; 
```

Enter fullscreen mode Exit fullscreen mode

在导入下面，我们添加了一个新函数， **fetchHistory** ，它将从通道中获取 6 条消息。您可以更改要获取的消息数量，最大数量为 100。

```
function fetchHistory(store){
  PubNubVue.getInstance().history(
      {
        channel: 'vueChat',
        count: 6, // how many items to fetch
        stringifiedTimeToken: true, // false is the default
      },
      function(status, response) {
        const msgs = response.messages;
        // Iterate msgs array and save each element to history
        msgs.forEach(elem => {
          store.commit('addHistory', {history: [elem]});
        })
      }
   )   
} 
```

Enter fullscreen mode Exit fullscreen mode

为了提交历史有效载荷，将 *response.messages* 保存到常量变量“ *msgs* ”。该常量包含一个对象数组，其中每个对象包含两个键( *timetoken* 和 *entry)* )。我们不想将整个数组提交给 Vuex 存储库**，相反，我们想迭代数组并提交每个元素。这将使得在 **addHistory** 函数中获取必要的键变得更加容易。要包含的最后一个修改在**挂载的**中，它调用 **fetchHistory** 。** 

```
mounted() {
  ...
  this.$nextTick(fetchHistory(this.$store));
}, 
```

Enter fullscreen mode Exit fullscreen mode

我们通过这个。$store 作为参数，这样我们可以将更改提交到存储中。

### 修改聊天记录

这是我们需要更新的最后一个文件。我们需要对模板和脚本进行更改。先说剧本。我们需要导入 **mapGetters** ，因为我们将在 computed 属性中使用它。

```
import {mapGetters} from 'vuex'; 
```

Enter fullscreen mode Exit fullscreen mode

在 computed 属性中，我们将 getter ' **getHistoryMsgs** '映射到**历史**。

```
computed: {
  ...mapGetters({
    history: 'getHistoryMsgs',
  }),
}, 
```

Enter fullscreen mode Exit fullscreen mode

在模板中，我们添加了另一个*消息气泡*标签。

```
<template>
  ...
   <message-bubble
    v-for="historyMsg in history" 
    v-bind:key="historyMsg.id"
    v-bind:uuid="historyMsg.uuid"
    v-bind:text="historyMsg.text"
  ></message-bubble>
  ...
</template> 
```

Enter fullscreen mode Exit fullscreen mode

这看起来和我们之前做的非常相似。我们用 **v-for** 来迭代历史。在每次迭代中，我们从数组中检索' *uuid* 和'*文本*，并将其绑定到适当的属性。消息将在聊天日志中显示为消息气泡。这就是我们需要为历史所做的一切。再次运行该程序，您应该会在聊天日志中看到历史记录中的最后六条消息。

[![Vue Group Chat App](img/bcc512ef3328f4ac51603114c0a3072b.png)](https://ocastroa.github.io/Vue_Dist/dist/)

有两件事需要注意。第一件事是，消息将仅在存储器中保存 1 天。1 天后，邮件将从存储中删除。您可以通过更改**保留时间**来更改信息存储的时间段，该时间位于**存储&回放附件**中。出于本教程的目的，我们保留默认值 1 天。

第二件要注意的事情是，历史消息将显示在聊天日志的左侧，即使这些消息是来自你的。这是因为每次应用程序实例化时，我们都会生成一个随机的 4 字符 UUID。因此，当您重新加载页面时，会为您分配一个新的 UUID，您在重新加载之前发送的消息将被视为其他用户发送的消息。这对于本教程来说没问题，但是对于实际的产品，每个用户都应该有一个唯一的持久的 UUID。对于持续 UUID，您发送的历史消息将显示在聊天日志的右侧。

## 接下来是什么？

现在您已经实现了基本的消息传递功能，是时候添加更多功能了！前往我们的[聊天资源中心](https://www.pubnub.com/developers/chat-resource-center/?utm_source=Syndication&utm_medium=DevTo&utm_campaign=SYN-CY19-Q2-DevTo-June-09)探索新的教程、最佳实践和设计模式，让您的聊天应用更上一层楼。