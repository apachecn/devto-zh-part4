# 如何降低你的 Vue？带有 Webpack 的 JS 捆绑包大小

> 原文：<https://dev.to/ratracegrad/how-to-reduce-your-vue-js-bundle-size-with-webpack-4gpf>

我在 Stanley Black & Decker 的工业 4.0 团队工作。我们的团队最近为史丹利全球的制造工厂创建了一个相当于应用商店的平台。工厂可以访问市场，并根据他们在当地生产的产品选择他们需要的应用程序。这将创建一个定制的构建，将所有这些应用程序捆绑在一起，供工厂运行。由于捆绑了如此大量的应用程序，我们为生产而构建的 Vue 导致了多个关于过大的警告。

## 我们最初建造的规模

当我们进行构建时，我们得到以下两条错误消息:

[![](img/4bcb4021908a556b10abf905f514cc30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PZ-iu4b8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563665253/1_xbuSudEVU0kH9kJVRo757A_jox9gi.png)

Vue 建议包的大小不要超过 244 KiB。我们有 14 项资产，每项都超过这个规模。此外，我们有四个入口点也超过了建议的大小。以下是我将我们的构建规模减半所做的工作。

## 是什么导致了大量的构建包？

首先，我需要理解是什么导致了这么大的构建包。为此，我安装了 webpack-bundle-analyzer。这将为每个包裹中的物品尺寸提供直观的指导。

`npm install --save-dev webpack-bundle-analyzer`

接下来，我配置 webpack 在 vue.config.js 文件中使用它。下面是我的 vue.config.js 文件的样子:

```
const BundleAnalyzerPlugin = require('webpack-bundle-analyzer')
    .BundleAnalyzerPlugin;
    module.exports = {
        configureWebpack: {
            plugins: [new BundleAnalyzerPlugin()]
        }
   }; 
```

当我再次运行生产版本时，安装了插件，我可以看到我的版本是 2.48MB。从图像中，我可以清楚地看到最大的问题是:

*   视图-echart
*   使虚弱
*   瞬间
*   洛达什

[![](img/220ec588e9b014f817d7f5c8050b59ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bUB8j9ku--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563666363/1_5LbshDHOBRCDGtpjED6ACA_dfhfkn.png)

## 减少 Lodash 的大小

Lodash 占用了 70.74kb 的空间。在我们框架的所有应用程序中，Lodash 只在两个地方使用。这对于两个方法来说是很大的空间。

[![](img/84d11d9b30b8956520a1c9e749d06460.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AmGyG478--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563666468/1_-UglvHdNYShBloaef9ldmQ_ndmq9o.png)

我们不仅装载了洛达什，还装载了威洛达什。第一步是从我们的 package.json 中删除 vue-lodash，因为不需要它。

下一步是只从 lodash 导入我们需要的两个项目，而不是加载整个库。我们使用了`cloneDeep`和`sortBy`。我替换了最初导入整个 lodash 库的调用:

`import _ from 'lodash';`

我用这个调用来替换它，它只导入我们需要的两个项目。为此，我将导入从 lodash 更改为 lodash/core:

`import { cloneDeep, sortBy } from 'lodash/core';`

做了这一个更改后，我的构建包的大小从 2.48MB 减少到了 2.42MB。

[![](img/65d2bb34f3d408d858bd8eec58b3c973.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jiPb2ld1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563666632/1_6RwqQS1Iepyvraw1u2vfjw_q4ozka.png)

在这里，我们可以看到 lodash 本身的大小，这是我们构建包的一部分。

[![](img/a2dab789ce240377f45134408c6e105f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qBeO6jdf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563666755/1_4vDduy8g05Ns1BayqwQ8cw_xotb8e.png)

## 缩小 moment.js 的尺寸

Moment.js 在我们的包中占用了 234.36KB 的空间。当你看这个图片时，最大的一部分是他们支持的所有语言的国际化区域。我们根本不使用 moment.js 的这一部分，所以这是我们的包中包含的一个很大的自重。

幸运的是，我们可以移除它。而不是用这个调用导入所有的 moment . js:

`import moment form 'moment';`

我们可以只通过这个调用来导入日期操作代码:

`import moment from 'moment/src/moment'`

至少在我们的代码库中进行这种替换是有问题的。代码中导入 moment.js 的地方有 18 处。我可以在代码中进行全局搜索和替换。但是，如果我们向框架中添加新的应用程序，开发人员很可能会使用默认调用来导入 moment.js。如果他们这样做，那么我们将再次导入所有国际化语言环境。

所以折衷的办法是在 webpack 中创建一个快捷别名。快捷方式会用“moment/src/moment”替换所有导入“moment”的调用。我们可以使用 resolve 和设置别名在 vue.config.js 文件中添加该别名。下面是我的 vue.config.js 文件现在的样子。

[![](img/d2e3cbc68090e223fae895cb38c130e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VtWfxSFP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563666863/1_T-fNGuJQmvd9T-lC-9V9EA_km6bfc.png)

当我现在运行我们的生产版本时，我们的包的大小已经降到了 2.22MB。

[![](img/aaf6f51f2d8b8904a9c2dfc9db49fe52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aSMEV2Ub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563666928/1_jUu4bPp3xygmCUfgEayMIA_q4tsxs.png)

当您查看图像中的 moment.js 时，您会看到国际化语言环境根本不再被加载。

[![](img/0719d9b97098013be5812067b74c262c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QOSOop4d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563666975/1_xNtQoz71NJrHKhoFiZ0Cig_chmrlf.png)

通过删除 moment.js 中的语言环境，每当我启动服务器运行我的代码时，就会产生一个错误，说它找不到`./locale`。在做了一些研究后，我发现这是 moment.js 几年来的一个已知问题，因为 moment.js 总是加载并假设语言环境存在。您不能告诉 moment 只加载日期操作功能。

为了解决这个问题，我使用内置的 webpack IgnorePlugin 来忽略这个消息。下面是我添加到 vue.config.js 文件中的插件代码:

```
new webpack.IgnorePlugin(/^\\.\\/locale$/, /moment$/) 
```

## 缩小 Vuetify.js 的大小

下一个我想关注的是 Vuetify.js 的大小。Vuetify 占用了 500.78KB 的空间。对于一个供应商的产品来说，这是一个巨大的空间。

[![](img/6a9cbc6c4a89ea487d90481e9c98ee42.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jiwnDSGQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563667062/1_CBetC6KrkTFdbcOWigIuuw_gv7yk9.png)

Vuetify 提供了一个他们称之为点菜的功能。这允许您仅导入您使用的 Vuetify 组件。这将减小体积。面临的挑战是，我们有如此多的应用程序，要仔细检查并试图确定我们正在使用的组件是不可能的。

在 Vuetify 的当前版本中(在我写这篇文章时是 1.56 版本)，他们提供了一个名为 vuetify-loader 的产品。它将检查您的代码，确定您正在使用的所有组件，然后将它们导入到您的构建包中。**注意:**最终 vuetify v2 将内置这个特性。在该版本发布之前，您必须使用 vuetify-loader 来导入您正在使用的组件。Vuetify 文档指出，要获得所有需要的样式，我们需要在 stylus 中导入它们。

我意识到我们运行的是旧版本的 vuetify.js。所以我决定将我的 vuetify 版本升级到最新版本。我还安装了 styles 和 vuetify-loader，同时安装的还有:

`npm install vuetify vuetify-loader stylus stylus-loader style-loader css-loader --save`

我用来导入 Vuetify 的插件代码对主题进行了一些定制，使用了我们公司的调色板。这是我当前的 Vuetify 插件的样子:

[![](img/7f61b0bbca4a66cc08040bd5ac43dd5d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZgzLVtG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563667138/1_G8N2qYFxgswv0qPAQos6bw_bfz2ww.png)

我需要将 Vuetify 的导入更改为从 vuetify/lib 导入。我也将进口铁笔，以获得所有的风格。下面是我的插件代码现在的样子:

[![](img/4d790e1e124da0d621dd234e14c43b35.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qjIJjVAm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563667176/1_9FOCuvfxDNvDoA0Dt9b55Q_itht7c.png)

最后一步是告诉 webpack 使用 vuetify-loader 插件，这样它将只导入我们正在使用的组件。我将需要插件，然后将其添加到插件数组。下面是我的 vue.config.js 文件:

[![](img/b2d8942e07fd4d2b332fbccebebaa626.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wqvHu7Jn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563667229/1_FHEgk5Ooivlj40vf7GlLrQ_mqhyjl.png)

现在，当我为生产运行我的构建时，我的包大小是 2MB。

[![](img/3266b1b3e70e58d24ce5311b05a2e75f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GDZ5Vxcj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563667277/1_8DImlTLemFNm_3IVGqRtuw_rccxcu.png)

## 缩小 vue-echarts 的尺寸

Vue-echarts 并不是我包里最大的项目。Vue-echarts 运行在 echarts 之上。和 Vuetify 一样，我运行的是两个产品的旧版本。为了将它们都升级到最新版本，我运行这个命令:

`npm install echarts vue-echarts --save`

我对 vue-echarts GitHub repo 做了一些研究，查看了所有已关闭的问题，发现最新版本的 vue-echarts 允许您通过更改导入内容来加载较小的包。之前我是用这个命令导入的:

`import ECharts from 'vue-echarts';`

我把它改成这样:

`import ECharts from 'vue-echarts/components/ECharts.vue';`

现在，当我为生产运行构建时，我的包大小降到了 1.28MB。

[![](img/daec93b523b41d17b147c89e5b649721.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--txwu5kdp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ratracegrad/image/upload/v1563667391/1_dI1qMH7z5jKaTGMtGsvHSg_q0qhcf.png)

## 结论

我的目标是减少为应用程序生产而创建的包的大小。我的构建的初始大小是 2.48MB。通过做一些更改，我能够将我们的构建大小减少到 1.2MB。这几乎减少了 50%。

如果您正在创建生产 Vue 应用程序，您应该花时间评估您的构建规模。使用 webpack-bundle-analyzer 来确定哪些项目占用的空间最大。然后开始采取必要的步骤来减少这些项目的大小。通过这种方式，我可以减少包中最大的四个项目的大小。

希望您能够遵循这些步骤来减少生产构建的规模。如果您有任何问题或意见，请在下面发表。非常感谢您的阅读。

## 培训课程

我在我的网站 CodePrep 上创建培训课程。我有关于 Vue、Webpack、Flexbox、函数式编程等等的培训课程。点击这里查看。