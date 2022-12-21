# 将布尔玛和 FontAwesome 5 与 Nuxt.js 一起使用

> 原文：<https://dev.to/overscoremedia/use-bulma-and-fontawesome-5-with-nuxt-js-11le>

### 那么，您使用 Nuxt.js 创建了一个新的样板站点，现在做什么呢？

嗯，也许你想使用 CSS 框架来开始设计你的网站。当然，使用框架有它自己的优点和缺点，但是老实说，这是一个很好的起点。

如果你还没听说过布尔玛，我推荐你去看看，因为它很棒。它的类名很有意义，它有很多现成的组件和预置，但不会太多，如果你以前没有使用过它，它会让人不知所措(看看你， [Vuetify](https://vuetifyjs.com) ...好吧，Vuetify 也很棒，绝对值得花时间去习惯它，但是这篇文章是关于布尔玛的，所以就这样吧。)

无论如何，一旦你的网站设置好了，在你网站的项目文件夹中打开你最喜欢的代码编辑器，将布尔玛添加到你的`package.json` :

```
$ yarn add bulma 
```

Enter fullscreen mode Exit fullscreen mode

接下来，您需要通过导入将布尔玛添加到您的站点中。实际上有几种方法可以做到这一点，但我将要解释的方法似乎对我很有效，所以请随意跟随。

1.  在站点的`assets/scss/`文件夹中创建一个`main.scss`文件。
2.  使用以下代码行导入布尔玛的实用程序文件:

```
@import "~bulma/sass/utilities/_all"; 
```

Enter fullscreen mode Exit fullscreen mode

1.  添加您的定制(如定制品牌颜色，在这种情况下，请参见[https://bulma.io/documentation/customize/](https://bulma.io/documentation/customize/)了解更多信息):

```
/* Custom colours:
   (These are what we use on our website,
   https://overscorestudios.ml,
   so feel free to experiment with your own
   colours/variables - see https://bulma.io/documentation/customize/
*/

$primary: hsl(242, 100%, 76%);
$primary-invert: findColorInvert($primary);

$info: hsl(337, 55%, 58%);
$info-invert: findColorInvert($info);

$light: hsl(275, 20%, 83%);
$light-invert: findColorInvert($light);

$dark: hsl(0, 0%, 15%);
$dark-invert: findColorInvert($dark);

$link: hsl(242, 100%, 76%);
$link-invert: findColorInvert($link);

$link-hover: hsl(229, 19%, 66%);
$link-focus: hsl(229, 19%, 66%);

$strong-color: hsl(229, 19%, 66%);

$colors: (
    "white": ($white, $black),
    "black": ($black, $white),
    "link": ($link, $link-invert),
    "light": ($light, $light-invert),
    "dark": ($dark, $dark-invert),
    "primary": ($primary, $primary-invert),
    "info": ($info, $info-invert),
    "success": ($success, $success-invert),
    "warning": ($warning, $warning-invert),
    "danger": ($danger, $danger-invert)
); 
```

Enter fullscreen mode Exit fullscreen mode

1.  导入布尔玛的其余部分:

```
// The rest of Bulma
@import '~bulma'; 
```

Enter fullscreen mode Exit fullscreen mode

1.  让 Nuxt 在你所有的自定义中导入你的自定义样式。Vue 文件通过安装 [@nuxtjs/style-resources](https://github.com/nuxt-community/style-resources-module) :

```
$ yarn add @nuxtjs/style-resources 
```

Enter fullscreen mode Exit fullscreen mode

1.  最后，通过在两个地方编辑`nuxt.config.js`文件，让 Nuxt 识别您的`main.scss`文件:

```
 css: [
    '~/assets/scss/main.scss',
  ],

  // ^ Add your main.scss file to Nuxt's Global CSS array 
```

Enter fullscreen mode Exit fullscreen mode

```
 modules: [
    '@nuxtjs/style-resources'
  ]

  // ^ Make sure to add @nuxtjs/style-resources to Nuxt's modules array
  // (Remember to add a comma if you have more than one module already) 
```

Enter fullscreen mode Exit fullscreen mode

## 凉豆

现在，没有任何额外的欺骗，你应该能够使用布尔玛的所有类-随着任何自定义颜色-在你的。Vue 组件！

说到这里，我们来为 FontAwesome 5 图标创建一些组件吧！

## 字体谁谁谁？

FontAwesome 5 是一种免费/高级图标字体，非常流行，在许多网站上使用。它包括许多主要品牌的图标、表情符号和其他大量的图标，这些在你建设网站时无疑是有用的。

同样，有很多方法可以将 FontAwesome 5 添加到你的 Nuxt 站点，如果你在互联网上有任何替代方法的建议，请在下面的评论中留下。:]

## 大进口

为了使用 FontAwesome，您必须导入它。在这种情况下，我们将使用他们提供的 SVG 图标集，尽管还有一个 webfont 和其他一些东西。

下面是安装您需要的节点包的命令:

```
$ yarn add @fortawesome/fontawesome-svg-core &&
$ yarn add @fortawesome/free-brands-svg-icons &&
$ yarn add @fortawesome/free-solid-svg-icons &&
$ yarn add @fortawesome/vue-fontawesome &&
$ yarn add nuxt-fontawesome 
```

Enter fullscreen mode Exit fullscreen mode

使用相同的命令随意添加所有的包；这样展示给他们更容易。基本上，你需要 FontAwesome 5 Free 的 SVG 品牌/实体图标包，以及它们的导入器包(一般用于 JavaScript，具体用于 Vue 和 Nuxt)。

现在，将`nuxt-fontawesome`添加到你的`nuxt.config.js`模块列表:

```
 modules: [
    '@nuxtjs/style-resources',
    'nuxt-fontawesome' // Imports FontAwesome 5 SVG icons
  ] 
```

Enter fullscreen mode Exit fullscreen mode

接下来，向您的`nuxt.config.js`添加一个数组，类似于:

```
 fontawesome: {
    imports: [
      {
        set: '@fortawesome/free-solid-svg-icons', // Solid icons
        icons: ['faCookieBite', 'faCommentDots', 'faEnvelope', 'faGrinWink', 'faHeart']
      },
      {
        set: '@fortawesome/free-brands-svg-icons', // Brand icons
        icons: ['faDev', 'faFacebook', 'faTwitter', 'faInstagram', 'faYoutube', 'faGithub']
      }
    ]
  }, 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可能已经注意到，在上面的代码片段中，我从 Solid 集合和 Brands 集合中导入了一些图标。技术上来说*可以*全部导入，但是我真的建议不要这样做(除非出于某种愚蠢的原因，你真的需要*全部*，因为保持你的导入更小可以保持你的站点的包更小，你的加载时间更快！

话虽如此，你可以在 FontAwesome 的网站上查找你想用什么图标[。确保使用以下模式导入任何图标:](https://fontawesome.com/icons?d=gallery&m=free) 

```
Icon Name: adobe
Import Name: faAdobe (in the brands icons array)

Icon Name: align-left
Import Name: faAlignLeft (in the solid icons array) 
```

Enter fullscreen mode Exit fullscreen mode

如果你似乎找不到窍门，你总是可以窥视你站点的`node_modules`文件夹，在`@fortawesome/free-brands-svg-icons/`或`@fortawesome/free-solid-svg-icons`文件夹中，寻找你想要导入的图标的名字——你会注意到每一个都有两个，因为 [TypeScript](https://www.typescriptlang.org/) 存在，并且每个图标的 JS 文件都有一个相应的 TypeScript 定义文件。

## 快到了！

现在，您导入了图标，但是如何让它们在您的？Vue 文件？

我选择为它们创建自定义组件，`Fas.vue`为实体图标，`Fab.vue`为品牌图标:

```
<!--- components/Fas.vue -->

<!---
  FontAwesome 5 Free Solid Icon Component

  Usage Example:

  <Fas i="arrow-down" />

  Props:
  - i: The FontAwesome 5 Free Solid icon name (like "arrow-up" or "ad")
  - classes: CSS classes to add to the icon

  Caveats:
  - Each icon has to be pre-loaded in nuxt.config.js or it won't show up

-->
<template>
  <font-awesome-icon :class="`${classes}`" :icon="['fas', i]" />
</template>

<script>
export default {
  props: {
    i: {
      type: String,
      required: true,
      default: 'arrow-down'
    },
    classes: {
      type: String,
      required: false,
      default: 'icon is-medium'
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

```
<!--- components/Fab.vue -->

<!---
  FontAwesome 5 Free Brands Icon Component

  Usage Example:

  <Fab i="facebook" />

  Props:
  - i: The FontAwesome 5 Free Brands icon name (like "twitter" or "reddit")
  - classes: CSS classes to add to the icon

  Caveats:
  - Each icon has to be pre-loaded in nuxt.config.js or it won't show up

-->
<template>
  <font-awesome-icon :class="`${classes}`" :icon="['fab', i]" />
</template>

<script>
export default {
  props: {
    i: {
      type: String,
      required: true,
      default: 'facebook'
    },
    classes: {
      type: String,
      required: false,
      default: 'icon is-medium'
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

有必要解释一下这些是如何/为什么工作的吗？嗯，可能不会。我真的不认为我很擅长教一个完全陌生的人使用 Vue，尽管我可以说我只花了大约 3 个月的时间就有信心使用 Vue，摆弄它的开关。老实说，直到我开始使用 Nuxt，它才开始运转，在几周的断断续续的试验中，我想我对它的工作原理有了一个很好的了解。

我对 Vue-newbies 的建议是，老实说，尝试使用 Vue(或 Nuxt，因为我真的认为它为你做了很多“脏活”，让你专注于编写高质量、可读、可测试、强大的代码)从头开始构建一个网站。如果你已经知道如何建立一个网站，那么它应该不会太难-只要去那里试一试。如果你不知道从哪里开始，我再怎么推荐 [FreeCodeCamp](https://www.freecodecamp.org/) 也不为过。

## 但如何尽管？

这里有一个在野外使用的`Fab.vue`组件的例子:

```
<a href="https://facebook.com/overscorestudios" target="_blank" rel="noreferrer" aria-label="Like us on Facebook">
  <Fab i="facebook" />
</a> 
```

Enter fullscreen mode Exit fullscreen mode

不要忘记导入页面/组件/布局的`<script>`标签中的所有定制组件:

```
import Fab from '~/components/Fab.vue

export default {
  components: {
    Fab
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

而且，你可能已经注意到，我在标签`<a>`中添加了一个`aria-label`，其中有`<Fab />`。我很确定这做了我想让它做的事情——也就是说，让屏幕阅读器用户理解他们看不到的图标实际上意味着什么。再说一次，我不是这方面的专家，但据我所知，它确实发挥了作用。确保图标总是有这样的可访问的备用图标。如果它们只是装饰性的，我猜你可能不需要它们，但是如果它们是用于按钮/链接或任何交互的东西，或者如果它们在你的页面上下文中很重要，它们真的应该有`aria-label` s 或类似的东西。还是那句话，欢迎在评论中讨论。

好了，现在你知道了！布尔玛和方太棒了应该开始运作了！

接下来，我将介绍如何让 PurgeCSS 工作(以保持您的包较小)，如何添加 cookie 同意横幅(以及使用 Matomo 进行道德跟踪)，如何向您的站点添加 Anime.js(用于漂亮的动画)，Typed.js(用于输入动画)和 Prism.js(用于语法突出显示)，以及如何使用 Netlify CMS 添加新闻页面等等。

与此同时，如果你正在创建自己的网站，可能是时候做出 Git 承诺了

```
git add .
git commit -am "Add Bulma CSS and FontAwesome 5"
git push 
```

Enter fullscreen mode Exit fullscreen mode