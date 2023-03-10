# 在几分钟内将国际化添加到 Nuxt.js 应用程序中

> 原文：<https://dev.to/christopherkade/adding-internationalization-to-your-nuxt-js-applications-39jd>

许多前端开发人员经常害怕实现国际化(通常称为`i18n`)。在 it 方面，设置、即时添加新语言& UX 通常是主要关注点。

谢天谢地， [Nuxt.js](https://nuxtjs.org) 让整个过程变得极其简单。在这篇短文中，我将一步一步地介绍如何为 Nuxt 应用程序设置 i18n。

成品可以在 Codesandbox [这里](https://codesandbox.io/s/nuxt-i18n-example-sppzc)找到。

### 第一步:安装`vue-i18n`并设置好

我们将使用众所周知的 [vue-i18n](https://github.com/kazupon/vue-i18n) 包来处理国际化。

从安装开始:

```
# Using npm
npm install vue-i18n

# Using yarn
yarn add vue-i18n 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的配置文件中将其定义为一个插件:

```
// nuxt.config.js

export default {
  // ...

  plugins: ["~/plugins/i18n.js"],

  // ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们现在需要创建前面提到的`i18n.js`文件来配置我们的插件:

```
// plugins/i18n.js

import Vue from "vue";
import VueI18n from "vue-i18n";

// Tell Vue to use our plugin
Vue.use(VueI18n);

export default ({ app }) => {
  // Set the i18n instance on app
  // This way we can use it globally in our components through this.$i18n
  app.i18n = new VueI18n({
    // Set the initial locale
    locale: "en",

    // Set the fallback locale in case the current locale can't be found
    fallbackLocale: "en",

    // Associate each locale to a content file 
    messages: {
      en: require("~/static/content-en.json"),
      fr: require("~/static/content-fr.json")
    }
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记创建包含每种语言的文本值的`json`文件。在我们的例子中，我们可以有:

```
//  static/content-en.json  {  "title":  "Hello, how are you?"  } 
```

Enter fullscreen mode Exit fullscreen mode

和

```
//  static/content-fr.json  {  "title":  "Bonjour, comment allez-vous?"  } 
```

Enter fullscreen mode Exit fullscreen mode

我们将能够访问我们组件中的每一个值，就像这样:

```
// Will return the correct string based on the current locale
this.$t("title") 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:动态改变我们的语言环境

当我们需要改变语言时，我们所要做的就是更新`i18n`上下文对象的`locale`属性。

这里有一个方法可以解决这个问题:

```
changeLanguage(lang) {  
  // Change the i18n context object's locale
  // This makes it so the correct locale file is used
  this.$i18n.locale = lang;
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是这个方法在组件上下文中的应用:

```
<template>
  <section>
    <h1>{{ title }}</h1>

    <div>
      <button @click="changeLanguage('en')">EN</button>       
      <button @click="changeLanguage('fr')">FR</button>
    </div>
  </section>
</template>

<script>
export default {
  computed: {
    title() {
      // this.$t("title") returns the value of our title attribute in our JSON file
      // The correct file is selected based on the locale value
      // If it was an object, we could access its attributes like so: this.$t("myObject").myAttribute
      return this.$t("title");
    }
  },
  methods: {
    /**
     * Called when a language button is clicked
     * Changes the i18n context variable's locale to the one selected
     */
    changeLanguage(lang) {  
      this.$i18n.locale = lang;
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### 第三步:等等，没有第三步？

是的，这就是在 Nuxt 应用程序中处理 i18n 所需要知道的全部内容。当然，在官方的[文档中可以看到很多定制用户体验的方法](https://kazupon.github.io/vue-i18n/introduction.html)。

我希望这有助于你们中的一些人在 Nuxt 项目的背景下理解我们的 i18n。请随时关注我，了解我将来会写的关于 Javascript、React、Vue & CSS 的文章。

Twitter 绝对是查看我每日分享内容的最佳地方，所以请随意👋看着我。