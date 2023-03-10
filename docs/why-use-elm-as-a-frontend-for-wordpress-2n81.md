# 为什么用 Elm 作为 WordPress 的前端？

> 原文：<https://dev.to/wking/why-use-elm-as-a-frontend-for-wordpress-2n81>

我想分享一下我对 Elm 为什么适合 WordPress 的想法。这不会深入探究 Elm 是如何工作的。我只是想找到我在 WordPress for Elm 中看到的机会空间，并快速回答让我得出这个结论的问题。如果你想看更多关于 Elm 如何工作的实际例子，特别是在 WordPress 的环境中，你可以点击这里:

[项目仪表板](https://www.wking.dev/projects/elm-press)

### 找合适的地方打招呼。

我这个项目的目标是帮助 Elm 进入 WordPress 社区，而不是把 WordPress 带入 Elm 社区。这似乎是一个很小的区别，但它对正确的切入点会有很大的影响。WordPress 是一个成熟且非常成功的平台。所以，WordPress 开发者需要确信 Elm 是一个比他们一直在做的更好的解决方案...永远。

要做到这一点，问题空间必须缩小，找到 Elm 的优势所在，在与 WordPress 斗争之后，这似乎是一个显而易见的选择。对我来说，正确的地方是....

[![Drumroll please!](img/8de7b134b2d15612005de64c9bc60e3e.png)](https://i.giphy.com/media/ogGmxeqA8L3sA/giphy.gif)

插件开发！Elm 不仅在这里影响最大，而且最容易被采用。更进一步，我想从插件管理界面开始。

### 完美契合

[![Guy trampolining into a blowup ball](img/107e346cf80c25826764db79f1903d09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZOCOvMVN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.giphy.com/media/uPkcXBZ4RAcqk/giphy.gif%3Fcid%3D19f5b51a5d3cc3b6556343522e5e3e1d%26rid%3Dgiphy.gif)

好了，关于我开始提到的那些问题。在经历了插件开发、维护和成长之后，我所面临的最大问题几乎可以完美地用 Elm 提供的优势来解决。让我展示给你看:

### 线条模糊。

**问题:**

后端代码不应该关心管理前端的状态。

在 WordPress 中，一切都是 PHP。起初这听起来不错，但实际上这最终看起来像是一个代码库，其中 PHP 代码在这里做一点后端，在那里做一点前端，突然之间两者之间的界限非常模糊。

**解决方案:**

WordPress REST API。所以你解决 WordPress 中问题的方法是 WordPress？是的。这迫使你的后端只保持你的后端，而你的前端可以保持你的前端。顺便说一下，使用 API 是 Elm 的一个梦想。

### UI 复杂度

**问题:**

插件生态系统是巨大的，开发者正在构建的工具本身就是成熟的应用程序。我们正在讨论拖放、实时更新、交互式指标等等。PHP 是专门为处理这样的 UI 复杂性而构建的吗？没有。

PHP 不是也从来不是用来管理复杂 UI 的。你认为脸书为什么会做出反应？内置的状态管理和可重用性是不存在的。

**解决方案:**

Elm 就是为这个用例而构建的。Elm 具有静态类型、状态管理和为模块化、简单性和开箱即用的可靠性而构建的架构。我不会深入探究 Elm 是如何工作的，但是你可以也应该在这里阅读所有相关内容[https://guide.elm-lang.org/](https://guide.elm-lang.org/)。

### 无畏的成长

**问题:**

这对我很重要。伴随着长寿和成长而来的是一系列新的问题。特别是在平衡以下三个事实时。

事实一:很多人依赖你所建立的东西，但不仅仅是为了他们自己。你的插件会影响他们的客户，从而影响他们的业务。

**事实二:**为了跟上客户增长，新功能和扩展始终是必要的。

**事实三:**您将拥有遗留代码和在代码库中做出的需要维护或更新的旧决策。

这三个事实意味着你的插件新版本最好坚如磐石。你不能害怕在需要的时候更新你的代码库，或者害怕发布错误，因为它们会影响你的客户的业务，伤害他们和你的声誉。

**解决方案:**

Elm 最大的优势之一是可以自信地重构你的整个代码库。不要相信我的话:

> ![Eddy Lane profile image](img/1eb63e64e9ca1d2bff81ad4f547f8c36.png)Eddy Lane@ edd Lane![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)断断续续使用 Elm 两年多了，当你重构数百行代码时，我仍然没有摆脱那种温暖的模糊感，它在编译后的第一次运行就完美了。2018 年 7 月 02 日上午 10:03[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1013724896772481024)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1013724896772481024)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1013724896772481024)2

> ![Julien Lengrand-Lambert profile image](img/f086e8e51f9396408005e7a9c850080f.png)Julien Lengrand-Lambert[@ jlengrand](https://dev.to/jlengrand)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)在 10 年的[#编程](https://twitter.com/hashtag/programming)中，在使用了 [#typescript](https://twitter.com/hashtag/typescript) 、 [#java](https://twitter.com/hashtag/java) 、 [#python](https://twitter.com/hashtag/python) 、 [#java](https://twitter.com/hashtag/java) 脚本等等之后，这是我第一次能够 yolo refa 感谢[#榆树](https://twitter.com/hashtag/elm)[#榆树](https://twitter.com/hashtag/elm)郎的这段经历！2018 年 7 月 23 日下午 13:08[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1021381419795394561)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1021381419795394561)11[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1021381419795394561)35

> ![Rao profile image](img/49d805792905069d44059ea75a156b95.png)饶@饶娆 _![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)几个月后写我的第一个榆树 app 我都忘了重构有多好玩了。你很少需要担心过于激进——编译器会把你从几乎所有的泥潭中解救出来。在 Ruby 呆了几个月，就上瘾了。23:20PM-01 2018 年 7 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1013563023212941313)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1013563023212941313)1[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1013563023212941313)16

### 离我够远了

这只是我基于 WordPress 和 Elm 开发经验的观点。我并不是说在 WordPress 中没有解决这些问题的方法。我认为 Elm 是一个有价值的解决方案，如果你对 WordPress 所提供的不满意，特别是作为一个前端开发者。我迫不及待地开始在 Wordpress 中构建 Elm，但是我也想听听你的意见！

如果你想了解我的最新进展，并成为这一探索的一部分，请注册下面的更新！另外，欢迎在这篇文章的 tweet 线程上发表评论:

**注册更新:**

[跟随](https://www.wking.dev/newsletter)

**推文线程:**

> ![Will King profile image](img/63c08335ced48715a8f909dcc2f24e5c.png)将王@ wking _ _![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)1/我想分享一下我对 Elm 为什么很适合 WordPress 的想法。这不会是对 Elm 的深入探究。我只是想找到我在 WordPress for Elm 中看到的机会空间，并快速回答让我得出这个结论的问题。2019 年 7 月 30 日下午 19:39[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1156288341647773701)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1156288341647773701)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1156288341647773701)2