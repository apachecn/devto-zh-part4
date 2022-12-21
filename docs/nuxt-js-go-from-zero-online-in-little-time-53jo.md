# Nuxt.js:从零开始= >快速上线

> 原文：<https://dev.to/overscoremedia/nuxt-js-go-from-zero-online-in-little-time-53jo>

因此...你想做一个网站，是吗？

你知道如何使用 HTML 吗？CSS？JavaScript？

你还在等什么？获取编码！

* * *

## 没那么快，牛仔

好的，所以从技术上来说，你可以用这三样东西制作一个完整的网站，但是使用框架不是更容易吗？想想您可以节省的时间——想想您可以创建的所有组件——想想您可以导入的所有节点模块！

好吧，也许最好在模块上放松一下；它们累积的速度比你想象的要快。但是，你可能有一个观点——从长远来看,(正确地)使用一个框架可以节省你很多时间，并且使你的编码技能比其他方式更进一步。当然，你可以从头开始(这也不是一个坏主意)，但是这需要更长的时间，尤其是如果你刚开始，你很可能会重复你自己...

*不要重复自己，开发者！*这是第一条规则(或者至少是在上面)。

这么多 web 开发人员使用框架肯定是有原因的，对吗？当然，数百万的框架用户并没有完全疯掉。(当然，我们中的一些人确实如此。)

## 就这么定了，然后。

所以你想用框架，想用 JS 编码。

哦，好家伙！他们有[很多](https://www.javascripting.com/application-tools/frameworks/?sort=rating)！选哪个？

[苗条](https://svelte.dev/)？[基金会](https://foundation.zurb.com)？[暴动](https://riot.js.org)？[芒果树](https://monkberry.js.org)？[余烬](https://emberjs.com)？[反应](https://reactjs.org)？[流星](https://www.meteor.com)？[马尔科](https://markojs.com)？[骨干](https://backbonejs.org)？[棱角分明](https://angularjs.org)？[秘银](https://mithril.js.org)？

...
...
...

那么 [Vue](https://vuejs.org) 呢？你知道，三个最流行的框架的失败者？有好看的绿色 V 形标志的那个？主要由 Patreon/OpenCollective 捐助者支持的框架，不知何故能够与微软、脸书、谷歌等大型科技公司支持的框架竞争。？那个吗？

是啊，为什么不呢？

## 就是 Vue

不要让这篇文章改变你的想法。如果你下定决心要学习某种框架，那就去学吧！老实说，现在最大的框架都很优秀，有足够大的社区可以持续，并且背后有足够多的开发者惯性可以在未来几年得到支持和更新。

但是，如果你真的想学 Vue，让我给你介绍...

## 输入 Nuxt

Nuxt.js 可以说是启动并运行 Vue 应用的最快方式。这是一个框架上的框架，但是不要让它吓跑了你。它的社区出奇的活跃，而且一直都有新的更新。

Nuxt 帮了你大忙，因为它附带了一个方便的命令`nuxt generate`,这个命令实际上构建了你的。vue 文件转换成普通的 HTML、JS 和 CSS！

然后，这些文件可以托管在静态站点托管站点上，如 GitHub Pages、Netlify、Surge、Now、[等等！](https://nuxtjs.org/faq/github-pages/)

## 服气了没？

同样，这篇文章并不是要强迫你使用 Nuxt.js。但是，如果你想尽快深入了解它，这篇文章*将*帮助你。

首先，你需要在你的电脑上安装 [Node.js](https://nodejs.org/en/download/) 和 [Git](https://gist.github.com/derhuerst/1b15ff4652a867391f03) 。像 [VS Code](https://code.visualstudio.com) 这样的代码编辑器也会派上用场。

如果你用的是 Windows，像虫火谷这样的软件包管理器可以帮你安装 nodejs 和其他东西。

此外，我将使用脸书的 Node.js 包管理器 [Yarn](https://yarnpkg.com) ，但是你可以随意使用可靠的老 NPM。]

最后，确保您已经在系统上设置了 Git。类似这样的事情会起作用:

```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com 
```

Enter fullscreen mode Exit fullscreen mode

为引起人的注意时所发的声音...`$`是命令行光标的占位符。在这种情况下，这意味着您可以在没有 root 权限的情况下执行这些命令。

## 现在潜水中！

首先，导航到您想要开发的目录，打开一个终端(在 VS 代码中您可以按(`CTRL/CMD + SHIFT + P`)，键入“终端”，然后点击第一个结果，在您当前的目录中打开一个新的终端)，准备好迎接精彩吧！

`yarn create nuxt-app nuxt-zero-to-online`

召唤强大的 [create-nuxt-app](https://github.com/nuxt/create-nuxt-app) 快速启动一个 Nuxt.js 应用程序(nuxt-zero-to-online 是文件夹名，你可以随意更改，或者如果你已经在想要开发的文件夹中，你可以用句点(.).

Yarn 将开始下载一些 Node.js 模块(在本例中是依赖项)，这可能需要几分钟，取决于您的互联网连接。

很快，进度条就应该完成了，您应该会看到这样的命令提示:

```
? Project name (nuxt-zero-to-online) 
```

Enter fullscreen mode Exit fullscreen mode

现在，你*可以*点击“回车”,如果你喜欢的话，但是你也可以给你的项目起一个不同于它的父文件夹的名字，通过键入你选择的名字，然后按“回车”(但是，*专业提示:如果你的项目在相同名字的文件夹中，记住它们的名字会容易得多*

接下来，您将看到类似这样的内容:

```
? Project description (My polished Nuxt.js project) 
```

Enter fullscreen mode Exit fullscreen mode

这里也一样。接受或更改-按“回车”继续。

```
? Author name (Matthew Piercey) 
```

Enter fullscreen mode Exit fullscreen mode

你现在应该知道该怎么做了...

```
? Choose the package manager (Use arrow keys)
❯ Yarn
  Npm 
```

Enter fullscreen mode Exit fullscreen mode

你的偏好占上风，但纱线将是我的选择。用“ENTER”键选择您的选项。

```
? Choose UI framework (Use arrow keys)
❯ None
  Ant Design Vue
  Bootstrap Vue
  Buefy
  Bulma
  Element
  Framevuerk
  iView
  Tachyons
  Tailwind CSS
  Vuetify.js 
```

Enter fullscreen mode Exit fullscreen mode

哦，这有个有趣的！我现在就选择`None`,原因很简单，如果深入研究这个提示给出的每个选项，这篇文章会太长。稍后，我可能会为其中的每一个，或者至少是其中的几个，撰写单独的文章。这仅仅是 Nuxt 真正为您完成繁重工作的方式之一。

```
? Choose custom server framework (Use arrow keys)
❯ None (recommended)
  AdonisJs
  Express
  Fastify
  Feathers
  hapi
  Koa
  Micro 
```

Enter fullscreen mode Exit fullscreen mode

老实说，我曾经只使用过`None`，但是我知道如果你熟悉这些框架中的一个，选择一个会很有帮助。

然而，如果我没记错的话，没有一个能和 Nuxt 的生成器配合得最好，这就是我将要使用的

```
? Choose Nuxt.js modules (Press <space> to select, <a> to toggle all, <i> to invert selection)
❯ ◯ Axios
  ◯ Progressive Web App (PWA) Support 
```

Enter fullscreen mode Exit fullscreen mode

我觉得这两个都有用。如果你正在做任何异步请求，Axios 是很棒的，PWA 支持也很酷，Nuxt 使它非常容易设置。同样，具体细节可能会在以后的另一篇文章中讨论。

```
? Choose linting tools (Press <space> to select, <a> to toggle all, <i> to invert selection)
❯◯ ESLint
 ◯ Prettier
 ◯ Lint staged files 
```

Enter fullscreen mode Exit fullscreen mode

老实说，所有这些都很酷。我把它们都选中了。

```
? Choose test framework (Use arrow keys)
❯ None 
  Jest 
  AVA 
```

Enter fullscreen mode Exit fullscreen mode

Ava.js 看起来真的很酷。(说实话，我不知道怎么用...还没有。)

```
? Choose rendering mode (Use arrow keys)
❯ Universal (SSR) 
  Single Page App 
```

Enter fullscreen mode Exit fullscreen mode

对于生成的网站，我推荐使用 Universal (SSR)。

```
? Choose development tools (Press <space> to select, <a> to toggle all, <i> to invert selection)
❯◯ jsconfig.json (Recommended for VS Code) 
```

Enter fullscreen mode Exit fullscreen mode

拥有一个 jsconfig.json 可能是个好主意。

## 让纱做它的事

Yarn 现在将开始构建你的新 Nuxt 应用的文件，基于你刚才给它的配置。

这也可能需要几分钟或更长时间，但应该在合理的时间内完成。

```
🎉  Successfully created project nuxt-zero-to-online

  To get started:

        cd nuxt-zero-to-online
        yarn dev

  To build & start for production:

        cd nuxt-zero-to-online
        yarn build
        yarn start

  To test:

        cd nuxt-zero-to-online
        yarn test

✨  Done in 234.70s. 
```

Enter fullscreen mode Exit fullscreen mode

## Woot！呜！

如果你已经做到这一步，你应该会在你的 Nuxt 应用的文件夹中看到以下文件夹:

*   `assets`(未编译的代码资产和其他东西可以存放的地方)
*   `components`(自定义 Vue 组件的位置)
*   `layouts`(您站点的布局可以放在哪里)
*   `middleware`(你的 Nuxt 中间件功能可以去哪里)
*   `node_modules`(您的本地 Node.js 依赖项所在的位置)
*   `pages`(你可以在这里放置你的站点页面)
*   `plugins`(在这里您可以配置 Vue 插件来与 Nuxt 一起工作)
*   `static`(您可以在其中放置静态资产，如图像)
*   `store`(你的 Vuex 店可以去哪里)
*   【JavaScript 测试可以进行的地方)

注意，我在上面的列表中放了很多“可以”,因为 Nuxt 真的不太挑剔，只要你告诉它你在做什么。它的社区以是一个重视约定胜于配置的框架社区而自豪——虽然 Nuxt 给了您大量的选项，但它帮助您填充了几磅的选项，留给您的选项只有几“克”,您绝对需要考虑这些选项。

您还会在项目的根目录中看到如下文件:

*   `.babelrc`(管理 Babel JS 编译器选项)
*   `.editorconfig`(参见[Editorconfig.org](https://editorconfig.org/))
*   `.eslintrc.js`(管理 ESLint JS linter 选项)
*   `.gitignore`(Git 不应该添加到任何 Git repos 的文件列表)
*   `.prettierrc`(更漂亮的配置文件)
*   `ava.config.js`(ava . js 的配置文件)
*   `jsconfig.json`(一些对 VS 代码和其他编辑器有用的配置)
*   `nuxt.config.js` ( *包含大量 Nuxt.js 选项的大配置文件*)
*   `package.json`(大 Node.js 配置文件)
*   (给你一些有用的提示)
*   `yarn.lock`(纱线的锁夹；不要碰；它列出了 Node.js 依赖项的最新版本)

## 考试时间到了！

```
$ yarn dev 
```

Enter fullscreen mode Exit fullscreen mode

嘣！假设一切正常，你可以打开浏览器到`localhost:3000`，你应该会看到一个漂亮的带有几个按钮的 Nuxt 标志动画。

## 去吧！

现在可以使用`git add .`后跟`git commit -am "First commit with Nuxt.js"`来确保 Git 正在跟踪对项目文件的更改。(顺便说一下，提交消息可以是您想要的任何内容)。

现在，你可以把你的代码推送到你最喜欢的 Git 仓库，比如 GitHub、GitLab、BitBucket、GiTea、Gogs 或者任何你喜欢的地方。

这不是 Git 教程，所以本文不会涉及这一部分。但是，我将向您展示如何快速将您的站点上传到静态主机 Surge.sh！

*温馨提示:您可以通过按(`CTRL/CMD+C` )*
退出正在运行的 Nuxt 服务器

```
yarn generate 
```

Enter fullscreen mode Exit fullscreen mode

现在，关键时刻到了！使用上面的命令来召唤 Nuxt 强大的静态站点生成器(SSG)。你现在应该注意到在你的项目的根目录下有一个`dist/`文件夹，在一点看起来奇怪的混乱之后:

```
yarn run v1.17.3
$ nuxt generate
ℹ Production build                                                                                                                                                                                         22:47:57
✔ Builder initialized                                                                                                                                                                                      22:47:57
✔ Nuxt files generated                                                                                                                                                                                     22:47:57

✔ Client
  Compiled successfully in 5.78s

✔ Server
  Compiled successfully in 1.35s

Hash: c976a06b8539501cf9c6
Version: webpack 4.39.2
Time: 5779ms
Built at: 2019-08-26 22:48:05
                         Asset       Size  Chunks             Chunk Names
../server/client.manifest.json   7.57 KiB          [emitted]  
       0e80d025a1d59071f604.js   46.1 KiB       0  [emitted]  app
       1810f9b2a94a668fce75.js    164 KiB       1  [emitted]  commons.app
       909acf276b8f95d7b778.js   4.27 KiB       4  [emitted]  
                      LICENSES  510 bytes          [emitted]  
       a282e69f670b5d4ce1ee.js    3.6 KiB       2  [emitted]  pages/index
       aef96f98834c826077de.js   2.33 KiB       3  [emitted]  runtime
     icons/icon_120.46472c.png   3.05 KiB          [emitted]  
     icons/icon_144.46472c.png   3.78 KiB          [emitted]  
     icons/icon_152.46472c.png   3.96 KiB          [emitted]  
     icons/icon_192.46472c.png   5.01 KiB          [emitted]  
     icons/icon_384.46472c.png     11 KiB          [emitted]  
     icons/icon_512.46472c.png   13.9 KiB          [emitted]  
      icons/icon_64.46472c.png   1.61 KiB          [emitted]  
        manifest.7f1df9dc.json  789 bytes          [emitted]  
 + 2 hidden assets
Entrypoint app = aef96f98834c826077de.js 1810f9b2a94a668fce75.js 0e80d025a1d59071f604.js

Hash: cbd81db0590a2ba9060f
Version: webpack 4.39.2
Time: 1353ms
Built at: 2019-08-26 22:48:06
                  Asset       Size  Chunks             Chunk Names
3899b046024edc17e008.js   3.65 KiB       1  [emitted]  pages/index
              server.js     32 KiB       0  [emitted]  app
   server.manifest.json  243 bytes          [emitted]  
 + 2 hidden assets
Entrypoint app = server.js server.js.map
ℹ Generating pages                                                                                                                                                                                         22:48:06
✔ Generated /                                                                                                                                                                                              22:48:06
✨  Done in 11.90s. 
```

Enter fullscreen mode Exit fullscreen mode

你现在应该好好的继续下去！

(如果你是比较喜欢冒险的类型，你可以试着看看这个文件夹，但是如果它有点难以理解，不要感到惊讶...)

## 上线！

```
yarn global add surge 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将让 Yarn 在全球范围内安装[浪涌 CLI](https://github.com/sintaxi/surge)。同样，不要让这篇文章阻止你使用你最喜欢的静态主机，或者你自己的服务器。

```
surge dist/ 
```

Enter fullscreen mode Exit fullscreen mode

我们开始吧！上面的命令启动 surge.sh CLI，告诉它您的静态站点位于 dist/ folder:

```
 Welcome to surge! (surge.sh)
   Login (or create surge account) by entering email & password.

          email: 
```

Enter fullscreen mode Exit fullscreen mode

输入您想在 Surge.sh 上使用的电子邮件，创建一个密码或输入您的密码(如果您已经有了)，然后按`ENTER`选择您的`project:`和`domain:`。

```
 project: dist/
         domain: faded-able.surge.sh
         upload: [====================] 100% eta: 0.0s (20 files, 310371 bytes)
            CDN: [====================] 100%
             IP: 45.55.110.124

   Success! - Published to faded-able.surge.sh 
```

Enter fullscreen mode Exit fullscreen mode

哦耶！

[https://www.youtube.com/embed/HkQ7_oWqKpc](https://www.youtube.com/embed/HkQ7_oWqKpc)

## 结束？

听着，我已经知道你在想什么。“但是你甚至没有教我们如何使用它！你只说了一点 Nuxt，做了一点点设置！”

当然，你是对的。但是，通常一个软件/网站项目最困难的部分是知道从哪里/如何开始。如果你想进入 Nuxt.js，*这个*是一种开始的方式。

如果你准备好深入研究，请便。但是，如果你想从这个系列中得到更多，你不用等太久。在接下来的几周里，我会发布更多的内容，涵盖从 CSS 框架到 cookies 和分析的所有内容。TTYL？