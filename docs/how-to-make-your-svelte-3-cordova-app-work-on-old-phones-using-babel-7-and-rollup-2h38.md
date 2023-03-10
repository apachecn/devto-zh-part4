# 如何使用 Babel 7 和 Rollup 让你的苗条 3 科尔多瓦应用程序在旧手机上工作

> 原文：<https://dev.to/johnnymakestuff/how-to-make-your-svelte-3-cordova-app-work-on-old-phones-using-babel-7-and-rollup-2h38>

当使用像 svelte 这样相对较新的框架时，你会遇到更多“经过战斗考验”的框架已经解决的问题。一个这样的问题是将你的代码移植到旧的浏览器上。这对 cordova 应用程序尤其有意义，因为它运行在系统 WebView 中，版本取决于各种因素，并不总是更新。

> 我在文章中写了更多关于 WebView 版本的内容:[你应该转存你的 cordova 应用程序吗](https://www.learningsomethingnew.com/should-you-transpile-your-cordova-app)

## 应该传递什么？

当然，我们要用巴别塔，来传送。

来自巴别塔[的文件](https://babeljs.io/docs/en/):

> Babel 是一个工具链，主要用于将 ECMAScript 2015+代码转换为当前和旧版本浏览器或环境下的向后兼容版本的 JavaScript。

但是需要传输哪个代码呢？我们需要考虑两个层次的蒸腾作用:

### 1。我们应用的代码

如果你在你的应用程序中使用 ES6 或更高版本(你应该这样做)，你需要转换它。这包括您的`.svelte`文件和`.js`文件。

### 2。属国

你的依赖也是与你的应用捆绑在一起的代码。如果它们是用 ES6 语法发布的，那么它们将导致您的`bundle.js`包含未被移植的代码。
对了，这里是[巴别塔的对外依赖](https://github.com/rollup/rollup-plugin-babel#external-dependencies):

> 理想情况下，你应该只转换你的源代码，而不是通过 Babel 运行你所有的外部依赖——因此排除:' node_modules/**'
> 我们鼓励库作者不要分发使用未被移植的 ES6 特性(而不是模块)的代码。你的库的用户不应该传输你的 ES6 代码，就像他们不应该传输你的 CoffeeScript、ClojureScript 或 TypeScript 一样。

不幸的是，并不是所有的图书馆都遵守这个规则。一个这样的库是 [axios](https://github.com/axios/axios/blob/master/UPGRADE_GUIDE.md#es6-promise-polyfill) :

> 直到 0.6.0 版本，ES6 Promise 都是使用 es6-promise 进行多项填充的。在此版本中，polyfill 已被删除，如果您的环境需要，您将需要自己提供它。

另一个是苗条的。根据这个[svelet GitHub 问题](https://github.com/sveltejs/svelte/issues/558)，准确列出需要传输/聚合填充的内容仍然是一个 WIP。

## 解

下面的配置对我有效，成功地传输了我的 cordova 应用程序。

### 1。安装依赖项

首先，让我们安装所需的巴别塔包，以及汇总插件。

```
npm i -D @babel/core @babel/preset-env rollup-plugin-babel core-js 
```

### 2。配置巴别塔

现在将以下配置添加到`babel.config.js` :

```
module.exports = function (api) {
    api.cache(true);
    const presets = [
        ["@babel/preset-env", {
            // "debug": true,
            useBuiltIns: "usage",
            corejs: 3, // or 2,
            targets: {
                browsers: "last 2 versions",
            }
        }]
    ];
    return {
        presets
    }
} 
```

注意`browsers: "last 2 versions"`行。这是`@babel/preset-env`的一个特性，使得它可以移植到所有浏览器的最后两个主要版本。
此外，如果你想查看正在传输的文件，那么`debug`字段也很有用。

### 3。配置汇总

在`rollup.config.js`的插件部分的末尾添加下面的巴别塔部分*，假设你使用的目录结构与 [svelte cordova 模板](https://github.com/syonip/svelte-cordova-template)中的目录结构相同:* 

```
import svelte from 'rollup-plugin-svelte';
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import livereload from 'rollup-plugin-livereload';
import replace from 'rollup-plugin-replace';
import babel from 'rollup-plugin-babel';

export default {
    ...
    plugins: [
        ...
        production && babel({
            exclude: [
                // 'node_modules/**',
                /\/core-js\//,
            ],
            extensions: ['.svelte', '.js', '.jsx', '.es6', '.es', '.mjs', '.ts']
        }),
    ]
}; 
```

这里需要注意几件事:

*   行`production && babel`是 js 的一个技巧，让 babel 插件不能在 dev 模式下执行。你可以随意打开或关闭它，如果你正在测试 transpiled 代码是如何工作的，让它在 dev 模式下运行可能会很方便。
*   `exclude`段的**不**包含`node_modules`。我们正在传输我们的依赖项，因为其中一些是未传输的。如果对你来说不是这样，你可以忽略它们，但是你仍然需要 transpile 模块本身。
*   `extensions`部分必须包含`.svelte`扩展名，否则你自己的应用程序代码不会通过巴别塔。
*   我们**正在**将`core-js`排除在传输之外，因为[关于 rollup-plugin-babel](https://github.com/rollup/rollup-plugin-babel/issues/254) 的循环引用问题

## 学分

我使用 simey.me 的这个[帖子作为这里描述的配置的基础。](http://simey.me/svelte3-rollup-and-babel7/)