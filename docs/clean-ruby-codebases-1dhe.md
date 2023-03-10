# 干净的 Ruby 代码库

> 原文：<https://dev.to/leejarvis/clean-ruby-codebases-1dhe>

*原文：<https://leejarvis.me/posts/2019/clean-ruby-codebases>T3】*

我喜欢阅读代码。即使是来自
一个不知名的库或插件的遥远角落的最小片段也足以激发一个新的想法，并且
至少它可能会引起兴趣、排斥(通常在阅读我的旧代码时)，或者只是教我一些新东西。

俗话说，代码被读的次数比它被写的次数多得多。编写伟大的代码需要多年的经验，没有人不阅读大量的代码就能成功。

我在 JavaScript 流行之前就开始写代码了，当时学习 HTML 意味着复制你最喜欢的网站源代码的内容，并根据你的目的进行调整。新网站引入了新的 HTML 标签和 CSS 规则，远远早于 w3schools 的突出地位。

当我开始学习服务器端的语言时，我自然变得有些困惑。那时我真的买不起这些书；我真的很想尝试所有的东西，看看有什么能坚持下来。

阅读我无法理解的代码成了一种习惯，甚至在我无聊的实验中安装几个操作系统也没能阻止我。在我能真正自己编写代码之前，我学会了欣赏干净、写得好的代码。GitHub 最终出现了，让这种体验变得毫不费力。

当与新开发人员交谈时，我用几个例子来展示我认为可读性很好的 Ruby 代码。这当然有一定程度的主观性，但对我来说，干净的代码:

*   简单明了
*   很容易消化
*   通常遵循通用风格的最佳实践
*   结构良好
*   避免过多的魔法/元编程
*   仅包含对您有帮助的注释

所以，事不宜迟，这里有几个我最喜欢的:

**[Sidekiq](https://github.com/mperham/sidekiq/)**

Sidekiq 是一个用于处理后台作业的库。对于想要在 Redis 中存储队列数据的 Ruby 和 Rails 开发人员来说，
几乎被认为是首选工具。什么是一个相当复杂的软件
写得如此之好，它基本上一直握着你的手。

**[续作](https://github.com/jeremyevans/sequel)**

Sequel 是一个用于 Ruby 的数据库工具包。它是对 ActiveRecord 的有力替代，也是我对非 Rails 项目的选择。它已经有 12 年的历史了，是我遇到的第一批 Ruby 库之一。

**[Oga](https://gitlab.com/yorickpeterse/oga)T3】**

Oga 是用 Ruby 写的 XML/HTML 解析器(很疯狂吧？).虽然
Oga 不再被维护，但它仍然是一个易读的
代码的好例子。

**[花见](https://github.com/hanami/hanami)**

Hanami 是一个年轻的 Ruby web 框架。它旨在对抗 Ruby on Rails 通过其“包括厨房水槽在内的一切”方法引入的极端膨胀。这个项目被分成更小的部分，使得
很容易消化和研究。

**[HTTP](https://github.com/httprb/http)T3】**

HTTP 是我用来发出 HTTP 请求的 Ruby 库。它使用起来又快又简单
(谁还记得`net/http`的语法呢？)，而且缺少
的“神奇”代码，真的很容易理解在
的引擎盖下发生了什么。

* * *

你有最喜欢的代码库吗？