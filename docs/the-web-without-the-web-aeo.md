# 没有网络的网络

> 原文：<https://dev.to/walaura/the-web-without-the-web-aeo>

# 1。

我 14 岁的时候建了第一个网站？这是模拟人生的粉丝网站。它用的是桌子，颜色不会通过任何可及性(或美学，就此而言)检查清单。如果我没记错的话，它是在 dreamweaver 中制作视觉效果的组合，然后将生成的 HTML 和 CSS 复制/粘贴到我自己制作的 PHP 后端中(您可以想象它在内部是什么样子)。

这是一个非常重要的网站，不仅因为它变得相对流行，而且对我来说也很重要，因为它成了我的个人游乐场。我自学了 CSS 来使布局更好，在这个过程中，我获得了足够的设计技能，可以作为一名设计师工作 4 年。我开始使用 Flash 制作横幅和动画(当时来自 macromedia！).

不久之后，web 2.0 时代到来了，我开始编写 mooFx 脚本，让事情变得更有趣。我不完全确定我的代码中发生了什么，我非常想念 PHP 标准库，但我还是挺过来了。最终选择了 jQuery，这是我多年来对 DOM 的全部理解。

我学到了更多关于 HTML 和 CSS 的知识。我学会了如何编写语义和可访问的代码。我学会了如何扩展 CSS 架构来防止代码混乱。

有一段时间，我的个人网站就像一个马里奥世界的舞台，一切都在移动，它使用 CSS 动画和一些 JS 点缀来添加无限的云和东西。

我爱上了我能做的所有酷东西。

<center>

[![](img/9ca4950135c6fc68ac27f44f925bc23e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ITal9QGx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ha6dim4gjonlx8ed3g6m.gif)

</center>

# 2。

我最终成为了一名受人尊敬的前端“工程师”。我做演讲之类的。LinkedIn 的招聘人员实际上在他们的问候信息上下了功夫。它统治一切。在这一点上我只是在吹牛。阻止我。

现在，尽管如此，你可能已经注意到我在工程职业生涯的介绍中错过了很多东西。

我不太清楚函数是如何映射到数学结构的。当我们开始做代数时，我实际上放弃了数学，在代数中，你要处理的不是等式，而是像`2*n > x/3`这样的东西，你必须找出`n`和`x`的范围(我不知道这个特定的问题是否可解，我只是想到了它)

我仍然只是在思考你可以在诸如 typescript 之类的东西中做的更复杂的类型。值得一提的是，我不会在个人项目中使用类型。我认为它们有利于使代码自我文档化，因此对团队也有好处。不过太抽象了。

很长一段时间，我从未使用过任何真正的 web 开发框架。我完全忘记了 angular 和 ember 时代，因为我认为一个网站不启用 JS 是不可接受的。当我们有语义 HTML 时，用 javascript 编写整个前端感觉非常不必要。

<center>

[![](img/85363039ec6abef632c208e89b272ef8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rwr1Mfp2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l2ejqlhzv58drpkmu5ep.jpg)

</center>

# 3。

我喜欢反应。我喜欢 React，因为我对扩展 HTML 和 CSS 有足够的了解，我理解单个文件组件的价值。我喜欢 CSS-in-JS 的方式，它给你足够的抽象，仍然可以使用像 blind owl 选择器这样的技巧，同时也给你使用 JS 做像容器查询这样的事情的全部能力。我喜欢服务器端渲染 React 应用程序是如此简单，因为它全部编译成普通的 HTML 而不是 web 组件，有效地将它变成一个可以活过来的模板引擎。我喜欢这种方式，通过使用完全语义标记，然后让水合作用做更多的事情，你仍然可以非常有效地进行渐进增强。

我也讨厌反应。我讨厌 React，因为这些行为不是默认的。如果您使用 div 和未标记的文本框制作表单并将整个表单发送到服务器，React 不会警告您。我讨厌 React，因为 CSS-in-JS 方法默认情况下鼓励你编写完全自包含的一次性组件，而不是试图构建一个完整的网站 UI。我讨厌服务器端渲染和渐进增强不是默认的，而是你必须去做的事情。

但我最讨厌 React 的地方是，它在 HTML、CSS 和 JS 的土地上一层一层地渐进，并用“高效”和“干净”的代码取而代之。
与普通的 HTML 相比，在 React 应用程序中添加类型检查非常容易，可以确保你不会意外添加`"12" + "8"`，否则你怎么知道你在这里没有得到 20。通过运行你的应用程序？荒谬。通过获取组件快照并及时有效地冻结它们，自动化测试也非常容易，不断发展的 CSS 布局被拦截。有了 CSS-in-JS，你可以忽略那些讨厌的 CSS 行为，比如层叠。又不是名字的第一个字母什么的。
没有那么多一流的 DOM 操作和强制性的构建步骤意味着工程师们终于可以开始为其他语言的 javascript 铺平道路，基于数学的纯粹和优雅，比如 Elm 或 Reason。

有些人认为这些都是好事。其他人认为这是一种自慰行为，服务于那些发现实际上编写网站太无聊，需要增加趣味的书呆子。

<center>

[![](img/06e85937e9c067fc72469d73062f2b88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ly1iydGi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4d74ecz0wvl2qx4fiu1x.jpg)

</center>

# 4。

我学东西出奇的快。我已经赶上了。我对这一切很了解。我也是个异类。我所知道的最好的前端开发人员现在完全不知所措。

大多数现代前端代码库不再是关于 HTML 和 CSS。事实上，他们是如此远离它，idk 20？总的来说，那些仍然关心 HTML 和 CSS，能够编写网站代码，还没有成为产品设计师的人甚至不知道从哪里开始。

我们“干净”和“优雅”的前端现在往往是后面比前面更干净。我们完美的 20 文件 redux 存储支持一个不支持 javascript 的表单。我们的 CSS-in-JS 模块用类型化的道具隐藏了用完全静态的道具作为实现细节的复杂 CSS。

我们正在建造带有世界级管道的厕所，人们不能坐在上面。知道如何修理马桶的人不知道如何在我们的管道系统中安装马桶。

懂 CSS 的设计师在不破坏一半测试的情况下，无法更新 GitHub 中的一些颜色。如果不弄清楚地图组件的属性类型，产品经理就无法替换页面中的一堆单词。可访问性专家不能用`buttons`替换`divs`，因为视觉回归测试表明 Windows Phone 6.5 中的 Opera mini 会渲染它们的边框，我们不能合并更改，直到它全部变绿。前端开发人员不能实现手风琴(老实说，这可能是最好的)，因为超级喜欢类型的家伙不会让她在 redux 之外存储状态。

在将前端提升到严肃代码领域的过程中，我们不仅让事情变得不可思议地过度设计，而且还点燃了我们最初用来爬上这里的所有梯子。

<center>

[![](img/4b1e2a02d0d49988916706fd9eb9eb08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BXJ_ErcV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j9bo057dlq3hm1t189a9.png)

</center>

# 5

我想了很多关于我是如何开始编码的。关于我如何发展对网络的理解。太疯狂了。我已经很久没碰代码了，但仍然在做很酷的网站。我们只是让事情运转起来。从设计上来说，网络总是“尽力而为”。你应该使你的 HTML 易于访问和语义化，然后以一种灵活的方式编写你的 CSS，这样当它不可避免地在一个不同的/旧的浏览器中崩溃时，页面仍然可以工作。然后你在上面撒上你的 javascript，如果不起作用呢？这很好，因为它不需要发挥作用。简直是锦上添花。

我喜欢 React，因为它让我更快、更容易地完成我的最佳工作。我讨厌 React，因为它周围的文化比库本身更积极地阻止其他人做他们最好的工作。

<center>

[![](img/ccfdb6f517442df535be89f32f0b7cb0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GT9NJva6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pmbz29o37bmccm191j67.png)

</center>