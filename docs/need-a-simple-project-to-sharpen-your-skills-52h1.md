# 需要一个简单的项目来磨砺你的技能？

> 原文：<https://dev.to/lucsan/need-a-simple-project-to-sharpen-your-skills-52h1>

我经常听到人们说他们需要一个简单的项目，但他们被所有的选项淹没了，那么他就是为你的需求创建一个简单项目的简单指南。

首先你需要一些数据，毕竟这是互联网，这是关于 T2 信息的。

不😰流汗吧，你不会想花很多时间在互联网上搜索，找到大量的数据，解析，清理，分类(除非你对机器学习感兴趣)，而是做一个简单的表格📑。

如果你的数据是一个很小的 4 乘 4 的表格，比如说，水果、价格、重量和 img(其中有 4 个水果),这也没关系，这对于任何项目来说都是足够的数据，如果你不喜欢水果，你这个怪人，就用你最喜欢的东西代替水果。

所以武装起来🍌香蕉和 3 种其他水果(🍎🥑🐷)，接下来的部分，虽然简单，但是比较难，你需要把重点放在你要演示的那部分工作上。

所以，如果你想展示你对数据库的掌握，把表格放在数据库中，对于 CSS 魔术师来说，只需用 html 制作表格，然后在浏览器中查看，对于编程大师来说，把表格制作成数组、对象、列表、目录或数据簇。

现在看看你的桌子🤔想想接下来怎么办？你想达到什么目的？列出你的水果的页面📰每种水果都有单独的一页？a📊水果图？

对于数据库，简单地设计尽可能多的方法来获取你的水果数据，对于编程，尽可能多的方法来操作你的水果数据，对于前端，尽可能多的方法来呈现你的水果，然后做其中的一些。

#### 🎈至此，你差不多完成了你的项目，简单吧！🎉

如果您是前端人员，那么明智的做法是将您的表格制作成一组 html 页面，并将数据直接写入页面。也许你使用过 Sass 或者更少，你可以把它全部上传到 github，制作一个 github 页面来运行你的站点。

对于我们其他人来说，工具链变得更加重要，您的 luverly 数据库及其表和 SQL 语句提供了丰富的内容🍏善良的各种格式，只能通过数据库浏览器(即:客户端)看到，你可能想显示你的🍒在网上也能结出果实。对程序员来说也是如此。

这取决于你想要展示多少开发序列，因为所有的部分都可以通过*其他东西*来处理，在某种程度上，你可以用互联网上其他地方找到的比特来创建一个项目。

像 Netlify(亚马逊)、Firebase(谷歌)、Glitch(不知道)这样的资源提供了网络空间和工具链。有了 Netlify，你可以使用 AWS lambda 函数，有了 Firebase，谷歌的等价物，当然，你可以以某种方式一起使用这三者(例如:让 Glitch 调用 AWS lambda 函数并使用 Firebase 用户登录)。

也许你可以简单地将你的项目拖放到其中任何一个。或者，您可以使用您最喜欢的数据库来 html 程序，该程序会根据数据库结构自动生成网页。简单来说，你可以找到一个工具来做你不想做的工作。*(但书:这个规则只对小项目有好处，你可能会花很多时间学习新工具)*。

💻这是我最近做的一个小项目的例子，有一个简单的堆栈。

我选择了 Netlify(而不是 firebase ),原因是我需要其中的一个。github 中的代码(obvo)，npm 中的简单 taskrunner(使用节点脚本)。

如果你感兴趣的话[https://github.com/lucsan/SpockScissorsPaper](https://github.com/lucsan/SpockScissorsPaper)找引擎[https://spockpaperscissors.netlify.com/](https://spockpaperscissors.netlify.com/)看网页应用。该应用程序的代码位于(运行在 https://github.com/lucsan/ReactScissorsPaper[反应](https://github.com/lucsan/ReactScissorsPaper)的 rspalator)

对于我的一点点数据，我想起了几年前我设计的一个项目🎲石头剪子布引擎，它给出一组项目，并返回两个项目相互对抗的结果。它接受任意数量的项目，但默认为 7 个。

从 TTD 的角度来看，这个项目本身就很不错，可能会取代 fizz-buzz (5s n 7s ),具有讽刺意味的是，我没有进行测试，因为我需要数据引擎。这也允许我拥有一个依赖项(RPS 引擎)，从而创建一个构建链。

然后我创建了一个使用 RPS 引擎的 React 项目，换句话说，就是一个让你玩 RPS 的网站。

github 上的代码库。Netlify 配置为在主更新时运行节点构建脚本，(并且，当 Travis 参与进来时，我也会参与进来)。如果你想添加更多的后端，那么也许可以添加一个登录(或一个旧的学校高分板，只有 3 个字母)保存到一些数据库或其他(或文件)。

对于前端来说，在游戏的机制和显示方面有很多东西。我添加了一些基本的引导文件，但是你可能会被 SASS，Grunt 等弄疯。

同样，这里的技巧是你使用的数据的简单性，以及你想展示技能的地方，因为开发运营部有很多需要连接的东西，对于后端，确保你添加了数据库，也许是购物篮，对于中间件，添加了客户表格，房间预订等，对于前端，确保你的 3 页看起来非常棒。

真正的诀窍是专注于你想要展示的部分(技能)，或者只是有机地进行，我有一个小桌子，我用它做什么？我知道我会很好地展示它，然后我知道，我会添加客户，购物篮，税收，折扣，散装折扣，缺货通知，关于水果状态的电子邮件更新，客户查询水果及其可用性的数据库，测试等等，一个将所有这些放在一起的构建链，简而言之，你需要/想要/兴奋地用你的一点点数据做什么。

希望这能以某种简单的方式帮助你理清你对下一个项目的想法。