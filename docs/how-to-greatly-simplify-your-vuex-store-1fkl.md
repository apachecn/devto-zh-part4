# 如何大大简化您的 Vuex 商店

> 原文：<https://dev.to/ratracegrad/how-to-greatly-simplify-your-vuex-store-1fkl>

随着 Vue 应用程序规模的增长，Vuex 存储中的动作和变化数量也在增长。让我告诉你如何把它简化成更容易管理的东西。

## vuex 是什么

Vuex 是 Vue.js 应用程序的状态管理模式+库。它充当应用程序中所有组件的集中存储，规则确保状态只能以可预测的方式变化。

## 我们如何使用 Vuex

我们使用 Vuex 在工厂核心框架应用程序中的所有应用程序之间共享状态。由于框架是一个应用捆绑包，我们目前有 9 个 Vuex 商店。每个商店都有自己的状态、动作和变化。我们使用商店中的动作对后端进行 API 调用。一旦数据被返回，我们使用突变来存储它的状态。这允许任何组件访问该数据。可以想象，我们的商店可以有大量的动作来处理这些 API 调用。以下是我们一家 Vuex 商店的所有活动示例。[![](img/3b4273fa23e2fac871afffd4adaebc9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_l6tOhIX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563714381/1_M7opn1qJ0HyA_Y4PJM0v5w_n4zxvu.png)

这家店有 16 个动作。现在想象一下，如果我们有 9 个商店，我们的工厂核心框架总共有多少个动作！

## 简化我们的行动

我们所有的行为基本上都执行相同的功能。每个操作都执行以下操作:

*   从 API 获取数据(如有必要，包括有效负载)
*   (可选)在状态中存储数据
*   将响应返回给调用该操作的组件

为了将这些重构为一个动作，我们需要知道动作是否需要知道:

*   要命中的端点
*   是否在 API 调用中发送有效负载
*   是否将数据提交给状态，如果是，提交给哪个状态变量

## 我们当前的行动

这是我们行动的一个例子。

```
async getLineWorkOrders({ rootState, commit }, payload) {
    try {
        let response = await axios.post(
           'api.factory.com/getLineWorkOrders',
           Object.assign({}, payload.body, { language: rootState.authStore.currentLocale.locale }),
           rootState.config.serviceHeaders
        );
       commit( 'setCurrentWorkOrderNumber', response.data.currentWorkOrderNumber );

       return response.data;
    } catch (error) {
       throw error;
    }
}, 
```

在此操作中，我们通过点击端点“API . factory . com/geteLineWorkOrders”从后端 API 获取数据。检索到数据后，状态变量 currentWorkOrder 被更新。最后，数据被返回给发出调用的组件。我们所有的行为都有这种格式。为了将它重构为一个单一的动作，我们需要有一个端点，无论是否发送有效载荷，是否提交数据。下面是我们重构后的单个动作:

```
async fetchData({ rootState, commit }, payload) {
   try {
       let body = { language: rootState.authStore.currentLocale.locale };
       if (payload) {
           body = Object.assign({}, payload.body, body);
       }
      let response = await axios.post(\`api.factory.com/${payload.url}\`, body, rootState.config.serviceHeaders );
      if (payload.commit) {
          commit('mutate', {
              property: payload.stateProperty,
              with: response.data\[payload.stateProperty\]
           });
      }
      return response.data;
   } catch (error) {
      throw error;
   }
} 
```

这个动作将处理每一个可能的呼叫。如果我们需要用 API 调用发送数据，它就会这样做。如果我们需要提交数据，它就会提交。如果不需要提交数据，就不需要提交。它总是向组件返回数据。

## 利用一次突变

以前，对于每个需要改变状态的动作，我们都会创建一个新的改变来处理。我们用一个突变取代了它们。这是我们的单一突变:

```
const mutations = {
    mutate(state, payload) {
       state\[payload.property\] = payload.with;
    }
}; 
```

如果一个动作需要在状态中存储数据，我们这样称呼这个变异:

```
commit('mutate', {
    property: <propertyNameHere>,
    with: <valueGoesHere>
}); 
```

## 结论

我们已经通过只有一个动作和一个突变极大地简化了我们商店中的动作和突变。

## 培训课程

我在我的网站 CodePrep 上创建培训课程。我有关于 Vue、Webpack、Flexbox、函数式编程等等的培训课程。点击这里查看。