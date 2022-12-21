# 博文:学锈

> 原文：<https://dev.to/jonasbn/blog-post-46gm>

我正在评估是尝试学习**围棋**还是**铁锈**，于是决定先看看**铁锈**。在我写这篇文章的时候，这是一项正在进行中的工作，但是我可以透露一些我已经找到的资源和我正在进行的过程。

Rust 语言很好地记录了过多的不同资源，有时很难决定使用什么资源。主要资源当然是[Rust 编程语言网站](https://www.rust-lang.org/en-US/)，它托管了这本书:“[Rust 编程语言书](https://doc.rust-lang.org/book/2018-edition/foreword.html)”。2018 年版在写作时，当*谷歌*时，你可能会找到旧版本的链接，但页面经常在可能的时间和地点引用新版本，并且已经进行了更新。

如果你已经安装了 **Rust** ，你可以生成一个本地版本，下面的命令会完成所有的工作，包括在你默认的浏览器中打开这本书:

```
$ rustup docs --book 
```

Enter fullscreen mode Exit fullscreen mode

你也可以在网站上找到“ [Rust by Example](https://doc.rust-lang.org/rust-by-example/index.html) ”一文，里面有很多有用的例子和实用的方法。

嗯，我决定从曼宁的付费视频课程“ [Rust in Motion](https://www.manning.com/livevideo/rust-in-motion) ”开始。它是由[@卡罗尔·尼科尔斯](https://twitter.com/carols10cents)和[@杰克·古尔丁](https://twitter.com/JakeGoulding)创作的。推荐视频课程，有免费的预览供您深入了解课程的形式和内容，在线有[示例源代码。](https://github.com/integer32llc/rust-in-motion-videos)

除了跟随课程和使用上面提到的资源，我还使用[Codewars.com](https://www.codewars.com/)。Codewars 提供了按难度分组并组织成形的小型编码挑战，这就像你有时会在技术书籍的一章末尾找到的小型练习一样——反正形是很多的，来自各种不同的作者，并在问题和挑战中给出了很大的变化。

我现在一次做一个形。然后我利用上面提到的资源来了解如何在 **Rust** 中提出一个可行的解决方案。我还在第八名。在**锈**上的 kyo(最低)，但是我玩得很开心。

早些时候，我写了一篇关于建立一个 **Rust** 开发环境 ( [dev.to](https://dev.to/jonasbn/til-setting-up-a-rust-development-environment-2f95) )的 TIL/blogpost。因此，当我检查 Katas(或其他编程任务)时，我从我的开发环境中获得了大量的反馈，这使我广泛地查阅了 **Rust** [标准文档](https://doc.rust-lang.org/std/)和我自己的已完成的 Katas 和 **Rust** 示例库，无论从哪里获得的任务都是相同的。

当我偶尔遇到一个耗费太多时间或者需要额外*思考* / *淋浴* / *睡觉*的挑战时，我会跳到另一个挑战，然后返回*更明智的*和更多*经验丰富的*在**生锈**。

Rust 工具链信息丰富，我听到很多人高度评价编译器发出的错误信息。这让我想到了[的列表 **Rust** 编译器错误](https://doc.rust-lang.org/error-index.html)。

或者你可以使用编译器本身:

```
$ rustc --explain E0200 
```

Enter fullscreen mode Exit fullscreen mode

还有其他资源，如[Codewars.com](https://www.codewars.com/)和推荐是最受欢迎的，但如果你不是那么喜欢形的东西和公开展示你的解决方案，我可以推荐[@卡罗尔尼科尔斯](https://twitter.com/carols10cents)[沙沙声](https://github.com/rust-lang/rustlings/)。

我以前从未见过类似于 **Rustlings** 的东西，它是一个 GitHub 项目，组织为一系列容易出错的代码，组织为 build，每次你得到一个要编译的步骤，你就移动到文本——这完全感觉像一个游戏。不过，我可以在不破坏任何东西的情况下给你一点小建议。请务必阅读显示的错误，因为当您获得一个要编译的步骤时，它会自动转到下一个任务，有时您看不到您已经成功，应该评估一段新代码。

如果你再遇到一些*粘* **锈**码 [@Jake Goulding](https://twitter.com/JakeGoulding) 已经把[变成了网上游乐场](https://play.rust-lang.org/) ( [REPL](https://en.wikipedia.org/wiki/REPL) )。

最后，我可以推荐 IRC 获得 Rust 的帮助，但不幸的是[它将在未来的某个时候退役](https://blog.rust-lang.org/2019/04/26/Mozilla-IRC-Sunset-and-the-Rust-Channel.html)。还有 **Rust** 用户群，我所在的当地团体是[哥本哈根 Rust Group](http://cph.rs/) ，我还没有参加过 meetup，但是我很想参加其中一个 hack 之夜来提升我的 **Rust** 学习。

作为一个小小的额外奖励，我可以给你一个刚刚*重新发现*的资源，一个[小抄](https://cheats.rs/)，我已经做了书签，但忘记使用了——它看起来令人难以置信地令人印象深刻，希望能好好利用它。

祝你的 **Rust** 历险愉快，如果你有任何你认为对我的 **Rust** 奋进有益的资源，请给我推荐 **Rust** 学习资源。

祝你好运并保持安全...

从我的[到收藏](http://jonasbn.github.io/til/rust/learn_rust.html)的交叉发布

## 资源

*   [Rust 编程语言](https://www.rust-lang.org/en-US/)
*   [铁锈编程语言书](https://doc.rust-lang.org/book/2018-edition/foreword.html) 2018 版
*   [生锈的例子](https://doc.rust-lang.org/rust-by-example/index.html)
*   [铁锈操场](https://play.rust-lang.org/)
*   【Rust 官方学习资源
*   [其他 Rust 学习资源](https://github.com/ctjhoa/rust-learning)
*   [锈病资源的收集](https://github.com/rust-unofficial/awesome-rust)
*   [Codewars.com](https://www.codewars.com/)
*   [铁锈标准文件](https://doc.rust-lang.org/std/)
*   [铁锈备忘单](https://cheats.rs/)
*   [沙沙声](https://github.com/rust-lang/rustlings/)