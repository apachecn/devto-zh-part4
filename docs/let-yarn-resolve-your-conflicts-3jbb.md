# 让纱线解决你的矛盾

> 原文：<https://dev.to/brettimus/let-yarn-resolve-your-conflicts-3jbb>

*归档到:“我怎么不知道这个？！?"*

* * *

总的来说，我不喜欢冲突。

但是工程就像生活一样，保证了偶尔的冲突。有时，这种冲突发生在你的包管理器的锁文件的丛林中。

为了解决`yarn.lock`中的合并冲突，我通常只是删除它并再次运行`yarn`。这相当于开发商在喊“微小的烟雾弹！”然后逃跑。

这是最佳实践吗？没有可能。有用吗？差不多一直都是。

嗯，今天不小心跑了`yarn`才删了我冲突不断的`yarn.lock`。

令我高兴的是，我收到了这条消息:

[![](img/fc638d40faba0199fd057f7df0a22923.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7bK8o6GK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2j6qy3g1jrwd2hqw14oh.png)

这是个东西！我想让全世界都知道！！！

Yarn 将尝试解决您的锁文件中的合并冲突！

这个特性似乎是在 Yarn 1.0 中引入的。基本上是两年前。

有时候感觉自己有点落伍了😬