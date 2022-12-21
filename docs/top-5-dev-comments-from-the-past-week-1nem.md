# 上周前 5 名开发者评论

> 原文：<https://dev.to/devteam/top-5-dev-comments-from-the-past-week-1nem>

这是你可能已经错过的令人敬畏的开发者评论的每周综述。欢迎并鼓励你使用 **[#bestofdev](https://dev.to/t/bestofdev)** 标签来发表帖子和评论。

[@humrochagf](https://dev.to/humrochagf) 回复 **[改变我一生的编程语言！](https://dev.to/lukegarrigan/the-programming-language-that-changed-my-life-2kc7)** 用不同的语言与自己联想:

[![humrochagf profile image](img/745f8b875848c72317934892ffd57cdc.png) ](/humrochagf) [ Humberto Rocha ](/humrochagf) • [<time datetime="2019-08-31T18:19:31Z" class="date-short-year"> Aug 31 '19 </time>](https://dev.to/humrochagf/comment/en5f) 

很棒的帖子，我在我的计算机科学课程上接触过 Prolog，我为此挣扎了很久。也许是时候带着你推荐的这本书回去再试一次了。

对我来说，我所经历的每一种编程语言都在某些方面改变了我的生活。

*   c 让我对资源管理非常关心。
*   大会，以了解更多的事情是如何运作的准系统。并且对于编译器和解释器的存在也是优雅的😄
*   理解 Java 的经典设计模式
*   Python 来发现我对编码、社区、开源的热情，让我更好地用代码表达自己。
*   JavaScript 和 TypeScript 对我理解事件和异步如何工作帮助很大
*   Shell 是服务器任务自动化的网关
*   现在 elm 培养了我对函数式编程的热情

每种语言都让我接触到看待☺事物的不同方式

这是一个价值 100 万欧元的节点脚本吗？ 是一本真正有趣的读物。 [@antontsvil](https://dev.to/antontsvil) 谈到确保你真正遵循需求的重要性:

[![antontsvil profile image](img/626748d5e2f02651623f80a4718ef77c.png) ](/antontsvil) [ Anton T ](/antontsvil) • [<time datetime="2019-08-29T10:17:57Z" class="date-short-year"> Aug 29 '19 </time>](https://dev.to/antontsvil/comment/ekg0) 

这是一个太熟悉的悲剧警示故事。你认为你知道客户想要什么，然后当你全心全意地打造完美的产品时，你意识到你完全误解了需求！教训是要记住在试图解决问题之前要完全理解问题😉

[@andrewbrown](https://dev.to/andrewbrown) 在这个讨论帖中有一个很棒的答案: **[如果你的经理让你在接下来的两周内，在工作中做任何一个“有趣且有成就感”的项目，你会怎么做？](https://dev.to/ben/if-your-manager-told-you-to-spend-the-next-two-weeks-working-on-any-fun-and-fulfilling-project-at-work-what-would-you-do-2cek)** 。就我而言，我期待着看到这最终成为现实:

[![andrewbrown profile image](img/e847a588254043528adef5924ecc7675.png) ](/andrewbrown) [ Andrew Brown 🇨🇦 ](/andrewbrown) • [<time datetime="2019-08-27T23:35:24Z" class="date-short-year"> Aug 27 '19 </time> • Edited on <time datetime="2019-08-28T21:44:04Z" class="hidden m:inline-block date-no-year">Aug 28</time>](https://dev.to/andrewbrown/comment/eim2) 

我会完成我的 AWS《星际迷航》卡拉 ok 应用程序，你可以输入卡拉 ok 配乐，它会剪辑视频照片，让《星际迷航》中的角色唱歌。

所以我可以做这样的东西:

[https://www.youtube.com/embed/rxos6LqTD0I](https://www.youtube.com/embed/rxos6LqTD0I)

在 AWS 上转录 TNG 的整个季节需要 500 美元，但我有足够的信用来这样做。

[![](img/5261a6c2dc95cd40e61cfa7828fedecb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iKH0oOR_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8ic0x4hwwudqiy0b35dr.png)

```
</div> 
```

Enter fullscreen mode Exit fullscreen mode

**[JavaScript 是最令人困惑的编程语言吗？](https://dev.to/ilonacodes/is-javascript-the-most-confusing-programming-language-48be)** 嗯， [@curtisfenner](https://dev.to/curtisfenner) 有一些附加的想法和例子:

[![curtisfenner profile image](img/91ca80551ad31d2a28b85aecd2e24e13.png) ](/curtisfenner) [ Curtis Fenner ](/curtisfenner) • [<time datetime="2019-08-31T01:16:47Z" class="date-short-year"> Aug 31 '19 </time>](https://dev.to/curtisfenner/comment/emh0) 

我认为假装所有的语言都是平等的是不诚实和适得其反的。TypeScript 实际上是从一个有问题的现实中诞生的，即在大型团队中编写 JavaScript 比它需要的更昂贵。

JavaScript *是*非常混乱，到了“混乱”是“容易预测”和“理解它并不复杂”的反义词的程度。

对于 JavaScript 令人困惑的复杂性的一小部分:

`{var x = 1; {var x = 2;} console.log(x)}`是`2`但`{let x = 1; {let x = 2;} console.log(x)}`使`1`。

`doThing(arg, (x) => { this.receive(x); });`一般会工作，而`doThing(arg, function(x) { this.receive(x); });`一般不会。

`[1, 2, 3, 10, 20, 30].sort()`就是`[1, 10, 2, 20, 3, 30]`。
`["x", "y", "z", "a", "b", "c"].sort((a, b) => a - b)`不改变顺序。

`"slice" in []`但是`for (let p in []) {console.log(p);}`什么也不打印。

`"𝕋𝕋".length`是`4`但是`Array.from("𝕋𝕋").length`是`2`

`{[{}]: 10}["[object Object]"]`是`10`，尽管这显然没有意义

`(a => a).length`是`1`，`Array.from((a, b) => a + b)`是`[undefined, undefined]`。

`arr.push`、`arr.pop`、`arr.splice`不返回数组，而是修改；`arr.sort`和`arr.reverse`修改并返回数组；`arr.concat`、`arr.slice`、`arr.map`不要修改数组。

`0 == "0"`和`0 == ""`而是`"" != "0"`。

当然，所有这些都有(相对简单的)解释。然而，每一个都代表了一个“琐事”，为了真正理解 JavaScript 代码，你必须知道这些；它们都是程序员为了不被 JavaScript 迷惑而不得不(反复)克服的许多障碍中的一部分。显然，这也只是 JavaScript 产生的混乱的几个突出例子的精选；了解这些并不意味着你不会被 JavaScript 迷惑。

尽管如此，JavaScript 绝对不是 T2 最令人困惑的语言。我也同意，它变得越来越好(特别是像 TypeScript 编译器这样的共存工具使得编写普通的 JavaScript 变得更加容易)。(不幸的是，正如上面的列表所显示的，这也使得 JavaScript 变得更加复杂，因为“遗留”特性与行为略有不同的“现代”特性并存)。

作为一个试图跟上编程语言理论的人，我害怕对编程工具的自满。显然，我们今天所处的位置*离编程工具的“顶点”*还很远(毕竟，编程语言本身在这一点上只有大约 60 年的历史)，这意味着*在*的基础上还有很多需要改进的地方。认识到我们已经取得了很大的进步是很重要的，但是不忽略我们仍然可以改进的地方也是非常重要的。

(以上并不是 JavaScript 独有的，尽管我确实认为 JavaScript 是更容易引起混淆的一种。c 可能是最常被称为“足够好”的语言的竞争者，尽管它有很多值得改进的地方。

作为对 **[的回应，苹果想从 macOS 中移除脚本语言](https://dev.to/stereobooster/apple-wants-to-remove-scripting-languages-2l0i)** [@codemouse92](https://dev.to/codemouse92) 加入了一些关于这个术语本身和苹果潜在动机的想法:

[![codemouse92 profile image](img/a072df6f7dff30b6b5a4ad8b83c98750.png) ](/codemouse92) [ Jason C. McDonald ](/codemouse92) • [<time datetime="2019-08-31T15:50:02Z" class="date-short-year"> Aug 31 '19 </time> • Edited on <time datetime="2019-08-31T15:50:43Z" class="hidden m:inline-block date-no-year">Aug 31</time>](https://dev.to/codemouse92/comment/en1o) 

“脚本语言”一直让我觉得是一个含糊不清的词。它缺乏一个正式的定义，但是被自由地应用于任何一种被开发者认为“不是真正的编程语言”的语言(同时对资产阶级的厌恶嗤之以鼻)。)

如果他们指的是“解释语言”，那是具体的东西，但这也意味着放弃 Java。

和...遗产？Python 什么时候成了“遗产”了？它是当今最流行的应用程序开发语言之一。(就这一点而言，Ruby 和 Perl 甚至都不是“遗留”的。)

不过，我有一个理论...我认为苹果只是试图“鼓励”(强迫)开发者使用他们的内部语言，并建立更多的苹果专用软件。又是整个围墙花园游戏。

但后来，我在四年前开始抵制苹果产品，当时他们开始明确阻止 Linux 能够与 iDevices 通信。(是的，技术支持证实了这一点。)脏池只是他们的游戏。

苹果爱好者:来 Linux 吧！花园篱笆的这一边有自由。；)

下周见，更多精彩评论，✌