# 用超光速粒子和功能性 CSS 在 10 天内完全重写:一个案例研究(第三部分)

> 原文：<https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-3-1k4>

### 这是一个 4 篇文章系列的第三章，带你经历一个使用超光速粒子的网站重构过程。是时候开始重写了！

### 菜单上的

1.  [当初为什么要重写？](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-1-3dkm)
2.  什么是函数式 CSS？
3.  **重构过程，一步一步**(你来了！👋)
4.  [后果:主要经验和建议](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-4-4b11)

* * *

## 第三章:重构过程，循序渐进。

好的。我们终于到了。

嗯，差不多了。让我从搭建舞台开始。

我们离圣诞节还有几天。每个人都去度假了。我一直要求有两周的时间来做一些重大的重构，看起来圣诞和新年假期是我完成它的“唯一机会”。

当办公室(和 BAU)处于关机模式时，我有机会*进入状态*，获得一些隔离并专注于一项任务。

在这一点上，我并不清楚休息期间我能重构多少。我对超光速粒子做了足够的研究和推理，知道它可以解决我们的技术债务问题。我只是不知道从我们目前的 CSS 到新系统的过渡会有多复杂。需要多长时间。

## 一个非常乏味的假期

我带着笔记本电脑坐在车里。

我妻子开车带着我们和两个年幼的孩子去 Mudgee，一个离悉尼内陆 5 小时路程的乡村小镇，在那里我们开始了为期 6 周的穿越新南威尔士的露营之旅。

你说得对。我将在旅途中，在一些很少或没有互联网的地区进行我的整个重构过程。

听起来像个计划！

[![Camping setup (tent and car in the nature) in Newnes in the Blue Mountains, my remote office for a few days](img/0972932e9f2d9e78fe97cf2c8db1cec5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kuOsx1Y6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jl4d01oncdudutq65mqh.jpg)

<figcaption>My "on the road" office.</figcaption>

跑题了，但这让我想起了那条有争议的推文。当我在一次重要的圣诞节重构会议上读到它时，我咯咯地笑了:

> ![Joe McCann profile image](img/fe98fb4fb8c00ce52b1af5bb2c55c5ad.png)乔麦肯@ joemccann![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)我所知道的最好的软件开发人员总是在假期里进行黑客攻击。
> 
> 真实故事。2016 年 12 月 24 日下午 18:50[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=812732099027419139)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=812732099027419139)131[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=812732099027419139)391

我当然不会假装自己是最好的开发者。乔·麦肯不认识我。但是我确信在假期里我会一直黑下去！😂

* * *

我们开始吧。

我将按照时间顺序，把这个过程分成我经历过的几个步骤或里程碑。

## 起点

我们的“遗留”CSS 由[基础](https://foundation.zurb.com/)框架和大量定制的 BEM 模块组成。

我们的网站运行在漂亮的 [CraftCMS](https://craftcms.com/) 上，它使用[树枝](https://twig.symfony.com/)作为模板引擎。稍后将会详细介绍为什么使用模板语言很重要。

开始是一件棘手的事情。该网站需要在办公室重新开放时运行，否则 BAU 将再次接管，我的超光速粒子 GIT 分支将永远见不到光。

我如何在不将网站置于“拆除网站”模式的情况下逐步实现超光速粒子？

## 第一步:超光速粒子为“糖顶”

在 tachyons slack 中进行了一些思考和讨论之后，我决定简单地在样式表的最末尾加载 tachyons。

这让我可以使用一些超光速粒子辅助类来填充、着色等，而不会有破坏其他东西的风险。

如果我不使用助手类，**什么都不会发生**。感觉很安全。我能感觉到超光速粒子而不需要冒险。

工作不到一小时，我就被震撼了。我能够**移除大量遗留的 CSS** 并用简单的超光速粒子类取而代之。移除 CSS 是 web 开发中最难做的事情之一，我正在以令人难以置信的速度前进。这真是了不起。我在咯咯笑。

* * *

我试图向我没有发育障碍的妻子解释这一点——她仍在开车——但她真的不明白为什么这是一件大事。😂

## 太容易了，感觉都快成笑话了。

我取任何给定的边界元模的部分。我找到了这个模块的 HTML 模板，然后用超光速粒子重建它。

我注释掉了整个自定义 CSS 片段，然后继续下一个。它仍然有效。这太荒谬了。

我期望只是替换一些空白，填充，字体大小和重量，但我在这里，写完全由超光速粒子驱动的组件。轻而易举。

## 趁热打铁。

在某些时候，我注意到我的进度最终会慢下来。

我开始注意到越来越多的地方我的超光速粒子课程不起作用。按钮有意外的悬停状态，间距有点偏离。

我意识到我正试图对抗一些基础类，它们比超光速粒子更具特异性，因此赢得了胜利。

如果我试图注释掉 Foundation，我可以看到我的一些漂亮的超光速粒子模块看起来很奇怪。他们从基础上继承了一些风格，没有它们看起来就不一样了。

学到的第一课。如果你计划在某个时候移除底层的 CSS 框架——这是我的计划——不要等太久。

这让我想到了第二步。

## 第二步:全力以赴

第一周还很早。我有充足的时间。我决定创建一个名为`kill-foundation`的新分支，在这个分支中我注释掉所有内容。

我想看看当我去掉所有遗留的 CSS 时，事情会是什么样子。

但是首先，让我说:一边露营一边工作，看着你的家人享受假期，是一件美好的事情。如果有机会的话，我强烈推荐你尝试一下。

> ![unknown tweet media content](img/8209e2b91770f6077594bf41a61200df.png)![Simon Vrachliotis 🏄🏀💻😍 profile image](img/8a3264c0e1a98809b05071c57bb23da5.png)🏄🏀💻😍@ simonswiss![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)上班三个小时后，我 4 岁的儿子从海滩上给我带来了一块饼干、一个苹果和三个贝壳。😍🙌[#远程工作](https://twitter.com/hashtag/remotework)[#露营](https://twitter.com/hashtag/camping)[#小孩](https://twitter.com/hashtag/kids)2016 年 12 月 27 日上午 00:13[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=813538351160565760)2[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=813538351160565760)14

* * *

好吧，回到我们的重构。

所以，我继续前进，注释掉所有 CSS 部分，除了超光速粒子。

我看着网站爆炸。

> “嘿，其实也没那么糟！”

当然，网站坏得一塌糊涂。你期望什么，我刚刚删除了近 200kb 的自定义 CSS。

但是，也不是*说*不好。讽刺的是，某些部分的排版和垂直节奏感觉更一致。这告诉我一些关于到处都是技术债务和自定义 CSS 声明的事情。😂

## 网格布局最明显

我决定修复的第一件事——因为这是目前最明显的——是我们依赖 Foundation 专栏的所有地方。

虽然超光速粒子能够以多种不同的方式完美地处理网格，但我决定使用另一个名为 [Flexboxgrid](http://flexboxgrid.com/) 的库。

Flexboxgrid 只做一件事:它为您提供了一个超级通用的网格系统...flexbox(谁能猜到！).如果你熟悉 bootstrap 的课程，你会喜欢它的。

Flexboxgrid 占地面积小，使得重新构建网格布局变得轻而易举。在我的书中，并考虑到我的设计师同事，它绝对值得在构建中增加 14kb(非 g-zipped)。

## 一次一个模块。

一旦网格布局回到原位，我感到非常有信心。我查看了网站的各个部分,*知道*如何用超光速粒子建造它们。

所以我开始工作，从主页开始。

我在几个小时内就完成了主页。繁荣💥。

[![Snippet of slack discussion where I get super excited about this utility-first CSS approach](img/5e248328dae7b540c097be5fdf8617a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1NVT8UAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3f1ppglyulg51d9h7y8z.png)

<figcaption>Getting excited in slack</figcaption>

* * *

如果我仍然怀疑我能否在假期完成整个重建，我现在非常有信心当我回到办公室的时候，网站会准备好。

* * *

[![Snippet of slack discussion](img/606b48d8170af47e6021679a7eb558b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jk6h_Ln---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z97ydevicyutwokkgtsh.png)

<figcaption>Confidence is rapidly growing</figcaption>

* * *

我仍然不能理解一旦你熟练掌握了超光速粒子，构建模块会变得多快。太令人兴奋了。你会有一种不可思议的感觉，随着你的前进，没有什么比这更复杂了。

这一切都变得可预测，这是一种非常强大的感觉。

* * *

## 然后，有一天..

2017 年 1 月 4 日，**圣诞节后 10 天**，我将新版本推送到我们的预生产环境。

[![Snippet of slack discussion showing when I pushed the new site to pre-prod](img/ac1a2ab459978d386fc3701c634197cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fQ99BGBL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gxj9sltiumndc7j0mpd6.png)

<figcaption>I did it! With a fair bit of extra time left, too.</figcaption>

* * *

## 判决结果

在第四章，也是最后一章，我们将更深入地探讨超光速粒子特有的知识和建议。

现在，让我们做一些一般性的观察。

## 1。不是 100%的设计匹配

网站的新版本**与旧网站**并不是 100%匹配。它也从未假装如此。

重建的主要目的是摆脱技术债务。我试图使网站看起来一样，同时试图标准化一些组件，以更一致的整体外观和感觉。

在一些地方，网站没有以前那么好了。但是很多地方，**更好**。

总的来说，它肯定需要润色。虽然我有设计的眼光，但我和 SocietyOne 自己的设计总监(也是密友)马特·巴伦先生(Mr [Matt Barron](https://twitter.com/m4ttbx) 的技能相去甚远。

*编辑:有趣的是，马特和我现在又是同事了，在[think mill](https://thinkmill.com.au)T3】🙌*

* * *

## 2。明显更少的错误

整个事情感觉稳定多了。我怎么强调这一点都不为过。

不可变的、单一用途的函数式 CSS 类意味着，如果你把
事情做对了，就不会有[意外覆盖](https://giphy.com/gifs/WM3HX2cZ3zTry)的风险。

* * *

## 3。团队成员入职很容易

人们对超光速粒子的第一个假设是，其他人不可能研究你的代码，除非他们学会了超光速粒子。

嗯，那倒是真的。对于任何代码库来说都是如此。认为你可以进入任何代码库，并且**知道**该做什么，而不需要学习系统..妄想。

我**强烈反对**这个论点。而我是瑞士人——我们大部分时间倾向于保持中立。

虽然我承认模板一开始看起来令人困惑，但是一旦你熟悉了超光速粒子语法，你就会从一个完全不同的角度看待事物。

记住超光速粒子课程并不需要很长时间。相信我，你会花**更长的时间**来理解我们之前的所有定制 BEM 模块。

* * *

## 4。移除上下文切换让你如此快速

当使用函数式 CSS 时，HTML 具有很强的声明性和自足性。它确切地告诉你事情将会是什么样子，而不必查找 CSS 样式表来弄清楚。

能够在一个文件中工作而不需要切换你大脑中的上下文**会产生不可思议的生产力增益**。

你会从 React 或 Vue 开发者那里听到类似的评论。他们喜欢在一个自包含的组件文件中工作。他们称之为真正的关注点分离，而不是语言分离。

* * *

## 5。CSS 包的大幅缩减

完全不用担心性能，当第一次推送到`pre-prod`时，CSS 包原来是**小于原来包大小**的 50%。

> ![unknown tweet media content](img/6c880579ad64ef577564786a76f81b61.png)![Simon Vrachliotis 🏄🏀💻😍 profile image](img/8a3264c0e1a98809b05071c57bb23da5.png)🏄🏀💻😍@ simonswiss![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)两周前，我开始用 [@tachyons_css](https://twitter.com/tachyons_css) 重构 [@mysocietyone](https://twitter.com/mysocietyone) 网站，剔除 css 技术债。还没有完成，但伟大的统计！🎉00:45AM-04 2017 年 1 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=816445330065457152)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=816445330065457152)3[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=816445330065457152)12

* * *

## 6。认知负荷低得离谱

我觉得现在没有 BAU 能阻止我们了。

> 这么说吧——超光速粒子作为 BAU 的一个工具包，让你可以做一些像内联风格一样简单的事情，但是它们对你代码库的其他部分的影响是完全不同的。

我相信你们都有过这样的经历:一个利益相关者(或者客户)想要把网站的一部分移到页面更高的位置。

对某些人来说，这听起来像是“微小的改变”。但是，作为一名设计师/开发人员，您会看到它可能带来的一些问题。

也许这个部分有绿色背景。你被要求把它移到另一个绿色背景部分的正下方。在那里根本不好用。间距现在完全消失了，边距顶部需要消失。

在这种情况下，很容易就想放弃像`<div style="margin-top: 0;">`这样的内嵌风格，就此结束。

函数式 CSS 有点类似于(但完全不同，尽管这一点经常被误解)内联样式。从这个意义上说，你可以用`<div class="mt0">`对你的部分应用同样的效果，而没有应用内联样式的任何负面影响。

如果你仍然需要一些移动设备上的页边距，但不是在更大的屏幕上，你可以做`<div class="mt2 mt0-ns">`。

祝你在* inline-*风格上好运。

## 7。功能性 CSS 强制要求简洁和模块化

我听到很多人声称函数式 CSS 是给懒人用的，或者是给那些对 CSS 了解不够的人用的。

我完全不同意。

在我看来，函数式 CSS 迫使你在编写 HTML 时考虑“模块化”。没有人喜欢在一个 HTML 文档中一遍又一遍地复制和粘贴相同的类。

为了避免这种情况，您需要加快您的“架构”游戏，并想办法尽可能地使您的 HTML 组件可重用。

像在数据数组中存储内容、编写循环来输出 HTML 的重复块这样的事情对维护代码有很大的影响。

超光速粒子无疑让我更深入地研究模块化模板技术。网络从各个角度指向**网络组件**。抱着这种心态很好。

> 从你的标记中提取你的内容，使它尽可能的后端不可知，这是一件好事。这会让你成为更好的开发者。功能性 CSS 让你思考组件和系统。不是页面。

我刚才听起来像布拉德·弗罗斯特吗？

* * *

## 那又怎样，功能性 CSS 没有弊端？

函数式 CSS 可能并不完美。这肯定不是每个人都喜欢的。

最初对难看的 HTML、*语义类的损失的负面反应很常见，因为它们是真实的事实。*

如果你想一想，评估一下利弊，你就能超越这些反应。

作为一名 web 开发人员**要做的就是评估取舍**。你需要从一个系统或方法中获得多少利益来克服感知到的负面影响？

如果我必须用一段话来总结我使用函数式 CSS 的经验，那就是:

> “我从使用功能性 CSS 中获得的好处，在减少认知负荷、自我描述的声明性 HTML 标记和巨大的生产力提升方面，简直是惊人的。到目前为止，他们克服了这种方法带来的一些负面影响。我会在一周中的任何一天进行权衡。”

我做到了。

在[最后一章](https://dev.to/simonswiss/full-re-write-in-10-days-with-tachyons-and-functional-css-a-case-study-part-4-4b11)再见，关于一些超光速粒子的观察、建议和经验教训！