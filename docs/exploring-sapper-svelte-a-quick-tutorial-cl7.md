# 探索 Sapper + Svelte:快速教程

> 原文：<https://dev.to/bnevilleoneill/exploring-sapper-svelte-a-quick-tutorial-cl7>

[![](img/f261062b73bcf5c8743b794e2c6ba249.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5XIurMOc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e4gf5enbcs1xbqoakade.jpg)

## 简介

不久前，[我们探索了 Svelte.js](http://We'll%20be%20taking%20a%20high-level%20look%20at%20Sapper,%20what%20it%20is,%20how%20it%20helps%20you%20build%20full-fledged,%20lightweight%20apps,%20and%20why%20you%20should%20consider%20using%20it.%20Note:%20This%20post%20is%20a%20followup%20to%20the%20intro%20to%20Svelte%20article%20published%20a%20while%20back.%20I%20strongly%20urge%20you%20to%20read%20it%20here:%20https://blog.logrocket.com/truly-reactive-programming-with-svelte-3-0-321b49b75969/.) ,看到了它如何帮助你编写真正的反应式应用程序，同时比许多其他前端框架交付更少的代码。虽然你可以单独用 Svelte 构建一个更复杂的应用程序，但它可能会很快变得一团糟。进入工兵！

在本文中，我们将从较高的层面来看一下 Sapper，它如何帮助您构建成熟的轻量级应用程序，以及分解服务器渲染的应用程序。 [![😁](img/c10695d626b135aa2c96e9699ffc9a07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z9XGTBmA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f601.png) 到本文结束时，你应该对 Sapper 有足够的了解，明白是什么让它令人敬畏。

尽管如此，通读一下[文档](http://sapper.svelte.dev/docs#Introduction(opens%20in%20a%20new%20tab))仍然是一个好主意，因为那里有一些我遗漏了的概念。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 什么是工兵连？

Sapper 是 Svelte 的配套组件框架，可以帮助你快速高效地构建更大更复杂的应用。

在当今时代，构建一个 web 应用程序是一项相当复杂的工作，包括代码拆分、数据管理、性能优化等等。这也是为什么现在有无数的前端工具，但是它们都有自己的复杂程度和学习曲线。

建一个 app 应该没那么难吧？还能比现在更简单吗？有没有一种方法可以在保持理智的情况下勾选所有的选项？当然有——那是一个反问句！

先说名字:工兵。我将直接引用官方文件来解释为什么选择这个名字:

> 在战争中，那些在战斗条件下建造桥梁、修复道路、清理雷区和实施爆破的士兵被称为工兵。
> 
> 对于网络开发人员来说，风险通常低于战斗工程师。但我们面临着自己的不利环境:功能不足的设备、糟糕的网络连接以及前端工程固有的复杂性。Sapper 是**苗条应用程序制造商**的缩写，是你勇敢而忠实的盟友。

嗯，很有道理。[![🤓](img/f1429a510cde04140f7054ca24e56ae5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QSre4hF9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f913.png)

Sapper(或者说，Svelte)的设计是轻量级的、高性能的、易于推理的，同时还提供了足够的功能来将你的想法转化为出色的网络应用。

基本上，以下是 Sapper 在以苗条的方式构建 web 应用程序时帮助你处理的事情:

*   **路由**
*   **服务器端渲染**
*   **自动代码拆分**
*   **离线支持(使用服务人员)**
*   **高层项目结构管理**

我相信你会同意，自己管理这些会很快变成一件杂务，让你从实际的业务逻辑中分心。

但是空谈是廉价的——代码是有说服力的！让我们通过一个使用 Svelte + Sapper 的小型服务器渲染的应用程序。

## 亲身体验

我不会告诉你 Sapper 如何帮助你轻松构建应用程序，我们将探索当你搭建一个新项目时得到的演示应用程序，并看看它是如何在幕后工作的。

首先，运行以下命令来引导一个新项目:

```
$ npx degit "sveltejs/sapper-template#rollup" my-app
$ cd my-app
$ npm install
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

这样做将得到一个基本的项目，但是对于本文的目的来说已经足够了。我们应该能够探索 Sapper 如何用这个简单的项目处理路由和服务器端渲染，而不必太深入。

让我们开始吧！

## 项目结构

Sapper 是一个固执己见的框架，意味着某些文件和文件夹是必需的，项目目录必须以某种方式构建。让我们看看一个典型的 Sapper 项目是什么样子的，所有的东西都放在哪里。

### 进入点

每个 Sapper 项目都有三个入口点和一个`src/template.html`文件:

1.  `src/client.js`
2.  `src/server.js`
3.  `src/service-worker.js`(这个是可选的)

#### `client.js`

```
import * as sapper from '@sapper/app';

sapper.start({
  target: document.querySelector('#sapper')
}); 
```

Enter fullscreen mode Exit fullscreen mode

这是客户端渲染应用程序的入口点。这是一个相当简单的文件，您在这里需要做的就是从`@sapper/app`导入主 Sapper 模块，并从中调用`start`方法。它接受一个对象作为参数，唯一需要的键是`target`。

目标指定应用程序将被挂载到哪个 DOM 节点上。如果你来自 React.js 背景，就把这个当做`ReactDOM.render`。

#### `server.js`

我们需要一个服务器来为用户提供我们的应用程序，不是吗？因为这是一个 Node.js 环境，所以有大量选项可供选择。你可以使用 Express.js 服务器、Koa.js 服务器、Polka 服务器等等。，但是有一些规则要遵循:

1.  服务器必须提供`/static`文件夹的内容。Sapper 不在乎你怎么做。把那个文件夹端上来！
2.  你的服务器框架必须支持中间件(我个人不知道有哪个不支持)，而且必须使用从`@sapper/server`导入的`sapper.middleware()`。
3.  您的服务器必须监听`process.env.PORT`。

只有三条规则——如果你问我，我觉得还不错。看一看为我们生成的`src/server.js`文件，看看它实际上是什么样子。

#### `service-worker.js`

如果你需要重温一下什么是服务人员，[这篇文章应该很适合](https://developers.google.com/web/fundamentals/primers/service-workers/)。现在，你用 Sapper 构建一个全功能的 web app 不需要`service-worker.js`文件；它只允许您访问离线支持、推送通知、后台同步等功能。

由于服务人员习惯于应用程序，因此没有关于如何编写应用程序的硬性规定。您可以选择完全忽略它，或者利用它来提供更完整的用户体验。

#### `template.html`

这是应用程序的主要入口点，所有的组件、样式引用和脚本都是在这里根据需要注入的。除了极少数情况下需要通过从 HTML 链接到 CDN 来添加模块，这几乎是一劳永逸的。

#### `routes`

每个工蜂 app 的 MVP。这是你大部分逻辑和内容的所在。我们将在下一节中进行更深入的探讨。

## 路由

如果你运行了**动手体验**部分的所有命令，导航到`http://localhost:3000`应该会带你到一个简单的网络应用，有主页、关于页面和博客页面。到目前为止，很简单。

现在让我们试着理解 Sapper 是如何将 URL 与相应的文件相协调的。在 Sapper 中，有两种类型的路由:页面路由和服务器路由。

让我们进一步分解一下。

### 页面路线

当你导航到一个页面——比如说，`/about` — Sapper 会呈现一个位于`src/routes`文件夹中的`about.svelte`文件。这意味着该文件夹中的任何`.svelte`文件都会自动“映射”到同名的路径。因此，如果在`src/routes`文件夹中有一个名为`jumping.svelte`的文件，导航到`/jumping`将导致该文件被渲染。

简而言之，页面路由就是`src/routes`文件夹下的`.svelte`文件。这种方法的一个非常好的副作用是你的路线是可预测的，并且很容易推理。你想要一条新路线？在`src/routes`中创建一个新的`.svelte`文件，你就成功了！

如果您想要一个类似于下面这样的嵌套路由怎么办:`/projects/sapper/awesome`？您需要做的就是为每个子路由创建一个文件夹。因此，对于上面的例子，你将有一个这样的文件夹结构:`src/routes/projects/sapper`，然后你可以在`/sapper`文件夹中放置一个`awesome.svelte`文件。

记住这一点，让我们看看我们的引导应用程序，并导航到“关于”页面。你认为这个页面的内容是从哪里呈现的？好吧，我们来看看`src/routes`。果然，我们在那里找到了一个`about.svelte`文件——简单且可预测！

请注意，`index.svelte`文件是一个保留文件，当您导航到一个子路由时会呈现出来。例如，在我们的例子中，我们有一个`/blogs`路由，在那里我们可以访问它下面的其他子路由，例如`/blogs/why-the-name`。

但是请注意，当`/blogs`本身是一个文件夹时，在浏览器中导航到`/blogs`会呈现一个文件。如何为这样的路线选择要渲染的文件？

要么我们在`/blogs`文件夹外定义一个`blog.svelte`文件，要么我们需要一个`index.svelte`文件放在`/blogs`文件夹下，但不能同时定义两个文件。当你直接访问`/blogs`时，这个`index.svelte`文件被渲染。

有动态 slugs 的 URL 怎么办？在我们的例子中，手动创建每一篇博客文章并将它们存储为`.svelte`文件是不可行的。我们需要的是一个模板，用于渲染所有的博客帖子，而不管蛞蝓。

再看一下我们的项目。在`src/routes/blogs`下面，有一个`[slug].svelte`文件。你认为那是什么？是的——它是渲染所有博客文章的模板，而不考虑 slug。这意味着任何跟在`/blogs`后面的 slug 都会被这个文件自动处理，我们可以在 page mount 上获取页面内容，然后将它呈现给浏览器。

这是否意味着`/routes`下的任何文件或文件夹都会自动映射到一个 URL？是的，但是这条规则有一个例外。如果您在文件或文件夹前添加下划线，Sapper 不会将其转换为 URL。这使得您可以轻松地在 routes 文件夹中放置助手文件。

假设我们需要一个助手文件夹来存放我们所有的助手函数。我们可以有一个类似于`/routes/_helpers`的文件夹，然后任何放在`/_helpers`下的文件都不会被当作一个路径。很漂亮，对吧？

### 服务器路由

在上一节中，我们看到有一个`[slug].svelte`文件可以帮助我们匹配像这样的任何 URL:`/blogs/<any_url>`。但是它实际上是如何让页面的内容呈现出来的呢？

您可以从静态文件中获取内容，或者通过 API 调用来检索数据。无论哪种方式，您都需要向路由(或者端点，如果您只考虑 API)发出请求来检索数据。这就是服务器路由的用武之地。

来自官方文档:“服务器路由是写在`.js`文件中的模块，这些模块导出对应于 HTTP 方法的函数。”

这只是意味着服务器路由是端点，您可以调用它们来执行特定的操作，比如保存数据、获取数据、删除数据等。它基本上是你的应用程序的后端，所以你可以在一个项目中拥有你需要的一切(当然，如果你愿意，你可以把它分开)。

现在回到我们的自举项目。如何获取`[slug].svelte`中每篇博文的内容？嗯，打开文件，你看到的第一段代码是这样的:

```
<script context="module">
  export async function preload({ params, query }) {
    // the `slug` parameter is available because
    // this file is called [slug].html
    const res = await this.fetch(`blog/${params.slug}.json`);
    const data = await res.json();
    if (res.status === 200) {
      return { post: data };
    } else {
      this.error(res.status, data.message);
    }
  }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我们所看到的只是一个简单的 JS 函数，它发出一个 GET 请求并从该请求中返回数据。它接受一个对象作为参数，然后在第 2 行将其析构以获得两个变量:`params`和`query`。

`params`和`query`包含什么？为什么不在函数的开头加一个`console.log()`，然后在浏览器中打开一篇博文呢？这样做，您会在控制台上记录类似这样的内容:

```
{slug: "why-the-name"}slug: "why-the-name"\_\_proto\_\_: Object {} 
```

Enter fullscreen mode Exit fullscreen mode

嗯。因此，如果我们打开第 5 行的“why-the-name”帖子，我们的 GET 请求将是对`blog/why-the-name.json`的请求，然后在第 6 行将其转换为 JSON 对象。

在第 7 行，我们检查我们的请求是否成功，如果成功，就在第 8 行返回它，否则调用一个名为`this.error`的[特殊方法](https://sapper.svelte.dev/docs#this_error)，并给出响应状态和错误消息。

很简单。但是实际的服务器路由在哪里，它看起来像什么？查看`src/routes/blogs`内部，您应该会看到一个`[slug].json.js`文件——这是我们的服务器路由。注意到它的名字和`[slug].svelte`一样吗？这就是 Sapper 如何将服务器路由映射到页面路由。因此，如果您在名为`example.svelte`的文件中调用`this.fetch`，Sapper 将寻找一个`example.json.js`文件来处理请求。

现在我们来解码[slug].json.js，好吗？

```
import posts from './_posts.js';

const lookup = new Map();
posts.forEach(post => {
  lookup.set(post.slug, JSON.stringify(post));
});

export function get(req, res, next) {
  // the `slug` parameter is available because
  // this file is called [slug].json.js
  const { slug } = req.params;
  if (lookup.has(slug)) {
    res.writeHead(200, {
      'Content-Type': 'application/json'
    });
    res.end(lookup.get(slug));
  } else {
    res.writeHead(404, {
      'Content-Type': 'application/json'
    });
    res.end(JSON.stringify({
      message: `Not found`
    }));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们真正感兴趣的是从第 8 行开始。第 3–6 行只是为要使用的路线准备数据。还记得我们如何在页面路由中发出 GET 请求:`[slug].svelte`？这是处理该请求的服务器路由。

如果您熟悉 Express.js APIs，那么您应该对此很熟悉。这是因为这只是一个简单的端点控制器。它所做的就是获取从`Request`对象传递给它的 slug，在我们的数据存储中搜索它(在本例中是`lookup`，并在`Response`对象中返回它。

如果我们使用数据库，第 12 行可能看起来像`Posts.find({ where: { slug } })`(有人知道吗？).你明白了。

服务器路由是包含端点的文件，我们可以从页面路由中调用这些端点。因此，让我们快速总结一下目前为止我们所知道的:

*   页面路由是`src/routes`文件夹下的`.svelte`文件，用于向浏览器呈现内容。
*   服务器路由是包含 API 端点的`.js`文件，通过名称映射到特定的页面路由。
*   页面路由可以调用服务器路由中定义的端点来执行特定的操作，如获取数据。
*   萨帕尔是相当深思熟虑的。

## 服务器端渲染

服务器端渲染(SSR)是 Sapper 如此吸引人的一个重要原因。如果你不知道什么是 SSR，也不知道你为什么需要它，这篇文章很好地解释了这个问题。

默认情况下，在客户端挂载动态元素之前，Sapper 会先在服务器端渲染所有的应用程序。这让你可以两全其美，而不必做出任何妥协。

不过，这里有一个警告:虽然 Sapper 在支持第三方模块方面做得近乎完美，但是有些模块需要访问`window`对象，正如您所知，您不能从服务器端访问`window`。简单地导入这样一个模块会导致你的编译失败，世界会变得有点暗淡。【T2![🥺](img/f224f710cd5ac1f9ebbda027e92b09fa.png)

不过，不要担心；对此有一个简单的解决方法。Sapper 允许你动态地导入模块(嘿，更小的初始包大小)，所以你不需要在顶层导入模块。你所做的将会是这样的:

```
<script>
  import { onMount } from 'svelte';

  let MyComponent;

  onMount(async () => {
    const module = await import('my-non-ssr-component');
    MyComponent = module.default;
  });
</script>

<svelte:component this={MyComponent} foo="bar"/> 
```

Enter fullscreen mode Exit fullscreen mode

在第 2 行，我们导入了`onMount`函数。`onMount`函数内置在 Svelte 中，只有当组件安装在客户端时才会被调用(可以把它想象成 React 的`componentDidMount`)。

这意味着当只在`onMount`函数中导入我们有问题的模块时，该模块永远不会在服务器上被调用，我们也不会有丢失`window`对象的问题。那里！您的代码编译成功，一切又都好了。

哦，这种方法还有另一个好处:因为您对这个组件使用了动态导入，所以实际上最初向客户端发送的代码更少。

## 结论

我们已经看到了使用 Sapper 是多么直观和简单。路由系统非常容易掌握，即使对于绝对的初学者来说也是如此，创建一个 API 来驱动你的前端相当简单，SSR 非常容易实现，等等。

这里有很多我没有提到的特性，包括预加载、错误处理、正则表达式路径等等。真正受益的唯一方法是用它来建造一些东西。

现在你已经了解了 Sapper 的基础知识，是时候开始使用它了。创建一个小项目，打破东西，修复东西，到处乱搞，只是感受一下 Sapper 是如何工作的。你可能会坠入爱河。[![❤](img/00dac1f9d527f50a78e0e7af4db6d55a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ovFHVbVK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/2764.png)

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://logrocket.com/signup/)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[探索札幌+苗条:快速教程](https://blog.logrocket.com/exploring-sapper-svelte-a-quick-tutorial/)首先出现在[博客](https://blog.logrocket.com)上。