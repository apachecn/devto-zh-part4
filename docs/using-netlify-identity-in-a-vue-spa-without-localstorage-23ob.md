# 在没有本地存储的 Vue SPA 中使用网络身份

> 原文：<https://dev.to/jeremywynn/using-netlify-identity-in-a-vue-spa-without-localstorage-23ob>

Netlify Identity 服务可以使认证功能的实现变得更加容易。使用[网络身份小部件](https://www.netlify.com/docs/identity/)增加了便利性。但是有一个安全使用它们的问题，特别是当你试图保护单页应用程序(spa)的时候。

对于 Vue，Netlify 链接到的一个[示例](https://github.com/whizjs/netlify-identity-demo-vue)位于 [Netlify 身份小部件](https://github.com/netlify/netlify-identity-widget) Github 存储库的 examples 文件夹中，该存储库展示了如何通过 Netlify 身份和小部件将身份验证合并到一个简单的 Vue 应用程序中。示例应用程序被编写为在成功登录后将登录用户的信息(包括 auth 令牌)存储在 Vuex 的商店中。然而，用户状态由`store/modules/user.js`中的以下内容设置:

`window.localStorage.getItem('user')`

这样做是出于持久性的原因，以便应用程序保留用户的登录状态和信息，从而防止用户在重新加载或关闭页面时再次登录。使用 localStorage 设置认证状态中的`user`的问题是，这依赖于客户端，客户端可以使用 JavaScript 将其设置为任何值。例如，进入前端 [Vue 示例应用](https://netlify-identity-demo-vue.netlify.com/)，打开浏览器控制台，输入:

`window.localStorage.setItem('user', true);`

然后刷新页面。页面将作为一个经过身份验证的用户刷新，显示消息“Hello {blank}”，注销登录和注册按钮所在的位置，现在您可以单击受保护的页面链接来显示受保护的消息，而不会出现未经身份验证的弹出窗口。

我还使用一个示例 Nuxt.js 应用程序来说明这一点，该应用程序托管在 [here](https://nuxt-netlify-identity-spa-before.netlify.com/) ( [source](https://github.com/jeremywynn/nuxt-netlify-identity-before) )。“规避”按钮只是执行相同的 localStorage 函数，随后进行页面刷新。

## 无服务器 SPA 可以做什么？

我为此纠结了一段时间，因为 Nuxt 中有几个选项:[验证模块](https://auth.nuxtjs.org/)、[验证外部 API (JWT)](https://nuxtjs.org/examples/auth-external-jwt) ，但它们似乎都涉及后端或通过 [GoTrue](https://github.com/netlify/gotrue) 更深入地钻研 Netlify Identity 的 API。关于是否在 localStorage 中存储身份验证信息，存在着[的争论](https://dev.to/rdegges/please-stop-using-local-storage-1i04)，并且还有其他几个教程教导以与 Vue 示例非常相似的方式存储身份验证令牌。

答案可能就在 Auth0 的一篇[文章](https://auth0.com/docs/security/store-tokens#don-t-store-tokens-in-local-storage)里。他们的论点是你不应该在本地存储中存储令牌。他们根据你的应用的情况提供了一些选择，但是文章的最后一部分适用于我。

> 如果您有一个没有相应后端服务器的单页应用程序(SPA ),您的 SPA 应该在登录时请求新的令牌，并将它们存储在内存中，而不需要任何持久性。为了进行 API 调用，您的 SPA 将使用令牌的内存副本。

没有持久性意味着没有本地存储，但是我们可以使用 Netlify Identity 小部件中的`netlifyIdentity`来合并用户状态，而不是使用本地存储。在这样做的时候，我们在某种程度上使用 Netlify 作为后端，因为它们处理身份验证和相关的工作流，所以只有在需要时让服务使用用户身份验证信息来融合状态才有意义。

获取用户信息的函数是`netlifyIdentity.currentUser();`。下面是一个使用它来合成用户状态的基本例子:

```
import netlifyIdentity from "netlify-identity-widget"; 

netlifyIdentity.init(); 
const currentUser = netlifyIdentity.currentUser(); 

export const state = () => ({ 
  currentUser: currentUser 
}); 

export const mutations = { 
  SET_USER(state, currentUser) { 
    state.currentUser = currentUser; 
  } 
}; 

export const actions = { 
  setUser: ({ commit }, payload) => { 
    commit('SET_USER', payload) 
  } 
}; 
```

当用户没有登录时，那么`netlifyIdentity.currentUser();`将返回`null`。`SET_USER`变异会将状态设置为 Netlify Identity 的登录动作返回的 currentUser，也可以在注销动作后将其设置为`null`。如果页面被刷新或关闭并重新访问，那么`netlifyIdentity.currentUser();`将根据它认为适合 SPA 的内容来恢复用户状态。

见[更新的示例](https://nuxt-netlify-identity-spa.netlify.com/)的示例 Nuxt.js SPA 与此实现([源](https://github.com/jeremywynn/nuxt-netlify-identity))。