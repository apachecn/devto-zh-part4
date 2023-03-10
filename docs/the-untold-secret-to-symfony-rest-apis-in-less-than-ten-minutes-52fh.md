# SYMFONY REST APIs 不为人知的秘密

> 原文：<https://dev.to/bamboriz/the-untold-secret-to-symfony-rest-apis-in-less-than-ten-minutes-52fh>

大家好，今天我想分享我今年在 API 开发方面的一个非常有趣的经历。基本上，它是关于我如何能够在几乎没有框架知识的情况下构建一个生产就绪的 Symfony REST API。

所以我有一个项目，一个客户想让我做，但是非常具体的使用 Symfony。它是一个后端 API，用于支持前端 React SPA。尽管我从未和 Symfony 共事过，但我觉得我能应付得来。我确实有一些使用 CodeIgniter 3 和 Slim PHP 的经验，我知道我可以利用这些经验，但我以前从未构建过 API。但是我知道我不能就此放弃——这种体验——而且客户玩得真的很好😉。所以我开始工作…

### **书籍的魔力**

> "书籍是一种独一无二的便携式魔法."―斯蒂芬·金

我需要一些魔法。我不仅要构建一个 API，还要构建一个好的 API。是的，我是那些相信只有在你能做好的情况下才工作的人之一。所以我求助于书籍。我首先想到的是菲利普·斯特金的《构建你不会讨厌的 API》。一个朋友曾经推荐过它，但当时我并不需要它。

我抓起书，很快就看完了。我真正喜欢的是这本书如何为我揭开了整个事情的神秘面纱。在他的书中，Philp 真的让它变得很有趣，在了解了所有这些新概念后，我立即开始感到非常自信。然后我进一步阅读了另一本书——迈克尔·斯托的《不受干扰的休息》。在那两次之后，我真的感觉到 API 主宰了世界。

### **SYMFONY REST API:第一次尝试**

我现在准备去拿我的手。但首先我必须学会 Symfony。

我看了 YouTube 视频，上了一些课程，但目前为止最好的是在 [SymfonyCasts](https://symfonycasts.com/screencast/symfony) 上的**starled Development with Symfony 4 课程**。如果你和我一样，也和我一样不熟悉 Symfony，SymfonyCasts 是你应该去的地方。Symfony 4 非常有趣，当我发现大多数很酷的东西都出现在版本 4 中时，我非常高兴我以前从未尝试过 Symfony😊。一旦基本原则到位，我必须开始考虑如何将整个 API 的东西带入到节目中。就在这个时候，我遇到了 FOSRESTBundle 和 API 平台。现在出现了一个两难的局面！

### **FOSRESTBundle VS API 平台**

有很多文章和帖子都是关于这个的，所以我要大声疾呼哪个更好，而不是什么对我有用。所以我读了所有我能找到的比较，并决定亲自尝试这两个。我首先在 Symfony 中建立了一个简单的 API(没有工具),它真的很酷很简单。然后我试着用 [FOSRESTBundle](https://github.com/FriendsOfSymfony/FOSRestBundle) 做同样的事情，但发现有点棘手，我花了很多时间试图弄清楚如何让它工作。这让过程变得更容易，但我可能没有感觉到我想要的那种轻松。

在那一点上，我已经想用 Symfony(没有工具)来构建 API，但后来我尝试了 API 平台，真的，我被震撼了！

因此，我对这个新发现感到兴奋，它让构建 API 变得如此简单。这太好了，令人难以置信，所以我做了大量的研究，我读到的一篇非常好的文章是 Marek Gajda 写的—[API 平台实用指南:如何判断它是否适合您](https://tsh.io/blog/practical-guide-to-api-platform-how-to-tell-if-its-the-right-framework-for-you/)。然后，我决定 API 平台是我的 SYMFONY REST API 的家伙。

### **结论**

简单来说，API 平台是一个基于 Symfony 的框架，它加速了 RESTful APIs 的开发。它是基于最佳实践的，并且在 API 开发中不要重复发明轮子。

带有 API 平台的 SYMFONY REST APIs 绝对值得任何小型、中型或大型项目考虑。虽然远非完美，但我真的相信这个项目和团队。他们在解决问题和回答 Slack 频道上的问题方面做得很好。这使我能够完成我的第一个项目，并在之前几乎没有 Symfony 经验的情况下构建一个生产就绪的 RESTFUL API。

我会在我的[博客](https://camertechtrends.com/secret-to-symfony-rest-apis-in-less-than-ten-minutes/)上发布一个完整的帖子，介绍我在 API 平台上面临的一些挑战(现在仍然面临)，一些如何克服这些挑战的技巧，以及我如何完成 API 并将其托管在 AWS Elastic Beanstalk 上。

**请留下您的任何问题，如果您对 API 平台有任何经验，我将乐于倾听**。

谢谢！