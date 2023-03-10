# Ajax 和异步代码

> 原文：<https://dev.to/krtb/ajax-and-async-code-27je>

# 什么是 Ajax，为什么？

首先，我想说的是，每当我谈到阿贾克斯的时候，我都会情不自禁地想起杰克斯，那个在真人快打有两只金属手臂的战士...还有人吗？

<figure>

[![](img/44385be8b21b34f2ec7274971ad4c004.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WHLk7UYv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://vignette.wikia.nocookie.net/mkwikimg/4/4e/JAX.gif/revision/latest%3Fcb%3D20090531180518)

<figcaption>Jax Briggs</figcaption>

</figure>

Ajax 是一种从网页访问网络服务器的技术。它代表“异步 JavaScript 和 XML”。是啊，有点拗口，所以让我们花点时间解释一下。

<figure>

[![](img/3cf9df14d13e3065bbc6cd8cc7443ce9.png)](https://i.giphy.com/media/NbgeJftsErO5q/giphy.gif)

<figcaption>When you see those bugs start crashing things...</figcaption>

</figure>

现在，在您开始宣称 Ajax 是某种 API 或 NPM 包之前，您应该理解 Ajax 更像是一个描述由多种技术构建的模型的术语，其中 **XMLHttpRequest** 对象是最关键的。

### [那些甜蜜的 MDN 文档更具技术性的阅读](https://developer.mozilla.org/en-US/docs/Web/Guide/AJAX)

一个 **XMLHttpRequest 对象**，或者简称为 XHR，允许你与服务器交互！是的，你也可以拥有权力！

<figure>

[![](img/3339750fa0e1333a5f082d9707393d96.png)](https://i.giphy.com/media/gC42Rz5MaVJGNKIxMC/giphy.gif)

<figcaption>How my lame jokes make all my friends feel</figcaption>

</figure>

这就是事情变得超级有趣的地方。我知道，好像他们以前不有趣似的对吧！？因此，当考虑 MDN 时，要了解 XHR 允许我们作为开发人员做的事情的关键是...

> "你可以从 URL
> 中检索数据，而不必刷新整个页面."
> 
> *   带着爱，MDN 神

当这种能力被注入到 ReactJS 之类的东西中时，我们能够在幕后更新我们的 web 应用程序的特定部分，同时为用户提供无缝的体验。这种一手给用户展示一个稳定干净的网站，一手完成我们的魔术的行为叫做*异步请求*。

<figure>

[![](img/290374655628514755e96f49a12b6dbf.png)](https://i.giphy.com/media/AEMyf9Oj6MpS8/giphy.gif)

<figcaption>"Don't look! Coding is much harder that it appears! Shhhh"</figcaption>

</figure>

在这一点上，我们留下了两个挥之不去的问题。

1.  什么是 *XMLHttpRequest* 对象？
2.  *异步*是什么意思？

要创建一个 XHR 请求，如果你已经在游戏中呆了很长时间，你可能已经写了类似我下面的东西

```
const request = new XMLHttpRequest();

```

多亏了其他开发人员的进步(和令人头疼的问题),我们现在有了大量的库或更常用的 *Fetch API* ,它允许读写更通用的可理解的请求。

对 api 的获取请求的示例如下

### [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)上取更多

<figure>

```

fetch('https://swapi.co/api/people/1')
.then(response => response.json())
.then(data => console.log(data, 'STAR WARSSSSS'))

```

<figcaption>I used this api here, "https://swapi.co/", try it!</figcaption>

</figure>

虽然 XML 有很大的作用，但是这些请求可以像上面的 JSON 一样提取不同的数据类型。

当我们使用 console.log()时，我们的对象如下所示

```
{
    "name": "Luke Skywalker",
    "height": "172",
    "mass": "77",
    "hair_color": "blond",
    "skin_color": "fair",
    "eye_color": "blue",
    "birth_year": "19BBY",
    "gender": "male",
    "homeworld": "https://swapi.co/api/planets/1/",
    "films": [
        "https://swapi.co/api/films/2/",
        "https://swapi.co/api/films/6/",
        "https://swapi.co/api/films/3/",
        "https://swapi.co/api/films/1/",
        "https://swapi.co/api/films/7/"
    ],
    "species": [
        "https://swapi.co/api/species/1/"
    ],
    "vehicles": [
        "https://swapi.co/api/vehicles/14/",
        "https://swapi.co/api/vehicles/30/"
    ],
    "starships": [
        "https://swapi.co/api/starships/12/",
        "https://swapi.co/api/starships/22/"
    ],
    "created": "2014-12-09T13:50:51.644000Z",
    "edited": "2014-12-20T21:17:56.891000Z",

```

异步基本上意味着，多件事情可以同时发生。如果你点击一个按钮来查看更多星球大战的事实，你的 JavaScript 页面不必停止它正在做的事情，它可以继续向用户显示一个很酷的交互站点，并在后台开始从 API 端点请求数据。

今天，大多数浏览器运行在基于 JavaScript 的引擎上，JavaScript 本身习惯于同步运行，这意味着它必须等待一个任务完成，然后才能按照任务被编码创建的顺序继续执行另一个任务。

通常 JS 引擎不是唯一运行的引擎。我们有像渲染引擎这样的项目，或者如我们所见，通过 HTTP 请求请求数据的元素。因此，尽管 JS 代码以同步方式运行，但还有其他部分在异步工作。

JS 有一个执行堆栈，由执行上下文构建而成，这些上下文是按照它们被创建的顺序来创建和运行的。一旦完成，每个执行上下文就消失了。

与之并行的是**事件队列**，由事件和正在发生的事件的通知组成，例如按钮点击。

但是，这里有一个很大的但是，JS 只是在执行堆栈清空之后才开始关注事件队列。

如果一个事件导致一个函数被创建和执行，当我们到达那个事件时，一个执行上下文被创建，就像处理一个 HTTP 请求一样，正如我们在上面的 fetch 中看到的。因此，它被放在调用堆栈中运行。就是这样！

JS 持续运行一个事件循环，定期检查是否有其他事情要做，这允许 JavaScript 在它的初始执行堆栈耗尽后返回查看事件。

JS 同步查看事件，但是浏览器会以异步方式将它们放入事件队列。JS 可以继续加载您在应用程序中编写的所有文件和函数，而特定的触发器和事件可以在一定程度上独立于 JS 引擎。

[![](img/96331b98c3d8b21608bd161fd2757b7c.png)](https://i.giphy.com/media/mp0X9k8eVoVgI/giphy.gif)