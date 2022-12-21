# 与 CORS、Nuxt 和 Webmentions 的疯狂冒险

> 原文：<https://dev.to/jeremywynn/a-crazy-adventure-with-cors-nuxt-and-webmentions-5fhl>

这个目标本身听起来并不令人畏惧:在我的单个博客帖子页面上整合类似的[web references](https://webmention.net/)部分。我在很多地方看到过它们，甚至找到了 Remy Sharp 的一篇名为[发送外发网络提及](https://remysharp.com/2019/06/18/send-outgoing-webmentions)的文章，解释了如何设置接收网络提及和补充链接。我完成了所有的先决条件，在登录[web reference . io](https://webmention.io/)后，我能够看到我的 web references。

## 在我的网站上获取它们

现在是实施的问题。有几个选项，如 Javascript 插件[web reference . js](https://github.com/resonance-cascade/webmention.js)和嵌入如[一个 web reference 端点](https://webmention.herokuapp.com/)。还有 API——web reference . io 甚至有自己的 [API](https://github.com/aaronpk/webmention.io#api) ,在那里我可以检索到一个 web reference 列表，比如[https://webmention.io/api/mentions.jf2?target = https://web remote . io](https://webmention.io/api/mentions.jf2?target=https://webmention.io)。那个 API 给了我 JSON，我可以很容易地用它做一些事情，很多事情。

## 用 Nuxt 实现

在我将必要的元素放入`<head>`并设置我的 Vue 模板以利用 JSON 之后，下一个任务是将这个 JSON 放入我的站点。我已经使用了 [asyncData 方法](https://nuxtjs.org/guide/async-data/)来获取我的博客文章的 JSON(来自物理文件)，所以我尝试在这里添加获取 web reference JSON，如下所示:

```
async asyncData({ $axios, params, payload, route }) { 
  const token = process.env.webmentionsToken; 
  let likes = null; 
  try { 
    likes = await $axios.$get('https://webmention.io/api/mentions.jf2', { 
      params: { 
        target: 'https://jeremywynn.com' + route.fullPath + '/', 
        token: process.env.webmentionsToken, 
        'wm-property': 'like-of', 
        'per_page': 20 
      } 
    }); 
  } catch(error) { 
    console.log(error); 
  } 
  if (payload) { 
    return { blogPost: payload, likes}; 
  } else { 
    return { 
      blogPost: await import(`~/assets/content/blog/${params.blog}.json`), likes
    }; 
  } 
}, 
```

Enter fullscreen mode Exit fullscreen mode

成功了！

## 没那么快

我注意到每当我加载或刷新页面时它都在工作，但是当我点击我的站点时 JSON 没有加载。我查看了 Firefox 开发工具控制台，发现了这个错误:

> 跨源请求被阻止:同一源策略不允许读取 https://webmention.io/api/mentions.jf2?的远程资源 target = https:% 2F % 2Fje…token = blahblahblah & WM-property = like-of & per _ page = 20。(原因:CORS 标题“访问控制允许来源”缺失)。

不过，Chrome、Internet Explorer 和 Opera 似乎都没问题。是 Firefox 和 Safari 在 CORS 身上遇到了这个问题。我在 Firefox 网络面板中注意到，请求是用`OPTIONS`方法发出的，而 Chrome 总是使用`GET`。`OPTIONS`方法是如何发出[预检请求](https://www.html5rocks.com/en/tutorials/cors/)的，但是 web reference . io 的 API 服务器似乎没有在其预检响应中包含 Firefox 或 Safari 想要的必要元素(如`Access-Control-Allow-Origin`)。

我需要`application/json`，没有办法让这个简单的 HTTP 请求让 Firefox/Safari 不使用`OPTIONS`方法。

## 我以为我知道该做什么

这种奇怪的行为正在发生，因为`asyncData`在服务器端被调用一次(在第一次请求 Nuxt 应用程序时),在导航到更远的路线时被调用一次(当我手动刷新页面时，网页提及出现在 Firefox/Safari 中)。我知道 [@nuxtjs/proxy](https://github.com/nuxt-community/proxy-module#readme) 可以用来让外部请求看起来像是来自你自己的站点。

我有这个。所以我对`nuxt.config.js` :
做了这次更新

```
modules: [
  '@nuxtjs/axios', 
  '@nuxtjs/proxy'
], 
proxy: { 
  '/api/mentions.jf2': { 
    target: 'https://webmention.io' 
  } 
}, 
```

Enter fullscreen mode Exit fullscreen mode

在更新了组件中 asyncData 区域下的 axios 调用以删除`https://webmention.io/`部分后，我点击了我的站点，所有地方都工作正常！我兴高采烈地将所有更新(经过大量研究和工作)推送到 Netlify，但随后我注意到了这个关键的@nuxtjs/proxy 警告:

> ⚠在生成/静态模式下不起作用！

***#^%！* *

## 还能怎么办

我无法控制 web reference . io API 服务器。CORS 问题不是 axios 的错。传递 axios 调用我在绝望中找到的任何配置选项都不起作用。至少还有其他一些选择:

1.  使用 JSONP:web reference . io API 通过包含`jsonp`参数来支持它。过去它曾被用来绕过跨起源分享痛苦。
2.  使用另一个 API，比如[一个 web 提及端点](https://webmention.herokuapp.com/documentation.html)。也许这个服务器会以不同的方式处理来自请求的`OPTIONS`方法。
3.  使用前面提到的 Javascript 或 HTML 嵌入方法
4.  使用并托管我自己的实例 [CORS Anywhere](https://github.com/Rob--W/cors-anywhere/)
5.  等待类似于[战争专家](https://warpist.com/)的东西
6.  哭？

为什么这玩意不能工作？

## 答案:中间件

我不完全确定现在如何，但是使用[中间件](https://nuxtjs.org/api/pages-middleware/)是可行的，因为它使得对 web reference . io API 的请求总是利用`GET`，即使在 Firefox/Safari 中也是如此。

在`middleware/webmention.js` :

```
 import axios from 'axios' 

export default async function ({ route, store }) {
  const likes = await axios.get('https://webmention.io/api/mentions.jf2', {
    params: { 
      target: 'https://jeremywynn.com' + route.fullPath + '/', 
      token: process.env.webmentionsToken, 
      'wm-property': 'like-of', 
      'per_page': 20 
    } 
  }); 
  store.dispatch('setWebMentions', likes.data); 
} 
```

Enter fullscreen mode Exit fullscreen mode

Vuex store 是我如何为我的网站提供这一网络提及 JSON。在`store/index.js` :

```
export const state = () => ({ 
  webmentions: null 
}); 

export const mutations = { 
  SET_WEB_MENTIONS(state, webmentions) { 
    state.webmentions = webmentions; 
  } 
}; 

export const actions = { 
  setWebMentions({ commit }, webmentions) { 
    commit('SET_WEB_MENTIONS', webmentions); 
  } 
}; 
```

Enter fullscreen mode Exit fullscreen mode

在我的页面组件`.vue`文件中:

现在事情将由中间件处理，所以我从`asyncData`中删除了 axios 和 likes 相关代码。我添加了一个`computed`条目，表示喜欢从商店得到它们:

```
computed: { 
  likes() { 
    return this.$store.state.webmentions; 
  } 
}, 
```

Enter fullscreen mode Exit fullscreen mode

并确保调用组件中的中间件:

`middleware: 'webmention',`

现在，我可以四处点击，让 webmentions 正确加载，没有任何 CORS 问题。