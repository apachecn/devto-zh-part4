# 打开灯

> 原文：<https://dev.to/westbrook/turning-up-to-lit-1o05>

> 这是一篇来自 Medium 的[2019 年 2 月 9 日的文章的交叉帖子，利用了我最近决定在我的写作中使用语法的优势(因此，在这里和那里做了一些小编辑)，如果你在那里看到它，感谢再次查看🙇🏽‍♂️，如果这是你第一次来，欢迎你。](https://medium.com/@westbrook/turning-up-to-lit-fd19aab2ce9a)

我内心深处有一种强烈的愿望，想开个玩笑说，要从 [Polymer 2.0](https://www.polymer-project.org/2.0/docs/devguide/feature-overview) 升级到 [LitElement](https://github.com/polymer/lit-element) 所需要的就是下面的差异:

```
- "polymer": "Polymer/polymer#^2.0.0",
+ "lit-element": "^2.0.0", 
```

这不仅会让我(和你)的工作变得更容易，而且简单地调高音量的委婉说法更适用，但这将是一个地狱般的麦克风下降，amirite？唉，我们的英雄必须走的路比那要长一点，然而，你们都会很高兴地听到，为了完成这次旅行，需要很少的魔法或超能力，如果有的话。

* * *

*(编者注:本文将自己定位为明确支持从 Polymer 2 到 LitElement 的升级。然而，重要的是认识到聚合物 2 和聚合物 3 之间的功能差异很小。如果您已经完成了这个转换，那么可以跳过下面的依赖关系管理和依赖关系使用部分，直接从 Polymer 到 LitElement 更新您的定制元素的实现。)*

* * *

在我们开始之前，让我们谈一谈我们将要升级的 Polymer 2.0 元素的代码来自哪里。

## 固执己见的元素

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [威斯布鲁克](https://github.com/Westbrook) / [生成器-聚合物-初始化-固执己见-元素](https://github.com/Westbrook/generator-polymer-init-opinionated-element)

### 聚合物 2.0 组件与一些方便纱脚本和文件结构的决定，为您的开发乐趣。

<article class="markdown-body entry-content p-5" itemprop="text">

# 生成器-聚合物-初始化-意见化-元素

> 聚合物 2.0 组件与一些方便纱脚本和文件结构的决定，为您的开发乐趣。

## 装置

首先，使用 [npm](https://www.npmjs.com/) 安装 [Yeoman](http://yeoman.io) 和 generator-polymer-init-opinious-element(我们假设你已经预装了 [node.js](https://nodejs.org/) )。

```
yarn global app polymer-cli
yarn global add yo
yarn global add generator-polymer-init-opinionated-element
```

然后生成您的新项目:

```
polymer init
? Which starter template would you like to use?
❯ opinionated-element - Polymer 2.0 component generator with some convinence Yarn scripts and file structure decisions for your developing pleasure
? What is your name? (You Name)
? What is your GitHub username or organization? (recent-org)
? What is the namespace that you would like to build this component into? (org)
? What would you like this element to be called? (name-of-element) //notice that web components have to have a hyphen in the name
 What does this element do? (nothing yet, but something awesome)  
```

…

</article>

[View on GitHub](https://github.com/Westbrook/generator-polymer-init-opinionated-element)

多年来，我已经使用了许多不同的技术来创建基于聚合物的 web 组件，但是最近决定采用`generator-polymer-init-opinionated-element`中应用的方法。该项目发布到 NPM，所以如果你想测试一下那里提供给你的东西，以便你了解这次升级的基线方面，请便。与我们的升级相关的生成过程的最重要部分如下:

1.  使用`custom-element.html` / `custom-element.js` / `custom-element-styles.html`文件结构，而不是通过单个`custom-element.html`来交付所有代码的单个文件组件
2.  [BrowserSync](https://browsersync.io/) 用于开发过程中服务代码
3.  [CircleCI](https://circleci.com/) 和 [Release It](https://webpro.github.io/release-it/) 应用于代码维护生命周期
4.  [Web 组件测试器](https://www.npmjs.com/package/web-component-tester)与[酱实验室](https://saucelabs.com/)一起用于 x-browser 测试
5.  一堆助手 NPM 脚本支持本地安装 [Bower](https://bower.io/) 而不是全局安装。

虽然代码维护生命周期和测试对任何项目都很重要，但这里的第一点可能是最引人注目的。许多人发现 Polymer 的单文件组件方法是它最积极的特性之一，在我与该库的早期交互中，这无疑是我最喜欢的特性之一。值得庆幸的是，当我们第一次开始合作时，一位[有见地的同事](https://github.com/caranicas)努力争取不使用它，因为它既简化了在 polyfilled 浏览器中的调试(当你以这种方式加载 JS 文件时，它们实际上在 Safari/Firefox/Edge 中被列为 JS 文件),又简化了向我一直在工作的[新 web 组件生成器](https://medium.com/@westbrook/generating-some-next-generation-web-components-58fadbb2a56a)中应用的技术的过渡。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [威斯布鲁克](https://github.com/Westbrook) / [发电机-easy-wc](https://github.com/Westbrook/generator-easy-wc)

### 用于开发、测试、记录和部署 web 组件的文件结构和 Yarn 脚本。

<article class="markdown-body entry-content p-5" itemprop="text">

# 生成器-easy-WC[![NPM version](img/779c541f13e95c828ffad064dc92eefd.png)](https://npmjs.org/package/generator-easy-wc)[![Build Status](img/fe5eaedae801676e19f6d1f16b55979b.png)](https://travis-ci.org/Westbrook/generator-easy-wc)[![Dependency Status](img/b2e04db4215698784ebdca3dc1c62deb.png)](https://david-dm.org/Westbrook/generator-easy-wc)

> 用于开发、测试、记录和部署 web 组件的文件结构和 Yarn 脚本。

## 装置

首先，用[纱](https://yarnpkg.com/en/)安装[约曼](http://yeoman.io)和`generator-easy-wc`(我们假设你已经预装了 [node.js](https://nodejs.org/) )。

```
yarn global add yo
yarn global add generator-easy-wc
```

## 初始化

然后生成您的新项目。在生成之前创建 git 项目允许`husky`适当地设置钩子。一旦将生成的代码提交给 master，我建议在建立了元素的功能后，立即进行分支，以便适当地使用 PR:

```
mkdir element-name-here
cd element-name-here
git init
yo easy-wc
# ... follow the prompts ...
git add .
git commit -am 'Generated Element'
git checkout -b element-name-here
```

## 提示

```
    _-----_     ╭──────────────────────────╮
   |       |    │      Welcome to the      │
   |--(o)--|    │      super-excellent     │
  `---------´   │     generator-easy-wc    │
 ( _´U`_ )    │        generator!        │
 /___A___\   /╰──────────────────────────╯
 |  ~  | 
 __'
```

…</article>

[View on GitHub](https://github.com/Westbrook/generator-easy-wc)

如果你想跳过可能令人讨厌的升级过程，直接从头开始制作新元素，我建议你不要从那里开始，而是在 [Open Web Components](https://open-wc.org/) 查看团队的优秀作品。

如果您准备好开始升级，让我们开始吧！

## 免责声明

如果你已经做了功课并深入研究了`generator-polymer-init-opinionated-element`，你可能会在下面的变化中发现一些简化。然而，我会尽我最大努力不忽略任何升级过程中显而易见的部分。

# 从简单的变化开始

总的来说，重构中我最喜欢的部分之一是删除一些东西，我们要做的大多数简单的改变就是删除！

```
// .circleci/config.yml

-      - run: yarn install:bower 
```

CI 中不再有`bower install`。

```
// config/.release-it.json

-    "package.json",
-    "bower.json"
+    "package.json" 
```

`bower.json`中不再有版本管理。

```
// package.json

-    "install:bower": "bower install",
-    "install:bower:clean": "rimraf bower_components && bower install",
-    "sync": "browser-sync . -w -c 'config/bs-config.js'",
-    "element:clean": "rimraf bower_components/ll-course-listing",
-    "element:directory": "mkdir bower_components/ll-course-listing",
-    "element:copy-files": "yarn copy bower_components/ll-course-listing",
-    "element:make": "yarn element:directory && yarn element:copy-files",
-    "prelive": "yarn analyze && yarn element:clean && yarn element:make",
-    "live": "yarn sync",
-    "copy": "cp -r *.html *.js analysis.json demo test",

// ...

-    "bower": "^1.8.2", 
```

不再有`bower`相关的依赖或脚本。*升级还将包括完全移除`bower.json`和`custom-element.html`，但是，让它们存在更长一点时间，以支持一些不太简单的升级步骤不失为一个好主意。*

您还会注意到，我们已经删除了控制 BrowserSync 的脚本。我注意到在过去使用它的时候收益不断减少，虽然这肯定与我的使用有关，而不是 BrowserSync 本身的功能，但我对这一删除非常满意。脚本中没有它也意味着您可以在命令行中对项目文件夹运行`rm config/bs-config.js`来清理不再需要的 BrowserSync 配置文件。

还有几个简单的补充应该采取:

```
// wct.conf.json

      "sauce": {
        // ...
        "browsers": [
+        {
+          "browserName": "firefox",
+          "platform": "Windows 10",
+          "version": "60"
+        },

// ...

           "browserName": "safari",
-          "platform": "OS X 10.12",
+          "platform": "OS X 10.13", 
```

我从来没有找到一个好方法来将本地可用的 Firefox 升级到 CircleCI 虚拟机，所以 Firefox 已经被排除在之前的 x-browser 测试之外，但这次通过 SauceLabs 的远程测试工具将它添加了回来，并特别注意了最后一个没有定制元素和影子 DOM APIs 的版本。macOS 平台版本凸起是为了避免早期 Safari 11 中不允许使用`async`作为导入/导出键的问题。

一个令人信服的论点可以肯定地指出这个测试是针对更新的版本或者更多的浏览器，所以请放心地继续添加你认为最适合你的项目的东西到这个非常小的基线中。

```
// polymer.json

{
+  "entrypoint": "index.html",
+  "shell": "custom-element.js",
+  "fragments": [
+    "*.js"
+  ],
+  "lint": {
-    "rules": ["polymer-2"]
-  }
+    "rules": ["polymer-3"]
+  },
+  "sources": [
+    "node_modules/@polymer/iron-demo-helpers/**/*.js",
+    "node_modules/@webcomponents/**/*.js",
+    "node_modules/wct-browser-legacy/**/*.js",
+    "index.html",
+    "demo/*"
+  ],
+  "moduleResolution": "node",
+  "npm": true
} 
```

这里最重要的添加是`moduleResolution`和`npm`属性。`moduleResolution: 'node'`将允许您通过节点样式*裸模块说明符*(即`'lit-element/lit-element.js'`)导入依赖关系，通过动态重写[URL](https://www.polymer-project.org/3.0/docs/tools/polymer-cli-commands)，您可以通过[聚合物项目的博客](https://www.polymer-project.org/blog/2018-02-26-3.0-preview-paths-and-names.html)了解更多信息。`npm: true`，[向聚合物客户端](https://www.polymer-project.org/3.0/docs/tools/polymer-cli-commands)概述其应如何获取依赖项的包名和位置。其余的变化支持`polymer serve`和`polymer build`在工作时决定读取/复制哪些文件。请注意，如果您在演示/测试中使用第三方代码，而这些代码可能不直接依赖于您的元素，那么您需要在`sources`条目中列出这些文件。

# 依赖关系管理

从 Polymer 2.0 到 LitElement 的最大飞跃之一是从由 [Bower](https://bower.io/) 包管理生态系统支持的 [HTML 导入](https://developer.mozilla.org/en-US/docs/Web/Web_Components/HTML_Imports)到由 [NPM](https://www.npmjs.com/) 支持的 [ES 模块](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/)的变化。简而言之，坏消息是 NPM 和 yarn 都没有像 Bower 那样做好管理 web 组件所需的平面依赖树的充分准备，但是好消息是现在从 JS 社区导入包到你的项目中比以前更容易了。虽然有很多东西可以添加到您的`bower.json`加班中，但在此次升级中最重要的部分是概述需要转移到`package.json` :
的依赖项

```
// bower.json

- "dependencies": {
-    "polymer": "Polymer/polymer#^2.0.0",
-    // as well as any other dependencies you might have been using
-  },
-  "devDependencies": {
-    "iron-demo-helpers": "PolymerElements/iron-demo-helpers#^2.0.0",
-    "web-component-tester": "Polymer/web-component-tester#^6.0.0",
-    "webcomponentsjs": "webcomponents/webcomponentsjs#^1.0.0",
-    "iron-component-page": "polymerelements/iron-component-page#^3.0.1"
-  }, 
```

这种移除中最复杂的部分是“任何其他依赖项”部分。也就是说，如果你依赖任何种类的第三方代码，你需要确保它是 ES 模块兼容的(或可转换的，即使[恶心](https://twitter.com/justinfagnani/status/995027541151776768))以使你能够进行升级。幸运的是，我处理的大多数依赖项都是内部的，希望你在这方面幸运，或者依赖于最新的项目，这样你就可以直接添加到`package.json` :

```
// package.json

+  "dependencies": {
+    // ...any other dependencies you might have been using
+    "@webcomponents/webcomponentsjs": "latest",
+    "lit-element": "^2.0.0"
+  },
+  "devDependencies": {
+   "@polymer/iron-component-page": "^4.0.0",
+   "@polymer/iron-demo-helpers": "^3.0.0",
+   "@polymer/test-fixture": "^4.0.0"

// ...

-    "wct-istanbub": "^0.0.7",
-    "web-component-tester": "^6.4.1"
+    "wct-istanbub": "^0.2.1",
+    "wct-mocha": "^1.0.0"
+  } 
```

为了便于转换，当您可以选择将一个依赖项从 Bower 版本直接升级到 NPM 版本时，我强烈建议您将此视为一个好兆头，并在此时运行它。一旦您的整个元素已经升级到 ES 模块，那么您就可以开始针对各种依赖项进行本地化升级，升级到更新的版本、更快的版本、更专用的版本等。这种转变的一个好处是可以直接访问 JS 社区的更大部分和他们开发的工具，所以希望通过这项工作有很多机会向你的元素开放。

# 依赖用法

从 HTML 导入到 ES 模块的迁移是从 Polymer 2 升级的核心部分，这是这种转换需要大量手工工作的地方。对于每个外部依赖项，您将转换这些导入，比如:

```
<link rel="import" href="../dependency/dependency.html"> 
```

致:

```
import {dependency} from ‘dependency/dependency.js'; 
```

其中一些非常简单(例如，定义和注册自己的子组件资产)，一些稍微简单一些(例如，以前将自己注入到全局范围中的依赖项，现在需要在 scopes 模块中针对本地使用进行更改)，一些将涉及跨组件的更深入、更普遍的更改。很难在这里涵盖所有的转换可能性，所以请在下面的评论中尽情分享你的英雄式重构故事吧！

## 基类

下面的代码使`Polymer.Element`基类可用于扩展:

```
<link rel="import" href="../polymer/polymer-element.html"> 
```

将在 JS 空间中被替换为类似的导入，这使得`LitElement`基类可用于扩展:

```
import {LitElement, html} from 'lit-element/lit-element.js'; 
```

这将允许从
更新类声明

```
class CustomElement extends Polymer.Element { 
```

到下面:

```
export class CustomElement extends LitElement { 
```

导出类声明可以更容易地扩展我们的组件，并根据需要利用高级技术(比如在外部 JS 文件中注册定制元素，甚至按需[，反复](https://github.com/skatejs/skatejs/blob/master/packages/define/src/index.ts))。

## 样式

以前，样式已经形成了一个`dom-module`，用于通过`<style include="custom-element-styles"></style>`语法包含在我们的元素中。

```
<dom-module id="<%= elementName %>-styles">
  <template>
    <style>
      :host {
        display: block;
        box-sizing: border-box;
      }
    </style>
  </template>
</dom-module> 
```

依赖 HTML 导入来使这些样式在我们的元素中可用:

```
<link rel="import" href="custom-element-styles.html"> 
```

在这里，我们对 ES 模块的迁移使这次更新看起来很像我们为使 LitElement 基类可用所做的工作:

```
import {style} from './custom-element-styles.js'; 
```

这段代码现在可以通过可用的[可构造样式表](https://wicg.github.io/construct-stylesheets/)来应用，这意味着不是定制元素的每个实例都有自己的`<style/>`标签，而是所有这些实例都可以通过`element.adoptedStyleSheets = [...]`共享一个标签。为了实现这一点，LitElement 提供了一个在`static get styles`中使用的`css`标签，它利用了这些功能，同时在不支持该功能的浏览器中提供了一个合适的后备。这意味着我们的独立样式文件现在看起来更像:

```
import {css} from 'lit-element/lit-element.js';
export const style = css`
  :host {
    display: block;
    box-sizing: border-box;
    contain: content;
  }
:host([hidden]) {
    display: none;
  }
`; 
```

而且可以应用在你的元素里，一拉:

```
static get styles() {
  return [style];
} 
```

返回的数组允许将多个样式声明组合到单个元素中，这使得跨多个元素共享样式更加容易。你可以在 LitElement 文档网站上了解更多关于这种技术的信息。

# 属性和特性

一旦你得到了你的外部依赖，你想要更新的最重要的元素内部概念之一就是你的元素的属性。与 Polymer 2 非常相似，LitElement 依赖于`static get properties()`来允许我们的定制元素将这些属性注册为`observedAttribues`，这使得`attributeChangedCallback`能够根据需要对这些属性的变化做出响应。在聚合物 2 中，以这种方式描述的属性可能如下:

```
static get properties() {
  return {
    everythingProperty: {
      type: String
      value: 'default value',
      reflectToAttribute: true,
      notify: true,
      computed: 'computeEverythingProperty(firstWord, secondWord)',
      observer: 'observeEverythingProperty'
    },
    firstWord: {
      type: String,
      value: 'default'
    },
    secondWord: {
      type: String,
      value: 'value'
    }
  };
} 
```

这里我们用默认值“default value”概述了`everythingProperty`是`String`。该值将被反映到属性`everything-property`中，因为当使用属性时，Polymer 在内部将 camel case 转换为 kabob case，并将通知其父对象其值的变化。此外，`everythingProperty`的值将由`firstWord`和`secondWord`的值计算得出，当这导致属性值改变时，将调用`observeEverythingProperty`方法来处理该改变。一直以来，因为它已经以这种方式注册了，所以我们可以依靠对`everythingProperty`的修改来勾选自定义元素的渲染管道。当更新到 LitElement 时，我们将继续使用`static get properties()`，但我们应该知道 LitElement 中可用的设置提供了一组更丰富、更高度可定制的选项，用于管理该属性符合属性符合渲染管道关系。

## 类型

当从 Polymer 2 转移到 LitElement 时，属性 getter 的`type`条目的内部工作方式[会让人感觉相当熟悉](https://lit-element.polymer-project.org/guide/properties)。在 Polymer 2 中为您完成的大部分工作都可以通过 LitElement 获得，它允许继续使用像`String`、`Number`、`Boolean`、`Array`和`Object`这样的类型，同时能够依靠您的基类来处理属性(总是字符串)和您期望从属性中得到的更丰富的数据结构之间的转换。除了这种支持之外，LitElement 在其属性描述符中有一个 [`converter`](https://lit-element.polymer-project.org/guide/properties) 条目，您可以在其中定制用于将 [`attribute`](https://lit-element.polymer-project.org/guide/properties) 转换为属性以及将属性转换为属性的处理，如果您希望监听某个属性的名称与您在组件内部使用来管理该数据的属性不同，还可以使用一个属性条目。`attribute`条目为我们的升级服务，因为它允许我们直接控制`everythingProperty`如何与属性相关联(默认为`everythingproperty`)。请注意，缺省情况下监听的属性中缺少大写字母，这是由 HTML 解析器的实际情况决定的。HTML 中的 Camel case 在 Chrome 中可能工作得很好，但在 FireFox 和 Edge 中的解析会给你带来麻烦，这就是为什么 Polymer 默认将其转换为 kabob case ( `everything-property`)。你现在有权力在这里自己做决定。这两个新条目极大地扩展了在元素属性中处理属性提供的数据的可能性。

## 值

在 LitElement 中，设置默认值的功能已被移除，以利于在`constructor()`中设置这些默认值。当从通过`value`条目设置缺省值的 Polymer 2 开始时，这可能有点令人惊讶，所以在您的代码中要注意这一点。这种改变可以被看作是为在不久的将来使用[公共类字段](https://www.chromestatus.com/features/6001727933251584)(Chrome 中已经有了)或者在未来使用 [ES7 装饰器](https://medium.com/google-developers/exploring-es7-decorators-76ecb65fb841)准备你的代码。(注意:[decorator](https://lit-element.polymer-project.org/guide/properties)目前可以通过 TypeScript 在 LitElement 中获得。)

## 反射属性

当设置为`true`时，`reflectToAttribute`条目将确保对属性的更改会反映在 DOM 中元素的相关属性中。[该功能](https://lit-element.polymer-project.org/guide/properties)通过`reflect`条目保留在 LitElement 中，该条目将与您的`type`或`converter`定义配对，以将您的属性应用管理回其相应的属性。

## 通知

当你的元素属性改变时，自动`notify`的能力，是 Polymer 2 双向绑定技术的关键部分，在 LitElement 中已经被默认移除。有一些[外部项目](https://www.npmjs.com/package/@morbidick/lit-element-notify)希望将这种功能混合回你的元素中，然而，在本文中，我们将在下面的“模板”部分中访问手动替换这种功能。

## 计算出来的

LitElement 中的`computed`条目已被完全删除。有许多不同的方法可以帮助您实现这种转变。哪一种最适合您的使用情形取决于许多不同的因素:

> ![Peter profile image](img/05e29a9436505ae9618d565454bbe152.png)彼得@ trading _ 彼得![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)都是计算属性在渲染()中的最佳方式吗？公司不会。每次触发渲染时重新计算属性？shouldUpdate 或 updateComplete 是更有效的模式吗？Esp。因为它提供了一个有变化的地图？
> [@聚合物](https://twitter.com/polymer)[@ justinfagnani](https://twitter.com/justinfagnani)2019 年 2 月 08 日下午 14:16[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1093876230548586496)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1093876230548586496)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1093876230548586496)0

> ![Justin Fagnani profile image](img/63af9a4f0f95df426167dd07b3ae6862.png)贾斯汀·法格纳尼@贾斯汀·法格纳尼![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)@ pkaske[@聚合物](https://twitter.com/polymer)看情况。如果是几个属性的简单表达，那么像 render()或 getter(我最喜欢的)这样的按需完全没问题。如果成本较高，那么您希望仅在输入发生变化时进行更新，无论是手动更新(changedProperties)、记忆功能还是 guard()2019 年 2 月 08 日下午 16:25[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1093908693299560448)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1093908693299560448)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1093908693299560448)4

这意味着在我们上面例子的上下文中，大致相当于:

```
computed: 'computeEverythingProperty(firstWord, secondWord)',

// ...

computeEverythingProperty(firstWord, secondWord) {
  return `${firstWord}  ${secondWord}`;
} 
```

我们将在我们的组件的能力和需求范围内，简单地把它变成如下的 getter，然后就到此为止。

```
get everythingProperty() {
  return `${this.firstWord}  ${this.second}`;
} 
```

然而，随着我们计算复杂性的增长，其他技术更有利的可能性也在增长。为了将这种计算从每一个`render()`转移到仅当原始属性改变时，你可以依赖于`updated()`生命周期方法，比如:

```
updated(changedProperties) {
  if(
    changedProperties.has('firstWord') ||
    changedProperties.has('lastWord')
  ) {
    this.everythingProperty = `${this.firstWord}  ${this.lastWord}`;
  }
} 
```

通过依赖`lit-html`提供的[保护指令](https://lit-html.polymer-project.org/guide/template-reference)，还可以选择将这个门控转移到模板中，这可能会在重用/可组合性方面增加更多的好处:

```
import {guard} from 'lit-html/directives/guard';

// ...

${
  guard(
    [this.firstName, this.lastName],
    () => html`${this.firstName}  ${this.lastName}`
  )
} 
```

你甚至可以超越这些技术，通过你自己的库代码或你认为合适的第三方工具来应用某种形式的[记忆](https://en.wikipedia.org/wiki/Memoization)。

## 观察者

LitElement 也省略了`observer`条目。与上面支持`computed`属性的代码非常相似， [`updated()`](https://lit-element.polymer-project.org/guide/lifecycle) 生命周期方法允许手动替换这个功能:

```
updated(changedProperties) {
  if (changedProperties.has('everythingProperty')) {
    // observe change
  }
} 
```

通过属性的自定义 getter/setter 可以实现类似的效果，在这里您可以扩展对这些新属性进入渲染管道的方式的控制:

```
get everythingProperty() {
  return this._everythingProperty;
}
set everythingProperty(value) {
  // observe this "change" every time the property is set
  if (value === this._everythingProperty) return;
  let oldValue = this._everythingProperty;
  this._everythingProperty = value;
  // observe change before the render.
  this.requestUpdate('everythingProperty', oldValue);
  // observe this change after requesting a render
  this.updateComplete.then(() => {
    // observe this change after the render completes
  });
} 
```

## 属性概述

《LitElement》中有很多关于`static get properties()`界面的内容和《Polymer 2》中的感觉是一样的，所以重要的是要注意那些*实际上*相同的东西和那些真正不同的东西。一般来说，当您可能已经习惯于依赖 Polymer 2 的自动功能被删除时，这些转换成本的好处是在手动实现这些功能时，可以更深入地集成到您的元素生命周期中，而不会被完全锁定到这些功能。当你发现扩展模式对增强你构建的应用程序类型很重要时，不要忘记这些东西可以打包成扩展基类，可以在你自己的项目中共享，在你工作的团队中共享，或者通过 [NPM](https://www.npmjs.com/) 、 [GitHub](https://github.com/) 或 [Polymer Slack](https://polymer.slack.com/join/shared_invite/enQtNTAzNzg3NjU4ODM4LTkzZGVlOGIxMmNiMjMzZDM1YzYyMzdiYTk0YjQyOWZhZTMwN2RlNjM5ZDFmZjMxZWRjMWViMDA1MjNiYWFhZWM) 在整个社区中共享，现在我们从 Polymer 2 的升级已经从我们的工具链中移除了 Bower 和 HTML 导入。

# 你的模板

早期基于 Polymer 2 的元素依赖于`<dom-module/>`方法将模板应用于元素:

```
<dom-module id="custom-element">
  <template>
    <style include="custom-element-styles"></style>
    <slot></slot>
    <h1>Hello [[prop1]]<h1>
    <input value="{{prop2::input}}" />
  </template>
  <script src="custom-element.js"></script>
</dom-module> 
```

这个`<dom-module id='custom-element'/>`的单个`<template/>`子元素与作用域数据绑定一起用于描述元素的影子 DOM。此外，我们看到`[[prop1]]`属性绑定到`<h1/>`的内容，而`{{prop2}}`属性基于`input`事件双向绑定到`input`元素的值。LitElement 不允许基于 HTML 的模板化(默认情况下),并且忽略了对双向绑定的支持，支持数据单向流动，所以当涉及到这个模板的内部工作时，有很多地方需要改变。

Polymer 2 的更高版本支持一个定制的`Polymer.html`模板标签，该标签已经将你的模板定位在这个升级路径的中间步骤。如果(像`generator-polymer-init-opinionated-element`一样)你已经使用了`Polymer.html`标签和相关的`static get template()`方法，上面的内容看起来会更像(或者可以转换成):

```
static get template() {
  const html = Polymer.html;
  return html`
      <style include="custom-element-styles"></style>
      <slot></slot>
      <h1>Hello [[prop1]]<h1>
      <input value="{{prop2::input}}" />
  `;
} 
```

这在移动到 LitElement 时需要更少的转换。还记得我们从`lit-element/lit-element.js`导入了一个`html`模板标签，所以直接的重构应该是这样的:

```
render() {
  return html`
      <slot></slot>
      <h1>Hello ${this.prop1}<h1>
      <input .value="${this.prop2}" @input="${this.handleInput}" />
  `;
}
handleInput(e) {
  this.prop2 = e.target.value;
} 
```

请记住，我们的样式现在是通过`static get styles`应用的，不再需要包含在模板中。请注意，`input`的值被绑定为一个属性(`.value="${this.prop2}"`)，这允许输入的可见值遵循由`prop2`保存的值。`@input`的事件绑定用定制元素代码中更显式的事件处理替换了以前由双花括号语法+事件名称(`{{prop2::input}}`)语法获得的双向绑定。

## 双向绑定

当处理从 Polymer 2.0 到 LitElement 的升级时，很容易感叹模板中双向绑定的丧失。双花括号语法(`child-property="{{twoWayBoundProperty}}"`)使得父元素很容易跟踪子元素的属性变化。这是通过 Polymer 从子元素调度一个自定义事件`child-property-changed`来响应属性值的变化，让父元素监听该事件，然后将子元素中的新值应用到父元素中的绑定属性。原则上，这种技术可以在您的 LitElements 中重复，方法是在父元素中配对以下绑定:

```
// parent-element.js

render() {
  return html`
      <child-element
        childProperty="${this.childProperty}"
        @child-property-changed="${this.handleChildPropertyChanged}"
      ></child-element>
  `;
}
handleChildPropertyChanged(e) {
  this.childProperty = e.detail.childProperty;
} 
```

对孩子进行这些更改:

```
// child-element.js

updated(changedProperties) {
  if (changedProperties.has('childProperty')) {
    this.dispatch(new CustomEvent('child-property-changed', {
      bubbles: true, // propagates beyond self
      composed: true, // propagates through shadow boundaries
      detail: {
        childProperty: value
      }
    });
} 
```

或者通过 getter/setter 对进行类似的更改:

```
// child-element.js

get childProperty() {
  return this._childProperty;
}
set childProperty(value) {
  if (value === this._childProperty) return;
  let oldValue = this._childProperty;
  this._childProperty = value;
  this.dispatch(new CustomEvent('child-property-changed', {
    detail: {
      childProperty: value
    }
  });
  this.requestUpdate('childProperty', oldValue);
} 
```

在用 LitElement 重构元素的第一步中，这可能是消除双向绑定的最佳选择。在未来，我强烈建议您确认应用程序中的这种数据流量既能实现您为用户设定的目标，又能定位您的元素以适应未来。将数据管理从组件中移走可能会使代码更易于维护和测试，所以我建议研究现有的无数状态管理技术来支持这样的决策。

## 常见模板实景

使用 Polymer 2，您的模板中很可能包含类似于`dom-repeat`(管理内容列表)和`dom-if`(管理条件内容)的东西。迁移到 LitElement 最令人兴奋的部分之一是用 JS 表达模板语言带来的灵活性。利用这种现实，您可以从模板中移除特定领域的语言现实。而不是像下面这样以聚合物为中心的数据列表:

```
<dom-repeat items="[[users]]" as="user">
  <h1>[[user.name]]</h1>
  <p>[[user.address]]</p>
</dom-repeat> 
```

你可以用更多以 JS 为中心的术语来概括这一点，比如:

```
${users.map(user => html`
  <h1>${user.name}</h1>
  <p>${user.address}</p>
} 
```

还有，你对内容的条件渲染，一拉:

```
<dom-if if="[[shouldShow]]>
  <p>Show an important thing.</p>
</dom-if> 
```

现在可以实现为:

```
${shouldShow
  ? html`<p>Show an important thing.</p>
  : html``
} 
```

从这里开始，在 JS 空间中管理您的模板为模板合成提供了新的选择。请注意下面是如何将分支逻辑的结果分解成它们自己的方法的，这让我们可以将我们的模板分成越来越小的部分，更容易孤立地进行推理:

```
render() {
  return html` ${loaded
      ? this.renderLoaded()
      : this.renderLoading()
    } `;
}
renderLoaded() {
  return html`Loaded.`;
}
renderLoading() {
  return html`Loading...';
} 
```

这个想法甚至可以走得更远(甚至可能走得太远？)如果您选择将整个控制流转移到 JS 中，利用某种策略模式，如下所示:

```
get templatesByState() {
  return {
    LOADING: this.renderLoading,
    LOADED: this.renderLoaded
  }
}
render() {
  return this.templateByState[this.loadedState]();
}
renderLoaded() {
  return html`Loaded.`;
}
renderLoading() {
  return html`Loading...';
} 
```

这些优化和更多的优化都是由`lit-html`提供的，它支持 LitElement 的模板解析和呈现过程。

## lit-html

除了这些直接转换，LitElement 的迁移意味着你的模板现在将由 [`lit-html`](https://lit-html.polymer-project.org/) 支持。作为一个高效的、富于表现力的、可扩展的 HTML JavaScript 模板库，`lit-html`在默认情况下提供了以前通过 Polymer Project 获得的工具所没有的性能，同时围绕[数据绑定](https://lit-html.polymer-project.org/guide/template-reference)、[数据类型支持](https://lit-html.polymer-project.org/guide/template-reference)和[控制流](https://lit-html.polymer-project.org/guide/template-reference)提供了大量扩展功能。所有这些甚至还没有开始进入它提供的[内置指令](https://lit-html.polymer-project.org/guide/template-reference)，以及它提供的[强大 API](https://lit-html.polymer-project.org/api/index.html) 来开发你自己的指令。将这些功能结合在一起，您可以比以往任何时候都更有目的地决定如何管理元素的渲染性能。正如聚合物团队所建议的[，如果你确实与“除了轻元素再出口之外的任何东西”一起工作，做好自己并运行`yarn add lit-html@1.0.0`以确保你已经获得了长期的依赖基础。](https://twitter.com/justinfagnani/status/1094304185099595776)

# 用 Web 组件测试器测试

在讨论我们上面的依赖性管理时，您会看到包含了:

```
"wct-istanbub": "^0.2.1",
"wct-mocha": "^1.0.0" 
```

这些更新使得在浏览器中运行 web component tester 比以前更加容易和友好，同时在 es 模块设置中提供了对测试覆盖报告的支持。通过这次更新，我们将在测试入口点中改变以下内容:

```
// test/index.html

<script src="../../webcomponentsjs/webcomponents-lite.js"></script>    <script src="../../web-component-tester/browser.js"></script> 
```

致:

```
<script src="../node_modules/mocha/mocha.js"></script>
<script src="../node_modules/wct-mocha/wct-mocha.js"></script> 
```

并且，为了在我们单独的测试文件中利用这些变化，我们将想要改变:

```
<script src="../../webcomponentsjs/webcomponents-lite.js"></script>
<script src="../../web-component-tester/browser.js"></script>
<script src="../../test-fixture/test-fixture-mocha.js"></script>
<link rel="import" href="../../polymer/polymer.html">

<link rel="import" href="../custom-element.html"> 
```

致:

```
<script src="../node_modules/@webcomponents/webcomponentsjs/webcomponents-loader.js"></script>

<script src="../node_modules/mocha/mocha.js"></script>
<script src="../node_modules/chai/chai.js"></script>
<script src="../node_modules/@polymer/test-fixture/test-fixture.js"></script>
<script src="../node_modules/wct-mocha/wct-mocha.js"></script>
<script src="../node_modules/sinon/pkg/sinon.js"></script>

<script type="module" src="../custom-element.js"></script> 
```

在这两个变化之间，您可以确保您的测试运行在最新的环境中，并且您在编写测试时所依赖的各种 API 在需要时可用。

## 可达性

我最喜欢使用 Web Components Tester 的一个好处是他们专注于可访问性测试。随着向 ES 模块的转移，通过`axe-core`工具进行可访问性测试的质量标准也更高了，这些工具遵循[宣言](https://www.npmjs.com/package/axe-core):

1.  自动化易访问性测试规则必须具有零误报率
2.  自动化可访问性测试规则必须是轻量级的和快速的
3.  自动化易访问性测试规则必须在所有现代浏览器中都能工作
4.  自动化易访问性测试规则本身必须被自动测试

为了利用这些工具，我们将更新我们各自的测试文件，包括:

```
<script>
  describe('custom-element', () => {
    let element;
    a11ySuite('custom-element-tester');

    beforeEach(function() {
      element = fixture('custom-element-tester');
    });

  // ...
  });
</script> 
```

致:

```
<script type="module">
  import '../node_modules/axe-core/axe.min.js';
  import {axeReport} from '../node_modules/pwa-helpers/axe-report.js';
describe('custom-element', () => {
    let should = chai.should();
    let element;
    beforeEach(async () => {
      element = fixture('custom-element-tester');
      await element.updateComplete;
    });
    it('a11y', () => {
      return axeReport(element);
    });
    // ...
  });
</script> 
```

为了确保我们的元素在每个测试开始前都被完全升级和渲染，你还会看到包含了:

```
beforeEach(async () => {
  element = fixture('custom-element-tester');
  await element.updateComplete;
}); 
```

LitElement 异步呈现，等待第一个`updateComplete`承诺的解决将为您省去很多麻烦。

# 结论

到目前为止，我们已经在将一个自定义元素从 Polymer 2 更新到 LitElement 方面取得了一些重大进展。在讨论一些哲学差异时，我们特别提到了:

*   从元素存储库的生命周期(CI、脚本、测试、构建等)中删除不推荐的流程。)
*   通过 NPM 而不是鲍尔获得依赖关系
*   通过 es 模块而不是 HTML 导入来应用这些依赖关系
*   更新样式应用程序以使用`css`模板标签和`adoptedStyleSheets` API
*   利用`static get properties()`的更新和扩展功能
*   管理 JS 中的模板并依靠`lit-html`的扩展功能
*   测试新的基于 ES 模块的元素

这有希望让你感觉有能力深入到将你的基于聚合物 2 的定制元素提升到 Lit 和 LitElement 基类的令人兴奋的可能性。前进吧，让定制元素更好、更快、更频繁！

* * *

但是，等等！当谈到您可能需要从 Polymer 2 生态系统中可用的旧方法更新的事情，以及在 LitElement 生态系统中工作时现在可能发生的事情时，这实际上只是冰山一角。当你更深入地进行这一升级(或者简单地用强大的 LitElement 基类制作定制元素)时，我希望你能在下面的评论中分享你的经历。无论你是否从上面概述的建议中得到帮助(或伤害，我总是在寻找一个好的编辑)，对这里没有完全涵盖的领域有疑问，或者发现了你认为社区的其他人可能会从你的 LitElement 工作中受益的东西，我都希望听到你的意见。只有通过公开和经常地分享我们的经验，我们才能在使用从 LitElement 基类创建的自定义元素时发现和了解所提供的可能性的全部深度。