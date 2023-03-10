# 如何在 Vue & Nuxt 中构建简单的事件总线

> 原文：<https://dev.to/lilianaziolek/how-to-build-a-simple-event-bus-in-vue-nuxt-592>

这篇文章将会简短而甜蜜，因为它实际上只是为另一篇即将到来的文章做准备(在 Vue/Nuxt 应用中截取手机上的后退按钮)。

# 问题

与发布-订阅模式相关的事件总线是软件开发中一个非常基本的概念。如果你没有听说过，我建议你阅读维基百科条目来理解这篇文章的其余部分。

简而言之，事件总线允许你分离系统的不同部分，这些部分在某种程度上依赖于发生在系统另一部分的事情(事件)。例如，考虑这样一种情况，正在登录的用户应该触发获取某些组件中的额外数据。有些人可能会认为，由于 Vue 的反应性和 VueX，事件总线是不必要的。这在某种程度上是正确的——因为这两种机制极大地减少了任何显式发布/订阅的需要。然而，在我看来，虽然您可以尝试总是只使用计算属性或观察器，但在某些情况下，事件总线可能是一种更简单、更广为人知的模式。作为一名开发人员，拥有各种工具并根据产生最简单、最易读和最易维护的代码来选择它们是很好的。

# Vue $on/$emit/v-on

Vue 带有内置的事件总线/发布-订阅机制。任何 Vue 实例都会公开一些相关的方法，包括: [`$on`](https://vuejs.org/v2/api/#vm-on) 和 [`$emit`](https://vuejs.org/v2/api/#vm-emit) 。

## 提醒:本地事件

通常，我们使用$emit 方法和 v-on 指令在父组件和子组件之间进行通信。

例如，在一个由带有关闭按钮的对话框(`ComponentPart.vue`)组成的子组件中，我们可以有如下内容:

```
<v-btn @click="$emit('close')">
    <v-icon>close</v-icon>
</v-btn> 
```

然后是父组件中的以下内容:

```
<v-dialog v-model="dialog" >
    <component-part @close="dialog = false"></component-part>
</v-dialog> 
```

注意，`@close`只是`v-on:close`的快捷方式。(你能猜到 v-btn 内部发生了什么让我们写`@click`吗？)

## 事件总线插件

event bus 使用相同的机制，只是我们需要获得一个全局可用的组件实例，我们将使用`$on`而不是`v-on`。正如我们在[之前的系列文章](https://dev.to/lilianaziolek/understanding-nuxt-vue-hooks-and-lifecycle-part-3-8fo)中所提到的，为每个访问者做一些事情，并且只做一次，在客户端，我们可以创建一个插件。这将初始化我们的事件总线。

*eventBus.client.js*

```
import Vue from 'vue'

const eventBus = new Vue();
//this helps WebStorm with autocompletion, otherwise it's not needed
Vue.prototype.$eventBus = eventBus;

export default ({app}, inject) => {
    inject('eventBus', eventBus);
} 
```

## 示例用法:

假设在我们的 VueX 商店中，我们与后端进行通信，该后端在用户登录后启动(这里模拟的只是一个登录按钮)并检索用户详细信息，例如告诉我们用户是否是管理员。一旦我们知道用户是否是管理员，我们想获取一些额外的管理员数据来显示在组件中。对于$eventBus，它看起来像这样:

### 用户资料变更时通知

store/user.js

```
export const state = () => ({
  userDetails: {
    admin: false
  },
});

export const mutations = {
  reverseUserDetails(state) {
    state.userDetails = {admin: !state.userDetails.admin};
  }
};
export const actions = {
  async fetchUserDetails({commit}) {
    // normally we'd have an axios call here, it would call our API to get user details
    // here I'm just hardcoding the userDetails to values opposite to what they were
    // every time when you "Login" and fetchUserDetails is called you will switch between admin and non-admin
    commit("reverseUserDetails");

    this.$eventBus.$emit("userDetailsChanged");
  }
}; 
```

### 订阅各自组件中的事件

components/admindatademo . vue

```
<template>
  <div>
    <span v-if="isAdmin"></span>
    <span v-else>Current user is not admin</span>
  </div>
</template>

<script>
  import {mapState} from 'vuex';

  export default {
    name: "AdminDataDemo",
    computed: {
      ...mapState({
        isAdmin: state => state.user.userDetails.admin,
        adminData: state => state.admin.adminData
      })
    },
    created() {
      //this listener is not needed in SSR-mode
      if (process.client) {
        console.log("Subscribing to know when userDetails change");
        this.$eventBus.$on("userDetailsChanged", () => {
          console.log("We were notified that user details changed, reacting, admin: " + this.isAdmin);
          if (this.isAdmin) {
            this.$store.dispatch('admin/fetchAdminData')
          } else {
            this.$store.dispatch('admin/removeAdminData')
          }
        });
      }
    },
    beforeDestroy() {
      //make sure to always unsubscribe from events when no longer needed
      console.log("Switching off userDetails listener");
      this.$eventBus.$off("userDetailsChanged");
    }
  }
</script> 
```

### 管理员数据刷新

```
export const state = () => ({
  adminData: {}
});

export const mutations = {
  setAdminData(state, value) {
    state.adminData = value
  }
};
export const actions = {
  async fetchAdminData({commit}) {
    // normally we'd have an axios call here, it would call our API to get some data specific to admin.
    // here we're just setting something random
    commit("setAdminData",{someValue: Math.random()});
  },
  async removeAdminData({commit}) {
    // if a user logs out, or stops being an admin, we want to remove existing adminData
    commit("setAdminData", {});
  }
}; 
```

### 有什么好处？

您可能会认为 user.js 可以直接分派给 admin.js，让它直接获取额外的数据——但这可能意味着，即使需要它的组件不活动，您也可以获取管理数据。此外，您将把获取一般用户详细信息与管理功能结合起来。

在这个非常简单的例子中，您还可以监视 user.js 存储状态，并在`userDetails.admin`值改变时做出反应。我希望这个简单的例子能够展示如何将它用于更复杂的场景。我将在下一篇文章中展示一个这样的场景(拦截手机上的后退按钮)。

# 全码

和往常一样，这个例子的完整工作项目位于 [Github](https://github.com/lilianaziolek/blog-examples/tree/eventBusAndBackButtonIntercept/dry-examples) 中——注意它只是我目前使用的项目的一个分支。

# 其他注释:

*   在 Nuxt 上下文中，您可以简单地使用`this.$root`，因为它是共享的根 Vue 实例。然而，我非常喜欢用代码尽可能清晰地表达你的意图，所以我选择创建一个非常简单的插件，并给它起一个有意义的名字。
*   我的示例代码总是有许多 console.log 语句，因此，如果您运行它，您可以快速、轻松地在控制台上看到发生了什么。如果在实际应用程序中使用这些代码，请删除所有这些代码以避免过多的噪音，或者用合适的日志框架(如果使用的话)来替换。