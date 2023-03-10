# 为你的网站、应用或数码产品提供基本的搜索引擎优化

> 原文：<https://dev.to/browserlondon/basic-seo-for-your-website-app-or-digital-product-3ol2>

一旦我们向客户交付了一个数字产品，我们经常会被问及关于 SEO 和搜索可发现性的建议。严格来说，我们不提供任何关于搜索引擎优化的官方建议——毕竟，我们是一家数字开发机构，而不是营销机构——但在成功构建并推出了许多我们自己的 [SaaS 应用](https://www.browserlondon.com/case-study/twine/)，如 [Twine](https://www.twineapp.com/) 之后，我们已经了解了一些关于如何营销它们的简单、早期的注意事项。

## 不能衡量，就不能提高

没有数据就不可能做出明智的决定，所以建立系统来收集人们如何找到、移动和使用你的网站或网络应用程序的信息是关键。

### 谷歌分析

在网站分析方面，Google Analytics 实际上是行业标准，而且它还有免费的好处。如果你的网站或网络应用是基于 WordPress 或 Shopify 等知名平台，那么设置起来很可能就像在设置栏中输入你的账号一样简单。如果你的站点是定制的，那么设置会稍微复杂一些，因为你需要将代码直接添加到页面中。如果你不确定如何做到这一点，[谷歌的帮助页面](https://support.google.com/analytics/?hl=en#topic=3544906)相当不错。

[![An example of a google analytics screen shot on a computer](img/70f11ca5dde1807d89f4795a7bf127f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rUkhG1t8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Google-Analytics-screen-on-computer-1024x487.jpg)

或者，您可以使用 [Google Tag Manager](https://tagmanager.google.com/) (GTM)部署 GA，这是一个标签容器系统。这有一些长期的好处，例如更容易向您的网站添加第三方营销和跟踪标签，并允许您触发用户事件，但这是另一个需要学习的系统，所以如果您对这一切完全陌生，坚持使用基本的 GA 实现是没问题的。

### 谷歌搜索控制台

这是一个至关重要的(然而笨拙的)资源，可以帮助你监控你的谷歌搜索流量和性能，包括点击率和你在谷歌搜索结果页面上出现的位置。要设置[谷歌搜索控制台](https://search.google.com/search-console/about) (GSC)，你需要能够证明你拥有自己的域名，如果你已经设置了 GA 或 GTM，并且可以登录管理员帐户，这个过程可以很快完成。

[![A screen grab showing an example screen from Google Search Console](img/4eda6f095b65e90c85db93a26642ef8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iu-IE9DI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Google-search-console-screen-grab-1024x492.png)

### Yoast

如果你的网站运行在 WordPress 上，我们强烈推荐 [Yoast 的 SEO 插件](https://yoast.com/)。这提供了关于如何更好地优化搜索页面和博客帖子的实时反馈，并为您提供了一种简单的方法来编辑页面标题和元描述在搜索结果中的显示方式。反馈很直观，建议通常会链接到 Yoast 网站上的最佳实践文章，非常值得一读。

[![An example of the feedback from the Yoast WordPress plugin](img/5503dbf6f27ee619f2ac46cc34c2bfc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ft0uvaVH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Yoast-plugin-example-1-1024x550.png)

### 阿赫雷斯/ MOZ /塞姆拉什

随着时间的推移，当你掌握了基础知识，你可能会想挖掘更深一点的监控指标，比如反向链接(这对建立域名权威至关重要)，以及你对特定搜索词的排名。要做到这一点，我们建议使用专业的第三方工具，如 Ahrefs，MOZ 或 SEMRush(甚至可能是三者的组合)。

Ahrefs 无疑是最完整的 SEO 工具；它提供了关于搜索引擎如何看待你的(和竞争对手的)网站的名副其实的数据海啸。对于大多数人来说，这个平台完全是矫枉过正，但标题域名排名和反向链接数字是一个有用的方式来看看你的内容和搜索引擎优化策略的影响。有什么问题吗？没有任何种类的免费版本可用。然而，有一个 7 天 7 美元的试用期，我们的建议是注册，在取消之前从系统中挤出所有数据。

[![An example dashboard from the aHrefs platform](img/e74d546b77d491561e5bd54257f4c161.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xuiPD4Qd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/hrefs-screen-grab-1024x641.jpg)

另一方面，MOZ 支持免费账户，尽管你每月只能在他们有用的链接浏览器工具上查询十次，它会显示你的哪个页面产生了最多的反向链接。此外，MOZ 有一个方便的 Chrome 扩展，可以显示你所在网站的域名权限；有助于了解你在竞争对手域名中的排名。如果您注意到任何令人惊讶的差异，比如一个较小的竞争对手具有奇怪的高域权限，那么就值得考虑 Ahrefs 试验，以深入了解他们正在做什么来产生所有这些权限。

SEMRush 提供了一个类似的反向链接工具，但我们发现这个网站对它的位置跟踪报告特别有用(也是免费的)。使用它，你可以跟踪你选择的 10 个搜索词的搜索排名，帮助你直观地看到哪些关键词你正在取得进步，哪些你可能需要更多的努力(或内容)。

[![An animation showing the search position reporting screens in SemRush](img/3a02843f66d2c42cf1353b7daf16c7c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sBJlO3lQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Semrush-position-tracking-1024x555.gif)

### Hotjar

安装一个指针和点击跟踪平台将有助于你看到人们如何在你网站的各个页面中移动，并通常会填补你的网络分析可能留下的一些空白。这无论如何都不是至关重要的，但当我们需要它时，我们倾向于使用 [Hotjar](https://www.hotjar.com/?utm_campaign=UK-GSN-Branded&keyword=hotjar&matchtype=e&utm_content=old-4&geo=9045895&adgroupid=40151917662&utm_source=AdWords&utm_medium=ppc&gclid=Cj0KCQiA6JjgBRDbARIsANfu58G_37FkYSU9Zww-CeF5sm9q-YTLsEJeLgUgl_yFDrPLsS0y_K3WkWMaArStEALw_wcB) ，因为它的免费帐户相对慷慨。如果你安装了 GTM，这些第三方技术将会更容易添加到你的站点中。

[![A heatmap click report from Hotjar](img/864d8ea52fa02179313f061b58236475.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K8nMJEZQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Hotjar-screen-grab-1024x499.jpg)

## 创建并执行 SEO 策略

有了基本的跟踪结构，是时候根据你看到的数据建立一个策略了。不时地重新审视你的战略以保持它与你的商业目标一致是很重要的(每六个月是一个好的开始)。

### 定义成功

对你的特定网站或网络应用来说，成功是什么样的？你想让人们做什么？

预先定义这一点将有助于你以后对你的 SEO 策略做出明智的决定。你可能只是想最大化流量(比如说，一个新闻网站)，但大多数组织都有更深层次的目标，可能是客户注册、产品销售，或者让访客采取特定行动，比如下载报告、[阅读一些重要信息](https://www.browserlondon.com/blog/2016/08/15/improving-user-experience-uks-transport-watchdog/)或者[进行捐赠](https://www.browserlondon.com/blog/2016/09/02/our-user-experience-design-improved-performance-for-ba/)。

随着网站目的的确定，你需要一个健壮的方法来测量和跟踪它。这可能是你的 SEO 策略中最棘手的部分，因为在你想要测量的和你可以调用的度量之间并不总是完美的匹配。例如，如果你的目标是高访客参与度，你应该衡量每个会话的页面数，会话持续时间，每个用户的会话数，还是三者的结合？

我们能给出的最好建议是慢慢来，了解你的数据。只有当你花了几周或几个月的时间了解人们如何使用你的网站或应用程序时，你才开始看到最有意义的衡量模式和指标。例如，为了衡量这个网站的成功，我们开始每周记录它的 MOZ 域名权威得分。然而，几个月后，我们意识到分数没有变化，这意味着它对我们来说不是一个有用的指标。因此，我们开始记录和报告其他更有意义的指标。

### 目标&谷歌分析和 GTM 中的行动

如果你使用 GTM，考虑为关键的访问者动作设置一些触发事件。

完全涵盖这个庞大的主题超出了本文的范围(同样， [Google 的帮助页面](https://support.google.com/tagmanager/answer/7679316?hl=en)是了解更多信息的好资源)，但简单地说，GTM 允许创建自定义事件，记录用户在您的站点上采取的特定操作。这些通常超出了开箱即用的分析设置中可跟踪的内容，可以是从单击按钮或链接到提交表单或记录用户向下滚动页面多远的任何内容。这是一个非常灵活的系统，最重要的是，你可以将这些触发事件推入谷歌分析。

[![An example of setting up custom triggers in GTM](img/7620c59415d0de1f32cc1832a24735e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uafLm-CB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Example-GTM-trigger-setup-1024x486.png)

这为什么有用？好吧，触发事件深入到用户在你的网站上做什么的细节，帮助你更好地衡量你的目标进展。

例如，如果你的目标是让用户阅读你站点的特定页面，那么该页面的平均停留时间是一个合理的初始度量。但是，你如何区分实际阅读页面的人和打开标签从设备前走开的人呢？在这里，您可以引入一个[滚动深度触发事件](https://support.google.com/tagmanager/answer/7679218?hl=en)，当有人滚动到某个页面深度，比如说 75%时，触发该事件，并将其视为真正的“读取”。

### 关键词定位

大多数 web 属性都希望出现在尽可能多的相关搜索中。这包括识别人们在寻找像你这样的服务或产品时使用的关键词，然后根据这些关键词优化你的网站。再说一遍，这是一个很大的主题，所以我们在这里只触及基础。

根据我们的经验，有三个简单的步骤来创建一个基本的关键字定位策略。

#### 一——检查你的术语

第一是检查你谈论你的产品或服务的方式是否和你的潜在客户一样。例如，如果你的网站销售苏格兰的桶装陈年谷物酒，你应该称之为苏格兰威士忌、威士忌还是苏格兰威士忌？

[![An example of using google trends to see what people search for](img/383cbcf69b246b106285a1350996a7dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--287ksDBU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Google-trends-example-1024x484.png)

要找到答案，你可以使用 [Google Trends](https://trends.google.com/trends/?geo=GB) ，它会比较多达五个查询的搜索量。在大多数情况下，在其他条件相同的情况下，将你的网站优化到平均搜索量最高的关键词是有益的。继续我们的例子，这将意味着使用单词威士忌，而不是苏格兰威士忌。

#### 二——寻找任何快速的胜利

我们建议的第二步是使用 GSC 来识别快速赢得关键词的机会。[搜索结果页面上的点击率迅速下降](https://www.smartinsights.com/search-engine-optimisation-seo/seo-analytics/comparison-of-google-clickthrough-rates-by-position/)；很少有人能通过前三四个链接，更不用说第二页的结果了。因此，付出一点努力来提升自己越过这些门槛是值得的。

通过在 GSC 搜索结果页面上的平均位置对关键词进行排序，可以快速查看你的网站是否存在任何有用的机会。如果你找到了一个，修改页面上的副本以更好地针对该关键词，可以帮助你越过一个重要的阈值，例如第一页。如果你面临多个机会，加入总印象指标，优先考虑搜索量最大的词。

#### 三——探索长尾

最后，了解长尾关键词如何在你的关键词策略中发挥作用是有益的。

没有一家公司是在真空中运营的，所以很有可能你的竞争对手会向和你一样的搜索词优化。这就是为什么研究标题关键词下面的长尾关键词是一个好的实践。

举例来说，如果你卖婴儿食品，你会正确的尝试为关键字'婴儿食品'排名。然而，这一术语很可能竞争非常激烈，因此要为其获得高排名需要花费大量的时间和精力。相反，你最好也瞄准婴儿食品下面的一些长尾关键词，如“有机婴儿食品”、“婴儿食品食谱”、“婴儿麦片”或“婴儿零食”。

[![Using google keyword planner to find long tail keywords](img/a2fe1264d9b045cf92436b0bee57030a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KYjimK9j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Long-tail-keywords-1024x395.png)

谷歌有一个有用的[关键词规划器](https://ads.google.com/intl/en_uk/home/tools/keyword-planner/)工具，可以帮助你发现长尾关键词，并向你展示统计数据，比如每个词一个月有多少次搜索。这是值得做的，因为规划者有时会抛出有趣或新颖的关键字组合。(请注意，您需要设置一个 Google Ads 帐户来访问规划工具)。

### (关于 Google Trends vs Google Keyword Planner 的说明)

如果你同时使用 Trends 和 Keyword Planner，你可能会注意到这两个系统中的搜索数字有时会相互矛盾。

这里有一个例子，趋势显示搜索南方公园的人数是摩门经的五倍。另一方面，Keyword Planner 显示这两者的平均每月搜索量相同。怎么回事？

[![An example of the google trends platform](img/83c31dc7b517d73867acf44881785e28.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---c9HhuTz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Google-trends-1024x486.png)

[![A screen snippet from a Google Keyword Planner report](img/2412a830926e26cd58be517964fa00ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zX1qeBI9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://assets.browserlondon.com/app/uploads/2019/07/Google-keyword-tracker-300x75.png)

不同的是，关键字规划器只显示广告投放的搜索数据，而趋势显示所有的搜索量。所以，在这种情况下，数据告诉我们,“南方公园”是更受欢迎的搜索词，但“摩门经”是更有价值的搜索词，因为其搜索的更高部分会触发广告的出现。

在大多数情况下，当涉及到你的 SEO 策略时，你最好使用 Google Trends。

### 面向 SEO 的内容创作

一旦你有了目标关键词的想法，你就需要开始创建内容来做到这一点。每个人的做法都会略有不同，这取决于他们的产品或服务是什么，以及他们的网站采用什么样的格式。也许你会写博客页面、FAQ 页面、有针对性的登陆页面或操作方法文章。

无论你如何创建你的目标内容，记住这些简单的提示是值得的:

*   牢记你的总体目标。记住你希望人们在网站上做什么。
*   写出对读者有用的高质量内容。人们在网页上停留的时间越长，你就越有可能获得目标关键词的排名。
*   避免在你的文案中过度使用关键词。谷歌可以告诉你正在这样做(这被称为关键字填充)，并会惩罚你以不自然的方式写作。同义词是你的朋友。
*   在你网站的其他相关页面上添加内部链接，以鼓励用户浏览你的网站。
*   将你的文稿分成几个部分，给每个部分一个相关的标题。这有助于谷歌解析副本，并带来更好的阅读体验。
*   确保所有图像都有好的描述性替代文本。
*   试着将你的关键词放入页面或文章标题中。
*   如果你的内容与一些可变的东西有关，保持它的更新(例如，餐馆网站上的菜单页)。搜索引擎会优先搜索最近更新过的页面。

如果你在你的网站上使用 SEO 插件，比如 Yoast，它很有可能会对你在以上几个方面的表现进行评分，并告诉你哪里可以做得更好。如果你不能每次都做到这几点，也不用担心。一旦内容发布后，完全有可能返回并继续调整和润色它。

### 总结

正如我希望已经变得很清楚，没有单一的搜索引擎优化策略，以适应所有的网站和网络平台。相反，您的方法将取决于您的数字产品的目标、您的经验水平，以及您在跟踪和改进我上面提到的数字指标方面投入的资源。

此外，重要的是进入搜索引擎优化过程，知道结果不会在一夜之间发生。建立一个搜索网站需要时间、耐心和毅力，尤其是如果你是这个市场的新手。令人高兴的是，反之亦然；一旦你建立了搜索词和高质量内容的基础，只要内容对你的用户保持相关和有用，它就会日复一日地可靠地为你服务。

* * *

为你的网站、应用程序或数字产品提供基本搜索引擎优化的帖子首先出现在伦敦的 T2 浏览器上。