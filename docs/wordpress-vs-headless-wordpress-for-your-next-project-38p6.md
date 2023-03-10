# Wordpress 与 Headless Wordpress 的比较

> 原文：<https://dev.to/jay_dennis_0/wordpress-vs-headless-wordpress-for-your-next-project-38p6>

首先，如果你已经知道 angular 或 react 这样的前端框架，你就不需要阅读这篇文章。我写这篇文章是为了 Wordpress 用户(甚至是超级用户)，那些混合开发者，以及那些定制了一些 Wordpress 主题，或者构建了一两个插件，并且正在寻找如何稍微升级他们游戏的资源的人。它也是为那些敢于自己动手的人写的，他们不怕修补一些东西，直到他们得到一些有用的东西。

这篇文章的目的是让对 Wordpress 有中级理解的人(你应该知道“循环”是什么)为你的下一个冒险或项目介绍一些超越简单的“主题化”的额外选择

当你考虑或开始一项新的冒险时，你可能已经对 Wordpress 的受欢迎程度有了一些概念。

Wordpress 为 100 万个顶级网站中的 30%提供支持。这是一个内容管理系统(CMS ),由一个庞大的开发者和用户社区支持，是开源的，并且定期更新。

简而言之，Wordpress 允许你发表——至少——一个老派的博客，并把你的文章放在某个地方。最多，Wordpress 可以是一个高度定制的(视觉上和功能上)解决方案。

碧昂斯的网站，Vogue 的网站，奔驰国际的网站都用 Wordpress。至于你能用这个框架实现什么，证据就在布丁里。但是有一些警告。

如果你过去使用过 Wordpress，或者有一段时间没有参与为一个组织创建网站，可能有一些你不知道的关于 Wordpress 的事情。

*   网站的速度比以往任何时候都更重要(对于用户体验和 SEO 而言)。
*   Wordpress 比以往拥有更多的功能(以及比你想象的更多的插件和主题选项)，但是 Wordpress 很容易变得臃肿，降低性能并增加安全漏洞。
*   2015 年 12 月，Wordpress 将 Wordpress REST API 集成到 Wordpress 核心中。

目前，这对你来说可能没什么意义。

但是简单地说，Wordpress Rest API 支持:

*   自定义 Wordpress 的一系列新路线。
*   一些潜在的方法来回避臃肿的 Wordpress 站点可能出现的速度和安全问题。

其中一种方式叫做**无头 Wordpress。**

什么是无头 Wordpress？

概括地说，headless Wordpress 涉及到使用 Wordpress 作为你的网站的“后端”,使用另一种技术作为“前端”

这包括使用 Wordpress 的内置分类来存储文章、媒体和你数据库中的其他项目。Wordpress 在知道为用户提供什么帖子方面速度很快。许多开发者知道如何使用和扩展 Wordpress 的内置功能。Wordpress 的结构对很多很多在线存储的媒体都有意义。

一旦这些信息从你的 Wordpress 后端被查询，它就会到达你的网站的前端，或者说是可视化的“表示层”。自从 Wordpress 创建以来，前端技术已经有了很大的进步。虽然你可以用 Wordpress 前端做很多事情，但这是许多主题陷入不必要的代码和插件膨胀的地方。

那么这是如何工作的呢？

嗯，我们提到 12 月 15 日的 Wordpress 核心更新包含了对 Wordpress Rest API 的访问。这到底是什么？

API 代表应用程序编程接口。

简而言之，API 允许程序查询某些 URL 来接收信息。一旦他们获得信息，负责显示内容的站点部分就决定如何处理返回的消息。

正如我们提到的，Wordpress 在存储和管理你的文章、数据和媒体方面非常快。它响应查询的速度也非常快。在人类可读的格式中，查询可能会说类似于“将作者约翰·克拉克从 2017 年 6 月到 10 月的所有帖子返回给我”或“按日期升序返回所有最近上传的照片”

Wordpress Rest API 让 Wordpress 后端完成所有的存储、管理和查询响应。简而言之，Wordpress 真正擅长的事情。

那么这对网站所有者和访问者来说意味着什么呢？

一个高度优化的 Wordpress 后端可能对性能有很大帮助。但是就其本身而言，一个无头的 CMS 对网站所有者和访问者来说没什么用。事实上，独立的 WP 后端基本上没有视觉输出。

那么是什么原因呢？

随着现代网络变得越来越复杂，臃肿的 javascript(和拙劣的 PHP)文件已经拖慢了网络上许多网站的速度。尽管许多这些“升级”旨在使网站达到 2019 年苛刻的用户体验标准，但它们往往适得其反。

这里的寓意是，自从许多 Wordpress 网站建立以来，网络已经发生了很大的变化。而你可以在旧网站的基础上逐步“更新”你的网站。到了某个时候，你可能会想要一刀两断。

在过去的几年里，出现了许多顶尖的前端技术。所有这些都可以连接到 Wordpress 后端。

Angular、React 和 Vue.js 等前端框架彻底改变了我们编写前端代码的方式。这些框架更有组织性，速度更快，并且在他们使用的许多网站上提供类似于渐进式网络应用的用户体验。

有了 Wordpress 处理后端，前端有了一个现代的框架，网站更快、更安全、更容易维护，也更有可能满足当今网民所要求的 UX 标准。

我需要无头 Wordpress 吗？

尽管上面说了很多，但我绝不是说每个网站都应该设置一个无头的 WP。有很多很多可行的例子表明，一个无头的 WP 是不必要的重新发明轮子，创建和维护可能更昂贵，并且不会提供明显的好处。

所以让我们来看看无头 wordpress 设置的优缺点，这样你就可以决定哪种设置最适合你。

**具有现代前端框架的无头 Wordpress 的优点**

*   混合 Wordpress 和其他框架的优点
*   潜在地提高速度和安全性
*   让你的网站更接近一个进步的网络应用，而不是 Wordpress 网站
*   更广泛的定制选项
*   一段时间后的未来

无头 Wordpress 与现代前端框架的缺点

*   现代前端框架比 Wordpress 有更陡峭的学习曲线
*   了解现代前端框架的开发者比 Wordpress 少
*   比简单的 Wordpress 主题更昂贵或耗时
*   对许多站点来说，不必要的复杂

最终，只有一部分新网站需要考虑无头 Wordpress。然而，如果你认为你的网站可能会发展得不仅仅是一个博客，你可能会发现自己正在考虑将来在 Wordpress 的基础上构建一些东西。

我如何设置无头 Wordpress？

有大量的指南为相对初学者定制 Wordpress 主题。这适用于管理菜单或拖放编辑器中的修饰性变化，以及构建您自己的主题的指南。有了 HTML、CSS 的基础知识，以及通过 PHP 排除故障的能力，作为一个初学者，你可以从头开始构建一个 Wordpress 主题。

虽然无头 Wordpress 可能也是如此，但大多数现代前端框架的学习曲线比最小化修改 Wordpress 要陡峭得多。也就是说，完全有可能自学所有必要的工具。

下面我们将概述最简单的设置。如果你能够完成以下步骤，并发现自己陷入了更复杂的变化中，你可以雇佣他人，至少可以为自己节省一些初始设置的费用。

**要事第一，主持。**

你可以构建世界上最前沿的无头 wordpress 应用程序，如果没有高质量的托管，它就不会很棒。为了真正获得无头 Wordpress 的许多优势(其中之一是速度)，你需要一个高质量的托管服务。

虽然有很多方法可以获得超快的托管，但对于 DIY 者和初学者来说，我建议找一个顶级的托管 Wordpress 主机。关于托管 Wordpress 主机的一个很好的资源可以在这里找到。我推荐托管 Wordpress 主机的原因是，你的应用程序的后端仍然依赖 Wordpress。为了让 Wordpress 真正发挥其功能，你需要对你的主机进行全面的优化，更新，并适合 Wordpress。

**接下来，设置 Wordpress。**

你需要全新安装。鉴于有很多[教程](https://www.wpbeginner.com/how-to-install-wordpress/) [out](https://themeisle.com/blog/how-to-install-wordpress/) [there](https://www.themeum.com/install-wordpress-localhost/) 关于如何使用著名的“一键安装”来设置 Wordpress，这一步你应该不会有太多麻烦。

假设，如果你正在学习本教程，你已经花了一些时间在编辑或构建 Wordpress 主题的基础上。虽然你会想在你的新托管主机上安装 Wordpress，但是请注意，你可能会想在本地安装它，以便进行构建你的新无头 Wordpress 站点的试错。我见过的关于这个话题的最好的指南之一可以在这里找到。

然后，你将主题留空。

Wordpress 有大量惊人的主题。但这不是重点，因为我们将使用另一个自定义前端。

也就是说，Wordpress 后端仍然需要 Wordpress 主题。所以让我们创建一个新的主题，并确保前端没有加载任何东西(通过 Wordpress)。

在最基本的水平，所有你需要做的是创建一个新的主题是创建 index.php 和风格. css 文件。

在 style.css 文件中，只需包含一些基本信息。要正式“制作”一个新主题，您只需包括以下内容:

*   主题名称
*   作者
*   描述
*   主题 URI
*   版本
*   许可证
*   和其他细节

这些在 style.css 文件的顶部被注释掉，如下所示。

```
/*
 Theme Name:   Twenty Seventeen Child
 Theme URI:    http://example.com/
 Description:  Twenty Seventeen Child Theme
 Author:       Your Name
 Author URI:   http://example.com
 Template:     twentyseventeen
 Version:      1.0.0
 License:      GNU General Public License v2 or later
 License URI:  http://www.gnu.org/licenses/gpl-2.0.html
 Tags:         light, dark, two-columns, right-sidebar, responsive-layout, accessibility-ready
 Text Domain:  twentyseventeen-child
*/ 
```

然后，在你的 index.php 文件中，你会希望通过重定向到你的主页来阻止 Wordpress 加载前端。将类似以下内容的重定向插入到您的 index.php 文件中:

`

```
<script type=”text/javascript”>
    Const pContain = document.getElementById(‘posts-list’)
    fetch(‘http://YourSite.com/wp/wp-json/wp-v2/posts’)
  // Response to request
    .then(r => r.json))
    .then(posts => {
      posts.map(post => {
        pDiv.innerHTML = post.title.rendered
        pContain.appendChild(pDiv)
    })
    })
  </script>
  </body>
</html> 
```

然后，探索 Wordpress API 的结构。

既然我们已经建立了我们的 Wordpress 后端并清空了前端，我们准备开始查询 Wordpress API。如果没有 Wordpress 内置的页面和功能的前端层次结构，从 API 返回的数据将是你想怎么处理就怎么处理。

为了下面的工作，你需要在你的 Wordpress 数据库中有一些数据。您可以在许多地方找到示例内容来填充您的站点。或者你可以写一两篇填充文章(或者默认的“Hello World”文章)。

有很多方法可以查询像 Wordpress 这样的 RESTful APIs，但是最简单的方法之一是使用 Fetch API。

您可能希望将获取脚本包含在 index.php 文件的文件头中，如下所示:
`

```
<script src=’https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js’ charset=’utf-8’></script> 
```

或者通过像 NPM 这样的分组管理器。

然后你可以开始使用一些相对基础的 Javascript 查询你的 Wordpress 后端。

下面可以看到一个简单的 get 请求:
`

```
//url (required), and options (optional)
fetch(‘https://YourWebsite.com/wordpress/wp-json/wp/v2/posts’,{
    Method: ‘get’
}).then(function(response){

}).catch(function(err){

    //error handling

}); 
```

虽然 Node 和 React 有许多更突出的请求库(在您构建站点时，您[可能会](https://jslancer.com/blog/2017/05/10/why-i-stop-using-fetch/)想要使用这些库)，但 fetch 很好地满足了我们的目的，它以一种新用户应该能够理解的方式清楚地显示了 Fetch 请求期间发生的事情。

注意，默认情况下，Wordpress API 文章的路径将被设置为/wp-json/wp-v2/posts。记住，Wordpress 中的帖子实际上包含了一系列数据类型，包括媒体、页面、附件、自定义 css 文件等等。

虽然上面应该会返回一个可以在你的检查器中查看的帖子列表，但是它的前端还没有完成。

下面是您可以做的事情的基本版本，将以下 Javascript 粘贴到 index.php 的主体中:

`

```
<script type='text/javascript'>
     Window.location ='http://www.YourPage.com';
</script> 
```

虽然是基本的，但这显示了 Wordpress API 的强大。当我们的 JSON 帖子或相关数据被返回时，我们可以按照我们认为合适的方式操作我们的对象。上面的例子只是放置了一个

在你的主页上的 div 的文章以及文章标题。就这么简单。

**选择一个前端框架。**

上面查询 Wordpress 数据库的例子很明显是为了向你展示使用无头 Wordpress 是多么的“容易”。在上面没有改变的情况下，使用 Wordpress 典型的前端功能会好得多。

当你可以使用一个更现代的前端框架的完全定制的解决方案时，你会发现在分离 Wordpress 的前端和后端中的真正力量。

如果您已经在本教程中学习了这么多，并设法跟上了进度，那么您可能至少可以轻松阅读 HTML、CSS 和 JS。虽然学习曲线可能很陡，但很多处于这个位置的人都自学了 React 或 Node。参加一个简短的入门课程，或者浏览一个教程，看看这两个框架中的任何一个是否能引起你的共鸣。

我最喜欢学习 react 的一个例子是 Medium.com 的这个 [FreeCodeCamp 帖子](https://medium.freecodecamp.org/learning-react-roadmap-from-scratch-to-advanced-bff7735531b6?gi=2e59c4eecd06)。

对于 Node，查看一下 [NodeSchool.io](https://nodeschool.io/) ，这是一个非常酷的教程，存在于支持 Node 的命令行中。

最后，随心所欲地定制。

为了充分利用一个无头 Wordpress 设置的能力，你可能想要为你的 Wordpress API 创建定制的端点。就像你可以在传统的 Wordpress 设置中创建自定义文章类型一样，你可以使用[高级自定义字段](http://www.advancedcustomfields.com/)插件来创建自定义文章类型。以及 [ACF to REST API](https://wordpress.org/plugins/acf-to-rest-api/) 插件，支持在您的 API 中提供定制的文章类型。

请注意，你的 Wordpress 后端将在构建一个无头 Wordpress 设置的整个过程中启用。你可以在你的主题中使用 functionality 内置的各种插件或功能，只是不会有太多的视觉输出。