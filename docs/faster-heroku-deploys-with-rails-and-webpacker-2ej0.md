# 更快的 Heroku 部署 Rails 和 webpacker

> 原文：<https://dev.to/voter101/faster-heroku-deploys-with-rails-and-webpacker-2ej0>

最初发布于:[https://moci un . XYZ/faster-heroku-deployments-with-rails-and-web packer/](https://mociun.xyz/faster-heroku-deploys-with-rails-and-webpacker/)

前段时间在[试播](https://pilot.co)中，我们转而使用 [webpacker](https://github.com/rails/webpacker) 作为编译 JavaScript 代码的工具。这是将 webpack 集成到 Rails 项目中的一个简单方法。

最近，我们注意到我们的 Heroku 部署变得令人沮丧的长。超过 10 分钟来构建和发布我们的主应用程序是不可接受的。将 CI 运行时和 Heroku 的[预引导](https://devcenter.heroku.com/articles/preboot#deploying-with-preboot)添加到组合中，现在我们可以轻松地完成大约 25-30 分钟的完整部署时间。当试图发布一个补丁时，这变得非常烦人。

如果我们能把它变短呢？

## 到底怎么回事？

构建过程中最大的一块时间是资产编译。对于每个版本，Heroku 都在安装所有的 npm 依赖项(使用 yarn ),并从头开始编译 webpacker 资产。每次构建都花费了很长时间，即使我们没有接触任何代码(两次部署相同的代码)。

在本地开发环境中，如果没有变化，这个过程会在不到一秒钟的时间内运行。

随着时间的推移，我们还注意到，我们的段塞尺寸越来越大，这导致部署更长。在每次 webpacker 构建之后，都有许多未清理的残余。简单的[回购清除](https://github.com/heroku/heroku-repo)足以解决这个问题，但是它需要手动执行命令。
 *‌显然有几个问题:*

 **   纱线缓存不能重复使用。
*   即使 JavaScript 代码没有被触及，完整的 webpacker 构建也会运行。
*   旧的构建产品没有被清理。

## 找到解决办法

幸运的是，有些人已经在研究解决方案了。这样，如果 JavaScript 文件没有被修改，webpacker 将重用以前的版本。否则，webpacker 将进行完全重建。最重要的是，这些变化包括一个 Yarn 缓存，它解决了依赖项安装的问题(不过这可以通过安装 Heroku 的官方 node.js buildpack 来实现)。

另一个问题是 webpacker [没有附带清理任务](https://github.com/rails/webpacker/issues/1410)(类似于`assets:clean`)。如果没有它，我们的应用程序的段大小将会不断增长。幸运的是，有一个名为 [clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin) 的 webpack 插件可以做到这一点。

结合那个插件，通过 [@kpheasey](https://github.com/kpheasey) 在[解决方案](https://github.com/heroku/heroku-buildpack-ruby/pull/892)中做一点小调整，我们就能达到我们想要的效果。我们在 GitHub 分支 [`heroku-buildpack-ruby`](https://github.com/pilotcreative/heroku-buildpack-ruby/tree/feat/yarn-cache) 中加入了这个调整。

## 将溶液放在一起

> #### 技术说明:
> 
> 该解决方案适用于版本> = 5.1、< 6.0 的 Rails。不过，应该很容易对其进行修改，以便与其他版本一起工作。:)

首先，你需要准备好`clean-webpack-plugin`:

```
yarn add clean-webpack-plugin 
```

Enter fullscreen mode Exit fullscreen mode

现在将插件的配置代码添加到`config/webpack/environment.js` :

```
// ...
const { CleanWebpackPlugin } = require("clean-webpack-plugin");

environment.plugins.prepend("CleanWebpackPlugin", new CleanWebpackPlugin());

// ... 
```

Enter fullscreen mode Exit fullscreen mode

最后一步—*在 Heroku 上你的应用程序的*设置*中用定制的 Ruby buildpack 替换官方的*。

```
https://github.com/pilotcreative/heroku-buildpack-ruby.git#feat/yarn-cache 
```

Enter fullscreen mode Exit fullscreen mode

[![Heroku buildpacks of one of our apps](img/046edb27f131ddae06a4fb01280b4958.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gi8c4nA2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xpcrvbmdkec2jz0od5d8.png)

现在运行一个完整的构建来构建您的缓存，您就大功告成了！对于我们来说，构建需要 2.5 到 5 分钟，这取决于要部署什么样的更改。

## 来源

*   [https://github.com/rails/webpacker/issues/1410](https://github.com/rails/webpacker/issues/1410)
*   [https://github.com/heroku/heroku-buildpack-ruby/pull/892](https://github.com/heroku/heroku-buildpack-ruby/pull/892)
*   [https://github.com/johnagan/clean-webpack-plugin](https://github.com/johnagan/clean-webpack-plugin)
*   [https://github.com/rails/webpacker/pull/1744](https://github.com/rails/webpacker/pull/1744)
*   [https://thoughtbot . com/blog/how-to-reduce-a-large-heroku-compiled-slug-size](https://thoughtbot.com/blog/how-to-reduce-a-large-heroku-compiled-slug-size)*