# 2019 年以开发者的心态开博客

> 原文：<https://dev.to/abhishekcghosh/starting-a-blog-with-a-developer-mindset-in-2019-114m>

在之前的一篇文章中，我谈到了我创建这个博客的动机。

但是创建和维护博客是一项艰巨的工作。首先，你必须决定如何命名它，然后想出如何设置和托管它，什么样的内容管理系统适合你，以及它们的功能和价格，博客有没有主题——你是喜欢坚持某个特定的主题还是更开放——一些赋予博客“个性”或“身份”的东西。所有这些都是在你有机会认真考虑如何继续定期发表文章之前——为你的博客创建真正的内容——这可能是最困难的部分！

当我大胆尝试时，我知道我想专注于一个“开发者博客”，所以至少我知道我要追求的主题。我认为这是一个很好的起点。

很快，犹豫开始了。

我应该去找一些托管平台解决方案，还是有一些我可以称之为自己的东西？我是部署现成的现成框架，还是从头开始构建？或者大概是介于和*之间的什么东西？我可以用什么样的*可信*和*实惠*服务提供商来托管我的内容？我应该从多少钱开始作为这个项目的*支出预算*？我会被锁定在某个服务提供商或技术选择上来存储我的内容吗？如果将来我想将我的内容移动到其他地方，会有多难？我怎样才能为这个博客找到真正的读者呢？*

看着可供选择的方案和要做出的选择，我完全有权利感到眩晕。

一步步来。

最终，我决定走上这条路，建立自己的域名，创建自己的博客，作为一个静态网站，使用[盖茨比](https://www.gatsbyjs.org/)生成，托管在 [Netlify](https://netlify.com) 上。

在我的第一篇博文中，我的确添加了一句轻松的话，围绕着“这些天所有酷孩子都在用盖茨比”。谁不想变酷呢？公平地说，我选择这条路的决定是经过深思熟虑的，而不仅仅是为了炒作。到目前为止，这似乎是一个不错的决定。

现在我不打算让这篇文章成为“又一篇关于如何与盖茨比和 Netlify 一起建立博客的文章”。还有一个[大的](https://dev.to/dceddia/start-a-blog-in-2019-with-gatsbyjs-and-netlify-3g92-temp-slug-9107789)、[多的](https://codeburst.io/build-a-blog-using-gatsby-js-react-8561bfe8fc91)、[资源](https://www.netlify.com/blog/2016/02/24/a-step-by-step-guide-gatsby-on-netlify/)、[全部](https://www.freecodecamp.org/news/how-i-built-my-blog-using-gatsby-and-netlify-f921f1a9f33c/)、[超过了](https://blog.logrocket.com/gatsby-netlify-cms-a-perfect-pairing-d50d59d16f67/)、[、](https://dev.to/saigowthamr/build-a-blog-using-gatsby-netlify-cms-adi)、[网](https://egghead.io/courses/build-a-blog-with-react-and-markdown-using-gatsby)就已经说明了！

相反，我将关注的是分享我对*做出那个决定*的理由。

我敢肯定，有很多人正在走上建立自己博客的道路，并且在这个过程中经历了一些和我一样的困境。如果可以的话，这是一次尝试。

*⚠️下面的内容是定制和固执己见的。*

以下是我今天要谈论的对我来说很重要的事情。

*   拥有自己的内容，而不是任由别人摆布，我可能会再次付钱让别人提供我创作的内容。
*   建立身份和个性的创造性自由和可定制性。
*   在同一屋檐下做更多事情的技术自由。
*   静态网站的速度，节俭和敏捷。可能的环境友好撒在上面。
*   内容的透明度、可移植性和版本控制
*   潜意识开发者体验
*   学习新事物并应用它们的借口
*   但是为什么是盖茨比呢？为什么是盖茨比？
*   那么，是不是都是阳光和彩虹？

#### 拥有自己的内容，而不是任由别人摆布，我可能会再次付钱让别人为我创作的内容服务。

我更喜欢完全拥有我创作的内容，由我自行发布，不受某些第三方托管平台不断变化的政策和潜在审查的影响。这就是为什么我没有选择托管解决方案，如[中型](https://medium.com)甚至【Wordpress.com】T2。

更不用说相关服务的使用定价模式了:虽然它们都是免费的，但通常的情况是，当你变得更大，或者希望更加可定制或功能更丰富时，通常会对各种辅助服务收费。考虑到他们可能提供的所有有用的服务，我并不是说这是不合理的，而是我觉得对我来说不值得。至少不会作为*规范*的地方来放我的内容。

#### 创造的自由和可定制性来建立身份和个性。

我不喜欢托管解决方案的另一个原因很简单，那就是它们实际上都向在它们平台上注册的每个人提供了一成不变的普通体验。事物的标准化有很多优点。作为一个靠为网络构建东西为生的人，你可能会理解我的观点在标准化事物的背景下所处的位置和原因，但在这种情况下，我显然会在创作自由的命令下选择离开。

博客、作品集(或者两者都有？)我放在那里的应该反映我觉得定义我的东西，是独一无二的，或者只是我希望我的观众体验的方式。我应该能够设计一个具有我想要的外观和功能集的用户体验，不受我正在使用的某个服务或平台的限制。

作为一名开发人员，能够自由支配对我来说至关重要。我可以投入一些时间和精力，让事情完全按照我的意愿来建造。今天或明天不能这样做不是我愿意讨价还价的筹码。

一些像 Wordpress 这样的平台确实为你提供了相当程度的灵活性，但是仍然有很多意见，并且经常达不到你想要达到的目标，要么以实际限制结束，要么简单地变得过于昂贵，使你失去兴趣并放弃。其他一些平台就更差了。忘了定制太多吧，Medium for one 已经停止提供一些基本的功能来建立你的身份，例如将定制域名与你的博客相关联。他们曾经在某个时候以很高的价格这样做，但现在已经完全停止了。我有我个人的推测(一把枪指着你的头，你的未来会不会轻易离开他们的平台？)，不过那是改天的话题了。

#### 同一屋檐下做更多的技术自由。

继续上述内容，它不仅仅局限于博客方面。我可以想象未来我可能想要创建自定义内容——比如我想要建立、讨论和分享的实验、项目或实验室。能够在同一个屋檐下托管和服务所有这些将是非常棒的。

对此有一些反对意见。理论上，我仍然可以在一些选定的软件平台上托管我的博客，把它放在一个域的后面，并通过子域等方式连接其他地方托管的其他增强功能……甚至是一些假设的幕后重写，就像声音一样不太可能。我想这个决定最好推迟。现在，我能做的就是尽可能少的假设，尽可能多的打开潜在的途径。为[奥卡姆剃刀干杯！](https://en.wikipedia.org/wiki/Occam%27s_razor)

#### 为速度、节俭、敏捷而静态搭建的网站。可能的环境友好撒在上面。

这是一个现在流行的思想流派，在我看来有很多好的理由。目前，我已经被 [JAMStack](https://jamstack.org/) 背后的一般哲学所收买。作为一名前端工程师，我不得不承认，它已经毫不费力地接近了我的心。我喜欢它如何自然地将`data`和`view`的分离融入其核心架构原则，然后使无数用例(从最简单到足够复杂的 web 应用程序)能够廉价托管、有效扩展并快速修改以适应不断变化的需求，这就是 2010 年代一代“静态网站”的定义。是的，这涉及到大量的客户端渲染，但我们会尽量不去考虑性能方面的问题。

令人惊讶的是，博客的用例很好地融入了这种范式。典型博客的实际内容主要是手工制作的，而不是动态生成的。从一个读者访问你的博客到另一个读者访问你的博客，这是不会突然改变的。与内容一起呈现的页面也是如此，这些页面可以在*构建时*而不是运行时制作得相当好。

在运行时，肯定有一些可以想象的事情可以给你的内容带来优势(比如围绕内容排名或动态计算的“本周热门帖子”的更复杂的功能或优化)，但大多数都是个人博客的二阶或三阶功能，而不是核心体验，如果在动态生成和完全静态构建之间存在一些最佳点， 与每次请求页面时都需要进行运行时计算相比，它更倾向于在受控环境中通过间歇性的构建时计算来实现。

避免在运行时做设计时能做的事情。考虑到这一点，在需要计算能力来提供主要非动态内容的服务器上引入额外的依赖，并因此消耗一些能源，这没有太大意义。同样，通过增加流量来扩展这些服务器的计算能力。更多的服务器，更多的能源，更多的钱。对于很多明显的场景来说，这可能看起来没什么大不了的，但对我来说这感觉很浪费。

然而，有了预建的静态组件，与必须无声地提供一些静态文件而没有任何复杂的计算需求相比，这变得相当微不足道，然后可以由廉价、分布式和易于扩展的[cdn](https://en.wikipedia.org/wiki/Content_delivery_network)来完成。实际上，您可以消除在服务器上反复计算所需的时间和精力，因此甚至不需要调试这些服务器，同时，您可以更容易地获得这些静态内容，并更接近您的受众，这些内容可以随时缓存并在地理上分发。

*这两个*因素都将通过使你的博客页面加载*更快*来改善你的用户体验。

这些 cdn 不需要为服务器提供定制需求和计算需求，非常便宜，并且可以通过在不同的存在点复制自己来轻松扩展，这使它们能够更快地向您的受众提供您的内容。想象一下，如果您意识到需要在所有主要地理区域部署计算内容的定制服务器，您将不得不经历的痛苦！

一个恰当的例子是，Netlify 提供的服务实际上是免费的，用于托管静态网站，直到每个月有足够大的流量。如果我的博客超出了这一消费，我会把它当作一个快乐的问题，并乐意为额外的金额付费！除此之外，它还提供了一系列我喜欢的[其他好东西](https://medium.com/netlify/10-netlify-features-to-surprise-and-delight-225e846b7b21)，我很确定你也会喜欢。不是附属推广。只是一个快乐的顾客。

就安全性而言，数据隔离不是我在这里想要的。这是由设计决定的，博客和它的内容通常是公开的，就像我一样。然而，数据完整性肯定是重要的，如果选择 CDN 服务提供商，我们可以解决这个问题，并确保我们正在做一些基本的事情，例如从 CDN [边缘服务器](https://en.wikipedia.org/wiki/Content_delivery_network#Technology)通过 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 提供内容。在这一点上，这些都是非常基本的，并且通常由这些服务提供商提供开箱即用的支持。对于安全性的更细致的方面，很遗憾这与本文无关。事实上，你现在可能不会在我身上寻找安全专家。所以我不会在这方面离题太远。

#### 内容的透明性、可移植性和版本控制

想象一下，拥有一个博客，里面有大量的文章、想法和帖子，这些都是你用血汗和泪水积累起来的。现在想象一下，由于你的博客托管服务提供商的数据库损坏，这一切都烟消云散了。它让我起鸡皮疙瘩。

主机服务提供商通常会确保很好地托管您的内容。但是坏事发生了。系统失灵。虽然系统可用性严重丧失的场景在上下文中可能看起来有点做作，但考虑到任何善意和明智的托管服务提供商都打算(并希望)确保数据冗余，最终这是一个信任度和感知透明度的问题。

当然，整个该死的世界都在云上运行。但可以说，并非每一朵云彩都发出同样的雷声。我的目标不是要多疑，但我更希望避免为此失眠。我不可能预先知道某个 xyz-affordable-hosting-dot-com 可能有多好，或者更恰当地说，它将始终为我提供的服务质量。

因此，与其走雇佣一些服务提供商的路线，他们旋转服务器并在一些托管数据库中出租一些存储空间，在那里我很难容易地跟踪我创建并不断变化的所有内容；或者甚至不知道将所有这些内容移入和移出该系统会有多复杂，我倾向于保持简单，并坚持使用我已经相信不会使我以后的生活变得更困难的机制。

对于一个博客来说，它也主要是某种形式的基于文本的序列化文档，可以很容易地直接存储为文件，而不是依赖于一些数据库服务支持的系统来存储我的内容，然后可能需要一些额外的过程(和[SLA](https://en.wikipedia.org/wiki/Service-level_agreement))来推、拉、转换或迁移数据，我会坚持一种更简单的方法，即我创建的内容直接以我创建它们的形式保留。作为文件。在某个仓库里。同样，我习惯于存储和维护非常可靠的源代码，这也是我用鲜血、汗水和泪水写的。这也让我获得了一个定义良好的、健壮的、经过战斗考验的*版本控制*系统的所有好处。

我说的是在 [Markdown](https://en.wikipedia.org/wiki/Markdown) 中创作并在 [Github](https://github.com/) (或者 [GitLab](https://about.gitlab.com/) 或 [BitBucket](https://bitbucket.org/) 中维护它们。

我知道我不必在这里推销减价商品。在*开发者城*，这是一种被普遍接受的创作文档的格式。因此，如果我想将我的所有内容从一个系统转移到另一个系统，我可能不会担心潜在的迁移问题。如果将来我的目的地系统需要理解 Markdown 之外的东西，仍然有可能在某个地方有一些 transpiler 可用，如果这个所谓的目的地系统足够流行的话。

这种方法真正伟大的地方在于，我创建的内容对我来说是显而易见的，它是如何存储和转换的。很简单。它们是文件。我可以点击一个简单的文件副本，随心所欲地多次复制它们，随时随地轻松备份它们(可能与 git repo 一起保存版本及其历史)。鉴于此，我失去这一切的可能性非常非常小。这确实伴随着对 Github 的极大信任。这是公平的。也可以接受。而且肯定比 xyz-affordable-hosting-dot-com 好得多。

#### 潜意识开发者体验

继续上面的主题，这是我冒险激怒一些关注点分离的伟大支持者的地方，他们也相信所有的事情都应该是非黑即白。

对于作为开发人员的我来说，维护我的博客和维护任何其他软件项目没有太大的区别。其实，*都是*。

[![What if I told you, it's okay](img/a28baedb37330d0ef8caefbabb0339d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tq3x3GcI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/myZWPss/meme-morpheus-1.jpg)

在开发者生态系统中，我们历史上构建了很棒的系统和工具，以确保事情按照它们应该的方式工作，并且构建和维护这些东西是一种很好的体验。

因此，将这些好处传授给创建和维护内容的人自然会感觉很棒。

这是否意味着我们将所有代码和内容交织在一起存储？最初将代码和内容“耦合在一起”的想法听起来令人反感。抽象、模块化、单一责任、关注点分离在哪里？哦，还有软件非通用化的风险？异端！

但是牺牲这些品质并不是我们现在要做的。我只是建议为创建和维护个人博客(甚至是由一小群人管理的博客)的特定用例提供一个稍微微妙一点的机制。

我知道一个典型的个人博客不会过分夸张，也不会过于一般化和可能过度设计。所以我会从一个简单的设置开始，我可以很容易地维护它。从一个包含所有内容的存储库开始非常好。事实上，拥有这一切让我惊喜地发现了一些意想不到的优势！

这是一个单一的可移植设置，我不必在多个工作场所、编辑器实例或软件工具之间跑来跑去，它们本质上是为同一件事情做贡献的。

我可以使用相同的工具链来构造我的内容，我用它来构造我的代码。写内容感觉像写代码。当我写一篇新文章时，我得到了将版本控制的提交推送到 Github 存储库的好处，这是一个钩子，当这个提交被推送到 Netlify 时，它会自动启动一个构建，然后通过[连续部署](https://www.netlify.com/docs/continuous-deployment/)将其部署到托管我的网站的 CDN。这是一个简单的工作流程，可预测且没有任何麻烦。这让我很开心。

最棒的是，当我创建内容并看到它的运行时，我可以获得对代码组件的即时反馈，这些代码组件是我为渲染这个博客的页面而构建的，如果我需要调整某些内容，只需修改我在代码编辑器的另一个选项卡中打开的文件中的代码，这是一件相当简单的事情。我是我自己的主要客户，这是软件开发人员在开发产品时遇到的最好的事情之一。我称之为*“反馈的火线”*(是的，我现在才想到的)。

重要的是要意识到建立一个博客——包括内容和发布它的软件——不是一个接一个的过程。它们是一前一后发生的。你第一次建立博客的那一天；从今以后的每一天。因此，只要规模允许，并且您的源代码被设计为在以后某个时间点需要分离时不会使分离复杂化，那么现在将它们放在一起是非常好的。

从表面上看，这可能听起来不合常规，但并不是所有的传统智慧都是正确的。当 [React](https://reactjs.org/) 出现并表示最好将 HTML、CSS 和 JS 代码一起放在一个组件的单个文件中时，我很想将这种反弹与美国“分离主义者”可能帮助促成的反弹相提并论。但我不会。有点太极端了。不太相似。但是，嘿，我们都知道那是怎么回事。如果是为了正确的理由而做，那就是正确的。我想看待这个问题的方式是随着时间的推移重新评估关注点是否真的如此独立。

#### 学习新事物并加以应用的借口

至此我已经谈到了*的含义*和*的动机*。让我们也来谈谈整个等式中的一个*机会*。

这也许是最简单的推理。

对于热衷于尝试新事物的开发人员来说，建立自己的博客、构建功能并能够根据自己的喜好对其进行定制的想法确实很有吸引力。

当然，无论如何，总是有可能着手研究新的东西，但以我的经验来看，这种努力往往以暂时和孤立的努力告终，如果没有构建、维护或扩展某个东西的结构化动机，就不会真正带来任何持续的兴奋或有意义的学习体验。

当您有一个实际的目标用例时，它会工作得更好。那么，为什么要失去利用这一点的大好机会呢？

伟大的软件建立在良好执行的抽象原则之上。我不喜欢我不理解的抽象概念。我后来确实喜欢它们，但只是在我破门而入，解剖它们，然后再把它们放回原处之后的 T2。我喜欢和原始人一起工作。我想充分了解我腰带上的工具是如何工作的。这并不是说我更喜欢在使用运动定律来解决问题之前重新发明它们，但我只是那些不能长时间远离弄脏自己的手摆弄本质的人之一。这个习惯不一定是你的朋友。有时候，它只是喜欢表现为一种强迫症。

所以我只是利用这一点来学习一个新概念或者只是一种看待问题的新方法。或者一个框架、工具或者语言。摆弄它们。将它们应用到现实世界的问题中。

它丰富了你。

#### 但为什么是盖茨比呢？为什么是盖茨比？

> "我实际上并没有恋爱，但我感到了一种温柔的好奇心。"
> 
> <cite>— F. Scott Fitzgerald, The Great Gatsby</cite>

走吧。我不能错过这样的机会。

但是把所有的闪光和戏剧性放在一边，它真的归结为一个简单的事实。对我来说，盖茨比似乎非常符合要求。

我想要一个静态的站点生成器，但是要用一种我暂时能适应的语言或工具链。盖茨比似乎满足了这一点，并且在 JavaScript 前端社区取得了巨大的成功。

它看起来经过深思熟虑和精心设计，有许多合适的铃铛和口哨让你富有成效，但又以自己的方式舒适地保持距离，不会对如何建立网站有太多的意见。

它是使用我确信我会有兴趣学习和尝试的技术构建的: [React](https://reactjs.org/) ， [GraphQL](https://graphql.org/) 。和一个[完全正当的](https://www.gatsbyjs.org/docs/why-gatsby-uses-graphql/)理由。

它似乎有一个架构，由定义良好的组件生命周期和一些中立的、自以为是的普通和有用的东西挂钩组成，使它非常可插，同时又非常强大。

它并没有给出建造什么和如何建造的处方。这不是一个博客解决方案。但这是一个解决方案，使用它，博客，更值得庆幸的是，可以如此优雅地建立。

它拥有*强大的* [社区支持](https://www.gatsbyjs.org/contributing/community/)，以及建立*真正* [快速](https://www.freecodecamp.org/news/how-gatsby-is-so-blazing-fast-c99a6f2d405e/)的网站的良好血统，这不仅仅关注最终用户体验(UX)，也关注开发者体验(DX)。它由 [Webpack](https://webpack.js.org/) 驱动，支持开箱即用的热重装，带有许多合理的默认设置、大量的[插件](https://www.gatsbyjs.org/plugins/)和一套丰富的[指南](https://www.gatsbyjs.org/docs/)、[入门包](https://www.gatsbyjs.org/starters/)和[灵感](https://www.gatsbyjs.org/showcase/)，让你比想象中更快地上手并投入使用。

以及对轻松构建[渐进式网络应用](https://developers.google.com/web/progressive-web-apps/) (PWA)的强大支持。事情的发展方向，今天低估 PWAs 的力量可能是错误的。但是我认为，低估一个简单、优雅且构建良好的工具来帮助构建 pwa 的能力更加危险。

也许我真的爱上了它。也可能不是。但是请放心，我已经被说服了，可以去尝试一下了。

我认为文档可以改进，虽然要更详细一点。

如果你感兴趣的话，这里有一张[详细的图表](https://www.gatsbyjs.org/features/)，对比其他流行的静态网站生成器，如 [Jekyll](https://jekyllrb.com/) 或内容管理系统(CMS ),如 Wordpress 或 site-builders。

#### 那么，都是阳光和彩虹吗？

肯定不会。不过，与其说天气阴沉，不如说阳光明媚。从好的方面来说。

最终，Gatsby 仍然是一个静态的站点生成器，尽管它具有欺骗性的强大。

有一些功能，一个更“真实”和更强大的内容和功能更动态渲染的 CMS 可以提供我所希望的。还记得我在上面一节中提到的动态计算的“本周热门文章”的例子吗？

然而，权衡了利弊，衡量了成本效益，到目前为止，使用 Gatsby 和 Netlify 建立和维护博客的投资回报简直是惊人的！我不会以任何其他方式拥有它！嗯…如果说软件行业教会了我什么的话，那就是*永远不要*说永远不要。但今天不是那一天。

此外，没有走上使用现有托管平台的路线，该平台已经具备了在其社区内分发内容、聚集受众和交叉授粉的能力，这无疑成为一个更难解决的挑战，以聚集持续的受众。老实说，这是一个成功的博客需要解决的最大问题之一。我还没有解决这个问题。

但是肯定有很多方法和想法。首先，我可以一直交叉发布我自己的博客网站作为规范，开始播种一些社区存在和反馈。这是一种常见的模式，我相信许多社区，比如 [Dev.to](https://dev.to/) ，都鼓励这种模式(我绝对崇拜 Dev.to 是什么，代表什么)。随着时间的推移，我肯定会给我的博客增加更多的功能来订阅读者，分享和吸引他们。

创造好的内容，让它浮出水面(在专门的社区或搜索引擎上)并让人们开始谈论它需要一些时间。这不容易，但也不是不可想象的。存在于托管平台上也不能神奇地解决这个问题。这是一场艰难而漫长的战斗，但人们已经一次又一次地这样做了。这也是我想在未来某个时候详细讨论的话题。

* * *

今天就讲到这里。

唷，那是一篇很长的文章！如果你已经通读了一遍，并走到了这一步，我真诚地感谢你！

欢迎反馈和意见！