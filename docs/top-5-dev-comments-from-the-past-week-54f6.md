# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-54f6>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

让我们以回复 **[让我相信类型是很棒的](https://dev.to/ben/convince-me-that-types-are-awesome-2od6)**[@非常感谢](https://dev.to/deciduously) :
开始吧

[![deciduously profile image](img/4f2b714e887ed34503658b5b3276ddc0.png) ](/deciduously) [ Ben Lovy ](/deciduously) • [<time datetime="2019-06-29T13:47:47Z" class="date-short-year"> Jun 29 '19 </time> • Edited on <time datetime="2019-06-29T14:28:00Z" class="hidden m:inline-block date-no-year">Jun 29</time>](https://dev.to/deciduously/comment/cemo) 

使用类型，您可以使非法状态不可表示。您可以存储一个`Name`和`Address`，而不是存储一堆字符串，并且可以编码到类型系统中，使`ContactInfo`必须包含一个电话号码、一个电子邮件地址，或者两者都包含，但不能什么都不包含。通过将该需求编码到类型系统中，就不可能在程序中表示不完整的记录。它也有助于重构——如果你的业务逻辑发生变化，你的编译器将指导你完成重构，以确保你的整个代码库反映了这种变化。

回复 **[计算机技术有过哪些失信行为？](https://dev.to/ben/what-have-been-the-broken-promises-of-computer-technology-4ho0)** ， [@shiling](https://dev.to/shiling) 谈论一些由技术使能的负面趋势:

[![shiling profile image](img/7b501dcbbd64fdabff80827f5b8c105e.png) ](/shiling) [ Shi Ling ](/shiling) • [<time datetime="2019-06-26T11:29:40Z" class="date-short-year"> Jun 26 '19 </time>](https://dev.to/shiling/comment/cbgc) 

当我年轻的时候，我对网络感到非常惊讶，因为我喜欢书，并认为它是一个巨大的无限图书馆。我认为这将有助于减少人们的无知和治疗愚蠢(嘿，我是一个早熟的 10 岁！).天啊，我错了吗，尤其是随着社交媒体和假新闻的出现。人们只是如此沉迷于流言蜚语，而不是了解这个世界。

**[我从来没有过分相信 switch 语句比`if else if else if else if else`](https://dev.to/ben/i-ve-never-become-overly-convinced-that-switch-statements-are-that-much-cleaner-than-if-else-if-else-if-else-if-else-81l)** 干净得多，这个帖子引发了一些大讨论。@0xrumple 的置顶回复是这样的:

[![yaser profile image](img/31df1fc8df7b44c6d537d1e78e852766.png) ](/yaser) [ Yaser Al-Najjar ](/yaser) • [<time datetime="2019-06-28T19:06:56Z" class="date-short-year"> Jun 28 '19 </time> • Edited on <time datetime="2019-06-28T19:07:43Z" class="hidden m:inline-block date-no-year">Jun 28</time>](https://dev.to/yaser/comment/ce64) 

这就是为什么我们在 Python 中从来没有`switch`😁

而且，对于丑陋的开关来说，字典总是一个更好更干净的选择。

```
def my_switcher(x):
    return {
        'a': 1,
        'b': 2,
    }[x]

print(my_switcher('a'))
print(my_switcher('b')) 
```

Enter fullscreen mode Exit fullscreen modeEnter fullscreen mode Exit fullscreen mode
Enter fullscreen mode Exit fullscreen mode

```
</div> 
```

Enter fullscreen mode Exit fullscreen mode

[@unclebob](https://dev.to/unclebob) 附和 **[开/关原则:如何说服其他开发者？](https://dev.to/rvictorino/open-closed-principle-how-do-you-convinced-other-devs-4i33)** 跟帖大解说:

[![unclebob profile image](img/d0c1129599fd54c0033fdff5881cdac3.png) ](/unclebob) [ Robert C. Martin ](/unclebob) • [<time datetime="2019-06-29T14:03:09Z" class="date-short-year"> Jun 29 '19 </time>](https://dev.to/unclebob/comment/cen6) 

开闭原则认为模块应该对扩展开放，但对修改关闭。这意味着您应该能够扩展一个模块的行为，而无需修改它。或者，换句话说，您应该能够通过添加新代码来添加新行为，而不是更改旧代码。

当您更改旧代码时，您有在现有行为中引入错误的风险。您还使旧代码的设计变得复杂，以适应新的行为。每当添加新行为时，您还必须重新部署旧代码。

当你遵循 OCP 时，你就不会冒将错误引入旧代码的风险。您不会使旧代码的设计变得复杂。并且您不必重新部署旧代码。旧代码可以保存在 Jar 文件或 dll 或 Gems 中，或者您使用的任何模块格式中，不做任何更改。

这有利于插件结构。新功能是现有应用程序的插件。它们可以独立部署。它们可以随意部署。而且，最棒的是，它们可以*独立开发*。

所以这不仅仅是添加类和改变方法的问题。相反，这是一个组织代码的问题，使得行为是隔离的、可独立部署的、可独立开发的和可选的。谁不想这样呢？

最后，再来讨论一下 **[是百事通还是一家之主？](https://dev.to/katieadamsdev/jack-of-all-trades-or-master-of-one-56b2)** ， [@georgecoldham](https://dev.to/georgecoldham) 谈论追随你的激情和好奇心的重要性:

[![georgecoldham profile image](img/e38bb1b308603420030d194dee8ea4b7.png) ](/georgecoldham) [ George ](/georgecoldham) • [<time datetime="2019-06-27T12:00:29Z" class="date-short-year"> Jun 27 '19 </time>](https://dev.to/georgecoldham/comment/ccmo) 

我坚信追随你的激情。

你惊奇地看着别人做的每一件事，你希望自己也能这样做(还有那边那个东西，哦，还有你昨天看到的那个，还有你上周看的那个副业，哦，还有....).

随着时间的推移，你会对一些因为工作或对某件事的强烈兴趣而不得不做的事情有更深的了解。你会发现自己对更多的技术元素充满了好奇，更深入兔子洞。

重视能够快速学习的能力远远高于背诵技术信息的能力，这是一种更有价值的技能。你知道的越多，使用的越多，从技术上来说胡扯的能力就会变得越容易。

如果你不得不去面试，那就花一周的时间学习技术术语和面试，然后希望你真的不需要它。

下周见，更多精彩评论，✌