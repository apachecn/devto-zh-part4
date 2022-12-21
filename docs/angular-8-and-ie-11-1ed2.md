# 角度 8 和 IE 11

> 原文：<https://dev.to/paulinhapenedo/angular-8-and-ie-11-1ed2>

有趣的是，我在 Dev.to 上的第一篇文章是关于 Angular 的，这是一个我几乎没有经验的框架。

在做了几年广告和营销工作后，我在 2010 年初转行了，所以我没有跟上 web 开发领域的突破性变化，比如 Backbone 和 Angular 框架的兴起。我的第一份网络工作是关于 WordPress 主题和心爱的 jQuery(我真的很怀念)。

无论如何，很抱歉绕了这么一小段路，说点真正重要的吧！在 Angular 的以前版本上，我们必须安装 [core-js](https://github.com/zloirock/core-js) 并导入一堆它的特性/助手，使你的`polyfill.ts`看起来像这样:

```
/** IE9, IE10 and IE11 requires all of the following polyfills. **/
import 'core-js/es6/symbol';
import 'core-js/es6/object';
import 'core-js/es6/function';
import 'core-js/es6/parse-int';
import 'core-js/es6/parse-float';
import 'core-js/es6/number';
import 'core-js/es6/math';
import 'core-js/es6/string';
import 'core-js/es6/date';
import 'core-js/es6/array';
import 'core-js/es6/regexp';
import 'core-js/es6/map'; 
```

Enter fullscreen mode Exit fullscreen mode

Angular 8 自带 [core-js](https://github.com/zloirock/core-js) 默认安装。如果我们告诉 Angular 这样做，它将被添加到我们的捆绑包 **only** 。

## 我们怎么做呢？

### 1。`tsConfig.json`

```
 // the default configuration
 "target": "es2015"

 // the change
 "target": "es5" 
```

Enter fullscreen mode Exit fullscreen mode

### 2。`polyfills.ts`

```
/** IE10 and IE11 requires the following for NgClass support on SVG elements */
 import 'classlist.js'; // Run `npm install --save classlist.js`. 
```

Enter fullscreen mode Exit fullscreen mode

### 3。`browserslist`

```
 # the default configuration
 not IE 9-11

 # the change - remove the "not"
 IE 9-11 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励提示

如果你打算使用 SVGs 精灵和标签`<use>`，IE 11 也会抱怨的。幸运的是有一个库可以解决这个问题，那就是令人惊奇的 [svg4everybody](https://github.com/jonathantneal/svg4everybody) 。把它加到你的`polyfills.ts` :

```
/** IE10 and IE11 requires the following for external source of SVG when using <use> tag */
import * as svg4everybody from 'svg4everybody';
svg4everybody({ polyfill: true }); 
```

Enter fullscreen mode Exit fullscreen mode

如果你对使用 Angular 8 的开发人员有任何其他的技巧和策略，请随时发表评论！