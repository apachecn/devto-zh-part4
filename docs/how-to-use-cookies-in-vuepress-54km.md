# 如何在 VuePress 中使用 cookies

> 原文：<https://dev.to/therealdanvega/how-to-use-cookies-in-vuepress-54km>

最近，我一直致力于将大量文档从 Gitbook 迁移到 VuePress。如果您对如何开始使用 VuePress 或我用来确定它是否对我们有用的过程感到好奇，请联系我并告诉我。

本文假设你有一些使用 VuePress 的经验，所以我们可以跳过一些入门知识，直接进入手头的问题。我们将详细介绍这一点，但简而言之，我们将学习如何在 VuePress 中使用 cookies。

我有一些需要确保 VuePress 能够处理的要求。在这篇文章中，我将谈论其中的一个需求，我遇到的问题以及我最终是如何解决它的。

## Gitbook 迁移

在我们深入一些代码之前，我们需要谈谈需求和我遇到的问题。在文档的当前版本中，我们实际上基于编程语言构建了多个版本。我们可能有一个概念，我们解释，然后展示代码样本在任何数量的语言。

在 Gitbook 中，您可以创建一个变量，设置一个默认值，然后在您的 markdown 模板中使用该变量，如下所示:

```
% if book.language === "JavaScript" %

```js
class Greeter {
  constructor(message) {
    this.message = message;
  }
  greet() {
    return `Hello, ${this.message}`;
  }
}
```

% elif book.language === 'TypeScript' %

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}
```

% endif %

```

当你访问生成的 HTML 时，我们只会显示你感兴趣的版本。这可以通过使用标签来解决，但是有些情况下我们会有条件地显示整个部分，所以不仅仅是代码。

当您运行构建时，您会将该语言作为参数传入，并为该语言构建文档。这是可行的，但是拥有相同文档的多个版本意味着缓慢的构建时间和生产中不必要的重复。

## MMMMMMMMMM 饼干

[![](img/1bd2a9b5929c55704a333f7fb8fd3754.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XxThzO68--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdubkuv024i41ire9ts7.jpeg)

我不知道你怎么想，但当我开始思考这个问题时，我马上想到了饼干。这部分是因为我当时很饿，但我也知道这是解决我问题的好办法。

对我来说，这是一个游客的偏好，他们可以随时改变。就像 Gitbook 解决方案一样，当用户第一次访问站点时，我可以通过在用户机器上放置一个 cookie 来创建一个默认值。然后我会给他们机会在任何时候改变这一点。

### 检视 cookies

在我第一次尝试解决这个问题时，我带来了一个名为`vue-cookies`的包。我意识到创建一个 cookie 并不困难，但是拥有一个好的 API 来管理 cookie 带来的一切是一件好事。快速浏览文档后，它看起来很容易安装，所以我继续将它添加到我的项目中。

```
npm install vue-cookies 
```

在一个普通的 Vue 应用程序中，我会跳到`main.js`并添加以下内容。

```
import Vue from 'vue'
import VueCookies from 'vue-cookies'

// install the plugin
Vue.use(VueCookies)

// we want this cookie to last for 120 days
VueCookies.config('120d')

// set global cookie
VueCookies.set('language','JavaScript'); 
```

但是这是 VuePress，而我没有一个`main.js`,那么我如何才能挂钩到现有的 Vue 实例呢？

### App 级增强功能

由于 VuePress 应用程序是一个标准的 Vue 应用程序，您可以通过创建一个文件`.vuepress/enhanceApp.js`来应用应用程序级别的增强功能，该文件将被导入到应用程序中(如果存在的话)。该文件应导出默认的钩子函数，该函数将接收包含一些应用程序级别值的对象。你可以使用这个钩子来安装额外的 Vue 插件，注册全局组件，或者添加额外的路由器钩子:

```
export default ({
  Vue, // the version of Vue being used in the VuePress app
  options, // the options for the root Vue instance
  router, // the router instance for the app
  siteData // site metadata
}) => {
  // ...apply enhancements to the app
} 
```

[https://vue press . vue js . org/guide/basic-config . html # app-level-enhancements](https://vuepress.vuejs.org/guide/basic-config.html#app-level-enhancements)

这听起来正是我所需要的，所以我快速设置了`.vuepress/enhance.App.js`并添加了以下代码。这允许我调用`Vue.use()`来安装插件并设置一个默认 cookie(如果不存在的话)。

```
import VueCookies from 'vue-cookies'

export default ({ Vue, options, router, siteData }) => {

  Vue.use(VueCookies)
  VueCookies.config('120d')
  if( !$cookies.isKey('language') ) {
    VueCookies.set('language','JavaScript');
  }

} 
```

这实际上非常有效，我对这个解决方案非常满意。直到我去构建文档的生产版本作为测试。当我运行`vuepress build`时，我开始得到一些错误，说窗口没有被定义，我马上知道我忘记了一个重要的细节。

### 浏览器 API 限制

因为在生成静态构建时，VuePress 应用程序是在 NodeJS 中由服务器呈现的，所以任何 Vue 的使用都必须符合[通用代码要求](https://ssr.vuejs.org/en/universal.html)。简而言之，确保只访问 beforeMount 或 mounted 钩子中的浏览器/DOM API。

为了在导入时使用假定为浏览器环境的代码，您需要在适当的生命周期钩子中动态导入它们:

```
<script>
export default {
  mounted () {
    import('./lib-that-access-window-on-import').then(module => {
      // use code
    })
  }
}
</script> 
```

[https://v1 . vue press . vue js . org/guide/using-vue . html # browser-API-access-restrictions](https://v1.vuepress.vuejs.org/guide/using-vue.html#browser-api-access-restrictions)

有了这些知识，我试图把一些东西拼凑在一起，但是失败了。在我的头撞在桌子上几次后，我意识到我需要从一个不同的角度来看这个问题，所以它对我来说又回到了绘图板。

### 重新思考解决方案

当我开始更多地考虑这个问题时，我意识到我只需要在应用程序为访问者加载时运行一次脚本。此时，我可以检查是否有 cookie，如果没有，就创建一个默认的 cookie。从那里，我总是可以给用户从另一个页面更改语言的选项。

所以现在的问题是，我如何在应用程序中添加一个脚本来执行这个逻辑。我记得在前面的`.vuepress/config.js`中，我用下面的代码给网站添加了一个 favicon。我做了一些调查，果然我也可以通过把脚本放到`.vuepress/public/scripts`文件夹中来把它推到这里。

```
head: [
  ['link', { rel: 'icon', href: '/favicon.png' }],
  ['script', { src: '/scripts/cookies.js' }]
], 
```

VuePress 应用程序是一个单页应用程序，因此只需运行一次。每个后续请求都是通过应用程序加载的，所以这个脚本只会被调用一次。对于这个问题来说，这实际上是可以的，所以我继续构建了一个小的 cookie 脚本。

```
document.addEventListener("DOMContentLoaded", () => {

  // if a cookie has not been defined and they aren't on the language selection page
  if( !cookieExists('language') && window.location.pathname != '/language.html' ) {
    // a cookie doesn't exist yet, we need to create one with a default language.
    document.cookie = `language=javascript;max-age=${60*60*24*120};path=/`;
    // we are setting a default cookie but we still want the visitor to have a chance to change it
    window.location.href="/language.html";
  }

})

function cookieExists(name) {
  return document.cookie.split(';').filter((item) => item.trim().startsWith(`${name}=`)).length;
} 
```

该脚本将检查 cookie 是否存在，如果不存在，它将创建一个默认的 cookie 并转到语言选择页面。这只不过是一个简单的降价页面，带有一些副本和一个我为更改 cookie 值而构建的自定义组件。

```
<template>
  <div class="language">
    <p>Current Language: {{ currentLanguage }}</p>

    <select @change="updateLanguage($event)">
      <option value="">Change Language</option>
      <option value="javascript">JavaScript</option>
      <option value="typescript">TypeScript</option>
    </select>

  </div>
</template>

<script>
export default {
  name: 'language-select',
  data() {
    return {
      languages: [
        { label: 'JavaScript', value: 'javascript' },
        { lagel: 'TypeScript', value: 'typescript' }
      ],
      currentLanguage: ''
    }
  },
  methods: {
    updateLanguage(event) {
      const language = event.target.value;
      this.setCookie(language);
      this.currentLanguage = language;
    },
    setCookie(value) {
      document.cookie = `language=${value};max-age=${60*60*24*120};path=/`;
    },
    getCookie() {
      return document.cookie.replace(/(?:(?:^|.*;\s*)language\s*\=\s*([^;]*).*$)|^.*$/, "$1");
    },
  },
  mounted() {
    this.currentLanguage = this.getCookie();
  }
}
</script> 
```

## 自定义组件读取 cookie

现在一切就绪，我需要一种方法来有条件地检查 markdown 中用户设置的语言。从组件开始可能有意义，但我喜欢从我希望我的标记看起来像什么开始。如果我在 markdown 中，并且如果用户的语言选择是 TypeScript，我想只显示下面的代码，我会设想编写下面的标记。

```
<code-block langugage="typescript">
``ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}
``
</code-block> 
```

这让我可以在正常的降价代码栏中编写代码，这让我很高兴。为了传递组件标签之间的内容，你可以使用一个 slot，我可以使用一个变量来决定我是否应该显示内容。最后一个难题是读取 cookie 值，我们可以在 mounted 方法中完成这项工作，因为我们知道此时 DOM 是可用的。如果你用下面的代码创建`.vuepress/components/CodeBlock.vue`，上面的代码应该可以工作。

```
<template>
  <div class="code-block">
    <slot v-if="display"/>
  </div>
</template>

<script>
export default {
  name: 'code-block',
  props: {
    language: String
  },
  data() {
    return {
      display: false
    }
  },
  methods: {
    getCookie() {
      return document.cookie.replace(/(?:(?:^|.*;\s*)language\s*\=\s*([^;]*).*$)|^.*$/, "$1");
    },
  },
  mounted() {
    const cookieValue = this.getCookie();
    this.display = cookieValue === this.language;
  }
}
</script> 
```

## 结论

我已经写了很长时间的代码，所以通常当我遇到一个问题时，它是我一次又一次解决的问题。结合客户端和服务器的静态站点生成器的想法提出了我以前没有遇到过的问题，这很令人兴奋。如果你对此有更好的解决方案，我很想听听。我希望其他人从这篇文章中有所收获，一如既往地做朋友....

快乐编码

丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*