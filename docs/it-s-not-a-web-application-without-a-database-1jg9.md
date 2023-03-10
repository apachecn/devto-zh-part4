# 没有数据库就不是网络应用

> 原文：<https://dev.to/jculverhouse/it-s-not-a-web-application-without-a-database-1jg9>

<figure>[![Rack of hard drives perhaps attached to a database server supporting a web application](img/9410f1ada98c7a26d93da951b031c003.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RSjbwdas--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/bandwidth-close-up-computer-connection-data-database-1563027-pxhere.com-1.jpg%3Fresize%3D840%252C561%26ssl%3D1) 

<figcaption>我想我刚刚跳过了一点！？</figcaption>

</figure>

在互联网出现之前，我们用电脑做什么？？如果你没有网络连接，你的智能手机或笔记本电脑值多少钱？没有数据库的 web 应用有什么用？语言和建筑哪个更重要？演员还是剧本？这些…都是问题…但是一个没有另一个就没有工作。尽管如此，一个 web 应用程序需要一个强大的 T2 数据库层，但是需要抽象来隐藏代码中的原始数据。因此，我们需要像选择编程语言一样谨慎地选择数据库解决方案。没错，如果我们不谈论父亲的 VHS 租赁店及其单一登记的解决方案(以前也有过！)

## 不要备用数据库层

作为一名开发人员，[对象关系映射](https://en.wikipedia.org/wiki/Object-relational_mapping) (ORM)的理念是将数据存储和检索的底层语义从应用程序代码中分离出来。ORM 在代码中抽象数据库功能，就像模板系统在代码中抽象 HTML 或 JavaScript 一样。每个人的最爱，[栈溢出](https://stackoverflow.com/)，有一个非常好的贴子[ORM](https://stackoverflow.com/questions/1279613/what-is-an-orm-how-does-it-work-and-how-should-i-use-one)的定义以及在项目中使用 ORM 的利与弊。当你在那里的时候，也看看 [Stack Overflow 的最新问题标签为【Rust】](https://stackoverflow.com/questions/tagged/rust)——也许你可以帮助别人！

ORM 工具在应用程序开发中承担了很大的责任。在开始时，这个`Diesel`箱已经为我的示例应用程序承担了这个任务。由于`Rust`和`Diesel`都是如此的新，我需要减少这些工具的工作量，但是我也非常希望它们为了语言的利益而变得相当成熟。

## 示例 Web 应用目标

让我先解释一下我对这个示例应用程序的概念——它可能永远不会完成，但没关系，它给了我一些实践的东西。10 年前，当我们在当地 200 英里的范围内寻找拍摄照片的好地方时，我和一个朋友想到了这个主意。我的[500 像素的个人资料](https://500px.com/jculverhouse)充满了它们——几乎所有的风景，我不画肖像或模特。

我们的想法后来被称为 PinpointShooting.com(这还不是一个网站，咄)。摄影师可以免费注册，并使用谷歌地图界面指示最佳拍摄地点。有些可能是显而易见的，但也有一些隐藏的地方可以为你的摄影增添趣味。

<figure>[![Woman laying on stomach to take photograph, maybe my web application could give her some tips](img/33edf3607c5a45b951a58d8f7c97cc6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3CjpeH4G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/iphone-ground-camera-photographer-perspective-spring-601803-pxhere.com_.jpg%3Fresize%3D300%252C200%26ssl%3D1) 

<figcaption>她似乎玩得很开心！！</figcaption>

</figure>

小贴士:一张照片通常更有趣，因为它是从别人没有注意到的角度或隐蔽的地方拍摄的。这样，你的照片看起来就不太像“旅游明信片”这就是为什么摄影书籍告诉你要跪下、躺下或爬上东西来拍照。事实上，任何改变正常视角的事情。有什么比从上面 5′-6′看到一朵花的照片更无聊的？那就是我们一直看到的角度，随便走走！另外，这也是为什么摄影师经常称之为“制造镜头”而不是“拍摄镜头”按下快门以上是有艺术的！

## 回数据库聊天

不管怎样，说够了——我需要存储一些数据！！我确信这将会扩展和发展，但这是我目前为止对`tables`的想法:

*   **用户**–存储免费用户资料、密码、电子邮件地址
*   **位置**——存储**用户**分享的拍摄好地点的地址和/或经纬度
*   **照片**—**用户**将从**地点**拍摄的照片(或图片链接)存储到数据库中
*   **评分**——由去过**地点**的**用户**评定，如果同意
*   **喜欢**——上传或链接到**照片**的**用户**
*   **访问**——当他们分享一个好位置时，访问链接一个**用户/位置**的位置，或者当他们上传/链接一个**用户/照片**，或者当他们离开一个**用户/评级**

我从`Postgresql`中的一些`Enum`类型开始 DB 设计。这些`tables`中的大多数都将有一个**状态**字段，只有几个可能的选择。`enums`变成了`Diesel`似乎(还)没有处理好的事情。事实上，我因为那个事实找到了一个`crate`。等等…我收回那句话…我找到了 3 个`crates`。到目前为止，Diesel-derive-enum 下载量最大，所以我下载了它。然而，我只是玩了一小会儿就放弃了。这只是一个愚蠢的练习应用程序，所以我将切换到`char`字段。嗯，它们对我来说也不是马上有效，而且时间也不早了。我就用一些我已经有一吨的`varchars`。

<figure>[![A database was not the first thing to migrate, here are some birds headed south](img/5b92752ed61deb955b1b454ca8bde4d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mHoI1KBc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rust.graystorm.com/wp-content/uploads/2019/07/nature-bird-cloud-sky-sunrise-sunset-530867-pxhere.com_.jpg%3Fresize%3D512%252C337%26ssl%3D1) 

<figcaption>数据库不是首先要迁移的东西！</figcaption>

</figure>

## 柴油数据库迁移工具

Diesel 有一个数据库`migration`工具，允许您的项目记录(并存储在存储库中)数据库在产品生命周期中经历的所有变化。看起来工具名可能来自 Rails——或者只有这么多同义词可供选择。我**用过**(用头撞过) [Sqitch](https://sqitch.org/) ，我刚刚开始意识到 [Skeema](https://github.com/skeema) 。当您在一个快速移动的代码库中与 100 多个其他开发人员一起工作时，数据库模式管理变成了一个有点复杂和脆弱的流程。因为我的现场问题(也因为这个项目只有我一个人，而且刚刚开始)，我重置了我的数据库，重新开始了几次。最后，我成功地将上述数据库结构编译成代码。

顺便说一句，`Diesel`有一个令人印象深刻的[网站](https://diesel.rs/)向你介绍可用的功能。我在 Perl 中使用了 [DBIX::Class](https://metacpan.org/pod/DBIx::Class) 模块(通常称为 DBIC ),这是数据库查询所能变得最复杂的了！但这些大多是例外；DBIC 通常很好地将 SQL 抽象成一些面向对象代码，从数据库中获取我需要的数据。最重要的是，`Diesel` [入门指南](https://diesel.rs/guides/getting-started/)很好地介绍了如何使用这个板条箱。另外，除了普通的`crate` [API 风格的文档](https://docs.diesel.rs/diesel/index.html)，还有[几个其他的指南](https://diesel.rs/guides/)——一些深入到`Diesel`的机制和好处。

我真的需要进入一些代码来看看`Diesel`会怎么样，但是从我目前看到的来看，我有很高的期望。一个系统程序员(我不是)可能对我在过去三篇文章中使用的工具和附加组件的需求要少得多。然而，对于大规模的 web 应用程序开发来说，这些大部分都是关键需求！

## 更多来了…

我希望所有这些工具都随着`Rust`本身的步伐继续发展，我也希望能够与`Rust`一起变得足够好，这样我就可以在将来帮助开发一些模块！再一次，这里是我的代码的[库链接(当你阅读这篇文章时，它处于任何状态)。我希望很快能分享更多关于 PinpointShooting web 应用程序的内容。](https://github.com/jculverhouse/pinpoint_shooting)

编辑:与此同时，去看看这篇[精彩的文章](https://medium.com/@saschagrunert/a-web-application-completely-in-rust-6f6bdb6c4471)，这篇文章着眼于在 web 应用程序的前端和后端使用 Rust！Web Assembly 现在很热门，所以这是一个很好的读物！

帖子[没有数据库就不是 Web 应用](https://rust.graystorm.com/2019/07/15/its-not-a-web-application-without-a-database/)最早出现在[学锈](https://rust.graystorm.com)上。