# 咕噜咕噜，然后大口大口，然后用网袋装好包裹🤖🔥🤔

> 原文：<https://dev.to/adam_cyclones/grunt-then-gulp-then-webpack-now-parcel-2j7b>

免责声明:这篇文章不是对工具的攻击，也不是对任何东西的攻击。

好吧，让我们做另一个慷慨激昂的帖子，我这个星期似乎很生气。🙄😠(没有人喜欢抱怨的开发者，但下周是我的生日，我已经老了)

打开 codesandbox 并点击 vanilla，我只想写一些 Vanilla 松散，快速和肮脏的老式不复杂，不透明通过量子分子解调超导树形图。我觉得这叫简单。

迎接我的是一个包裹配置。"你好，包裹，这是什么文件？"我用人类已知的最有效的措辞问自己。但在这之前，让我提醒你，我点击了香草！为什么 JavaScript 变成了编译型的第一语言和解释型的第二语言，我们写的不是我们发布的，这不会困扰你吗？

我花了一段时间来证明运行一个前端的 10 个左右的配置文件，“这很正常”，每个人都在做，这只是前端生活的一部分，这不会困扰你吗？

我们这么忙着往前走，有没有人回头看看老朋友？大口 4，这不是我记忆中的大口但是很好听。

好吧，任务运行者不是捆绑者(事物的编译器)，所以这是个坏例子，但我的观点仍然成立。为什么 JavaScript(浏览器供应商)跟不上需求，为什么我们必须在我们写的代码和损坏的更大的代码之间有如此多的混乱，以至于没有人真正知道里面是什么。

最近，我发现学习 C++和 emscripten 的工作量和构建“完美的 Webpack”的工作量差不多，这很疯狂，也很耗时。

难道你不希望只写带有可选静态类型的 JavaScript 吗？也许只是有一个 JSC (JavaScript 编译器)的官方规范，更少的新工具和更多的开发？

是的，我问了很多问题，关于这篇文章的观点和结论，包裹，听起来像 Fusebox，0 config bundler，是的，这不是真正的新东西，捆绑东西就像动态链接一样，dll，an。exe，这也是一个包，从概念上讲，这都是旧帽子的东西。Makefiles，OP bundler，还有很多。这些工具都没有编译回脚本语言本身，今天的 JavaScript 有什么严重的问题吗？🧐

(他说，所有这些之后都回到了现代堆栈的编程工作中，没有任何进一步的思考)

更新:(他接着写了滴滴)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [亚当-万马](https://github.com/adam-cyclones) / [迪迪](https://github.com/adam-cyclones/didi)

### 将项目从普通 JS 转换为 ESmodules，包括 bundler-like / task runner 行为。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![didi the dino is a pterodactyl logo](img/2c05d39f2b41b857eec070ccb81e5294.png)](https://github.com/adam-cyclones/didi)

### 姐姐

JavaScript 和 Typescript 的 transpiler，将 CommonJS 模块转换成不同的 es 模块

## 谁用滴滴？

前端和 deno 开发者会发现 didi 很有用。

## 试试看。

**滴滴还没有准备好投入生产**但是你仍然可以带滴滴去兜风！

```
npm install -g @didi-js/client-didi-cli

# or

yarn global add @didi-js/client-didi-cli
```

Enter fullscreen mode Exit fullscreen mode

创建一个项目、一个入口文件、一些已安装的 node_modules，然后使用基本说明符将浏览器依赖项添加到入口文件中。

```
const colorThief = require('colorThief'); // base specifier example, no paths needed
```

Enter fullscreen mode Exit fullscreen mode

运行`didi path/to/example-project`

结果应该在这个示例项目中输出一个新的目标目录，并且服务器应该在`http://localhost:8086`启动。您可能会在浏览器中看到一些控制台错误，这在此阶段是正常的。

您可能还会注意到您的导入如下所示:

```
import colorThief from "color-thief"; // still no path?
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/adam-cyclones/didi)