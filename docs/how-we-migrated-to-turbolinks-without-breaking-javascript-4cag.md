# 我们如何在不破坏 Javascript 的情况下迁移到 Turbolinks

> 原文：<https://dev.to/honeybadger/how-we-migrated-to-turbolinks-without-breaking-javascript-4cag>

现在是 2019 年，所以我们决定是时候采取更现代的方法来处理[蜜獾](https://www.honeybadger.io/)前端了。我们实现了 Turbolinks！这只是雄心勃勃的路线图的第一步。2025 年，我们计划迁移到 Angular 1，我们将在 React 上完成十年，除非我们遇到任何障碍！

但是让我们现实一点。Honeybadger 不是一个单页应用程序，可能永远也不会是。spa 对我们的技术要求来说毫无意义。看一看:

*   我们的应用程序主要是显示静态信息页面。
*   我们处理大量数据来生成一个错误报告页面。
*   我们有一个由四名开发人员组成的非常小的团队，所以我们希望我们的代码库尽可能的小和简单。

## PJAX 的日子

有一种方法我们已经用了很多年，让我们既有蛋糕又能吃到它。它被称为 PJAX，它的主要思想是您可以获得类似 SPA 的速度，而无需所有的 Javascript。当用户点击一个链接时，PJAX 库拦截它，获取页面并用新的 HTML 更新 DOM。

它并不完美，但比你想象的要好——尤其是对于我们这样的应用程序。唯一的问题是我们的 PJAX 库不再被维护，并且阻止我们更新 jQuery(唉)。所以它必须消失。

## 移动到涡轮链

现在如果你仔细想想，PJAX 听起来很像 Turbolinks。它们都使用 JS 来获取服务器呈现的 HTML，并将其放入 DOM。它们都进行缓存并管理前进和后退按钮。这就好像 Rails 团队采用了其他地方开发的技术，只是对它进行了重新命名。

嗯，我很高兴他们这么做了，因为 Turbolinks 是一款比以前好得多的软件。它是主动维护的，根本不需要 jQuery！所以我们离抛弃`$`的梦想又近了一步。

在本文中，我将向您介绍我们从 PJAX 到 Turbolinks 的迁移。好消息是 Turbolinks 开箱即用的效果惊人地好。唯一棘手的事情是让它与您的 JavaScript 一起工作。在这篇文章结束时，我希望你会对如何做有一个好的想法。

## Turbolinks 是一个单页应用程序

Turbolinks 不只是给你一些单页应用程序的好处。Turbolinks **是**单页应用。想一想:

1.  当有人访问你的网站时，你为他们提供一些 HTML 和 Javascript。
2.  JavaScript 接管并管理 DOM 的所有后续更改。

如果这不是一个单页应用程序，我不知道什么是。

现在让我问你，你为单页应用程序编写 JS 与“传统的”web 应用程序不同吗？我当然希望你知道！在“传统的”应用程序中，您可以避免粗心大意，因为每次用户导航到新页面时，他们的浏览器都会破坏 DOM 和 JavaScript 上下文。然而，水疗需要更周到的方法。

## 一种行之有效的 JS 方法

如果您已经工作了一段时间，您可能还记得编写过类似这样的代码:

```
$(document).ready(function() {
  $("#mytable").tableSorter();
}); 
```

每当文档完成加载时，它使用 jQuery 初始化一个表排序插件。让我问你:当页面卸载时，卸载表格排序插件的代码在哪里？

没有了。不需要回到那天，因为浏览器处理了清理工作。然而，在像 Turbolinks 这样的单页面应用程序中，浏览器*并不*处理它。作为开发人员，您必须管理 JavaScript 行为的初始化**和**清理。

当人们试图将传统的 web 应用移植到 Turbolinks 时，他们经常会遇到问题，因为他们的 JS 从来不会自动清理。

所有 Turbolinks 友好的 JavaScript 需要:

1.  显示页面时初始化自身
2.  在 Turbolinks 导航到一个新页面之前清理自身。

对于新项目，我会推荐使用 Webpack，以及类似于 [Stimulus](https://github.com/stimulusjs/stimulus) 的轻量级框架。

## 捕捉事件

Turbolinks 提供了自己的事件，您可以捕获这些事件来设置和删除您的 JavaScript。让我们从拆除开始:

```
document.addEventListener('turbolinks:before-render', () => {
  Components.unloadAll(); 
}); 
```

除了第一个浏览量之外，`turbolinks:before-render`事件在每个浏览量*之前触发。这是完美的，因为在第一次浏览量没有什么可拆的。*

初始化的事件稍微复杂一些。我们希望我们的事件处理程序运行:

1.  在初始页面加载时
2.  在对新页面的任何后续访问中

以下是我们捕捉这些事件的方式:

```
// Called once after the initial page has loaded
document.addEventListener(
  'turbolinks:load',
  () => Components.loadAll(),
  {
    once: true,
  },
);

// Called after every non-initial page load
document.addEventListener('turbolinks:render', () =>
  Components.loadAll(),
); 
```

不，你没疯。这段代码对于它所做的来说似乎有点太复杂了。你可能会认为在任何一个页面被加载后都会有一个事件被触发，不管加载它的机制是什么。然而，据我所知，没有。

## 对缓存又爱又恨

Turbolinks 网站看起来比传统网络应用更快的一个原因是它的缓存。然而，缓存可能是一个非常令人沮丧的来源。我们将要讨论的许多边缘情况都以某种方式涉及到缓存。

现在，你只需要知道:

1.  Turbolinks 会在离开页面之前立即缓存页面。
2.  当用户点击“后退”按钮时，Turbolinks 从缓存中取出上一页并显示出来。
3.  当用户单击他们已经访问过的页面的链接时，缓存版本会立即显示。该页面也是从服务器加载的，稍后显示。

## 经常清除缓存

每当你的前端持久化任何东西，你可能应该清除缓存。涵盖这些情况的一种简单方法是，每当前端发出 POST 请求时，清除缓存。

在我们的例子中，90%的请求来自 Rails 的 UJS 库。因此，我们添加了以下事件处理程序:

```
$(document).on('ajax:before', '[data-remote]', () => {
  Turbolinks.clearCache();
}); 
```

## 不要期望一个干净的 DOM

Turbolinks 会在你离开页面之前缓存页面。那可能是在之后的*你的 JavaScript 已经操纵了 DOM。*

假设您有一个处于“打开”状态的下拉菜单。如果用户离开页面，然后返回，菜单仍然是“打开的”，但是打开它的 JavaScript 可能已经不在了。

这意味着您必须:

*   编写您的 JS，这样它就不会因为遇到它在不干净状态下操纵的 DOM 元素而惊慌失措。
*   当您的组件被“卸载”时，确保将 DOM 返回到适当的状态。

这些需求很容易在您的 JavaScript 中得到满足。然而，它们可能更难与第三方库相遇。例如，如果 Turbolinks 在 Bootstrap 的模态处于“打开”状态时缓存它们，Bootstrap 的模态就会中断。

我们可以通过在缓存页面之前手动整理 DOM 来解决模式问题。下面，我们从 DOM 中移除任何打开的引导模式。

```
document.addEventListener('turbolinks:before-cache', () => {
  // Manually tear down bootstrap modals before caching. If turbolinks
  // caches the modal then tries to restore it, it breaks bootstrap's JS.
  // We can't just use bootstrap's `modal('close')` method because it is async.
  // Turbolinks will cache the page before it finishes running.
  if (document.body.classList.contains('modal-open')) {
    $('.modal')
      .hide()
      .removeAttr('aria-modal')
      .attr('aria-hidden', 'true');
    $('.modal-backdrop').remove();
    $('body').removeClass('modal-open');
  }
}); 
```

## 从正文中删除所有 Javascript

Turbolinks 运行它在 HTML 主体中遇到的任何 javascript。这种行为听起来可能有用，但它会招致灾难。

在“传统”的 web 应用程序中，放在程序体中的脚本精确地运行一次。然而，在 Turbolinks 中，它可以运行任意多次。它会在用户每次查看该页面时运行。

*   你有没有一个第三方的聊天小部件可以在页面中注入一个`<script>`标签？准备好被注入 10 个、50 个、100 个脚本标签。
*   你设置了一个事件处理器吗？准备好获取 100 个这样的页面，让它们在你离开页面时保持活跃。
*   你用谷歌分析跟踪页面浏览量吗？每次用户访问缓存的页面时，要准备好注册两次页面浏览。为什么？Turbolinks 首先显示缓存版本，然后立即显示页面的服务器呈现版本。因此，对于一个“页面视图”，页面的内联 JS 运行两次。

问题不仅仅是内联 JavaScript。它是放置在文档主体中的任何 JavaScript，即使是作为外部文件加载的。

所以帮自己一个忙，把所有的 JavaScript 都放在文档的头部，它应该在的地方。

## 使用 JS 模块加载第三方小工具

如果你不能使用内联 JS 来加载你的第三方部件，你怎么做呢？许多库，比如我们自己的`honeybadger-js`库，提供了 npm 包，可以用来将它们导入 webpack 或其他构建工具。然后您可以导入它们并在 JS 中配置它们。

```
// Here's how you can set up honeybadger-js inside webpack.
// Because the webpack output is included in the document head, this 
// will only be run once. 

import Honeybadger from 'honeybadger-js';

const config = $.parseJSON($("meta[name=i-honeybadger-js]").attr('content'));

Honeybadger.configure({
  api_key: this.config.key,
  host: this.config.host,
  environment: this.config.environment,
  revision: this.config.revision,
}); 
```

有很多方法可以从服务器传递像 API 键这样的数据。我们将它们编码为 JSON，并将它们放在每个页面上都有的 meta 标签中。

```
%meta{name: "i-honeybadger-js", content: honeybadger_configuration_as_json} 
```

可悲的是，一些第三方服务不提供 npm 包。相反，他们让你在 HTML 中添加一个`<script>`标签。为此，我们编写了一个 JS 包装器，将脚本注入 dom 并对其进行配置。

这里有一个例子，说明我们如何为购买我们服务的用户包装 heroku 小部件。

```
class Heroku extends Components.Base {

  // For every page load, see if heroku's JS is loaded. If not, load it.

  // If so, reinitialize it to work with the reloaded page. 

  initialize() {

    this.config = $.parseJSON(this.$el.attr('content'));

    if (this.herokuIsLoaded()) {

      this.initHeroku();

    } else {

      this.loadHeroku();

    }

  }

herokuIsLoaded() { 
归来！！窗户。回旋镖；
 }

initHeroku() { 
窗口。boomerang . init({ app:this . config . app，addon:' honey badger ' })；
 }

loadHeroku(){
const script = document . createelement(' script ')；
script . type = ' text/JavaScript '；
script . async = true；
script . onload =()=>this . initheroku()；
script . src =
'<[https://S3 . Amazon AWS . com/assets . heroku . com/boomerang/boomerang . js&gt；](https://s3.amazonaws.com/assets.heroku.com/boomerang/boomerang.js>)’；
document . getelementsbytagname(' head ')[0]。appendChild(脚本)；
 } 
 }

components . collection . register({
选择器:' meta[name=i-heroku]'，
 klass: Heroku，
})； 
```

## 
  
从容应对资产更新

由于 Turbolinks 是一个单页面应用程序，在您部署之后，活跃的用户可能仍然在使用您的 JS 和 CSS 的旧副本。如果他们请求一个依赖于新资产的页面，你就有麻烦了。

幸运的是，您可以告诉 Turbolinks 注意资产文件名的变化，并在它们发生变化时进行硬重新加载。这种方法在 Rails 中工作得很好，因为您的应用程序 CSS 和 JS 通常会在文件名后附加一个内容哈希。

为了启用这个特性，我们需要在适当的`<style>`和`<link>`标签上设置`data-turbolinks-track`属性。对于 rails/webpacker，它看起来像这样:

```
= stylesheet_pack_tag "application", "data-turbolinks-track": "reload"

= javascript_pack_tag 'application', "data-turbolinks-track": "reload" 
```

## 
  
把属于多通的东西交给多通

最后，要意识到使用 Turbolinks 涉及到放弃对一些事情的控制。

*   你不能使用 JS 以任何方式操纵窗口位置*而不破坏 Turbolinks。我们一直在 URL 哈希中保存当前选择的选项卡状态，但不得不删除它。*
*   使用 jquery 伪造链接点击是行不通的。相反，你应该手动调用`Turbolinks.visit`。

## 结论

我是 Turbolinks 的粉丝。我们已经在这里讨论了许多边缘情况，但在大多数情况下，它开箱即用。

PJAX 几乎触及了我们前端的每一个部分。取代某个核心的东西从来都不是一件容易的事。然而，我不得不说迁移比我预期的要顺利得多。

我们已经在生产中运行了几个星期，只有两个小的错误报告。在很大程度上，似乎没有人注意到这种转变，这是我的理想结果。