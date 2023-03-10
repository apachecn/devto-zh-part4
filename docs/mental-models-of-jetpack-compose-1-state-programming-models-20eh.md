# 喷气背包的心理模型构成#1:状态和编程模型

> 原文：<https://dev.to/louis993546/mental-models-of-jetpack-compose-1-state-programming-models-20eh>

> 更新(2019.08.19): [第二章现在开始](https://dev.to/louis993546/mental-models-of-jetpack-compose-2-declarative-ui-with-code-separation-of-concerns-4636)🎉

<figure>[![](img/8b28e92c1d9023bc69a7eb7038bb2687.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P4vEd95u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Azij_6EWgVip4GyOtH8zBMA.jpeg) 

<figcaption>通称“心智模式”。我真的不知道如何将这个概念形象化🤷</figcaption>

</figure>

所以上个月，droidcon Berlin 发生了，我做了一个关于 Jetpack Compose 的演讲,[简要介绍了内部情况，以及他们已经做出的一些决定](https://speakerdeck.com/louistsaitszho/jumping-into-jetpack-compose-way-too-early-to-see-whats-inside)。

我的确在会议前写了一堆博客文章，我真的很感谢所有的支持。当视频仍然不可用时😠，与会者也没有太多的反馈，我确实觉得有很多基础知识应该被涵盖。所以今天，我想给你们一些所谓的“心智模型”的简要概述，这是让你们理解 Jetpack Compose 对未来的 Android 开发人员真正意味着什么的第一步。

所以首先，在谈论任何心智模型之前，让我们简单地谈谈它是什么，以及为什么我认为它们很重要。根据维基百科:

> 心智模型是对某人在现实世界中如何运作的思维过程的解释。

首先，这是一个解释。第二，它来自一个人，所以它可能是个人的、主观的，甚至是过度简化的(即技术上错误的)。

为什么了解一些心理模型对理解 Jetpack Compose 很重要？在我看来，Jetpack Compose 带来了太多潜在的翻天覆地的变化，随之而来的是，一堆当前的最佳实践或标准将会过时，在某些情况下甚至是错误的。就像从 Jave 到 Kotlin，或 AsyncTask 到 Rx，或 Rx 到 Coroutine 的转换一样，有些东西你可能想取消学习，为了取消学习这些东西，你必须把你的头缠在原来的问题上，然后重新应用新的思维方式，即心智模型。

### 状态

除非你在玩 [apk 高尔夫](https://github.com/fractalwrench/ApkGolf)，否则你的应用程序会有[状态](https://en.wikipedia.org/wiki/State_%28computer_science%29)。视图有状态，你的逻辑有状态，到处都有状态。基本上，如果你代码**记住了**任何事件(可以是文本变化、网络响应、系统回调，几乎任何事情)，你的应用程序就被认为是“有状态的”，那段数据就是一个“状态”。所有这些事件只发生几毫秒，而有一种状态会持续更长时间。

你通常对任何状态所做的就是相应地转换视图。一些常见的例子包括:

*   当标志为`checkCheckbox == true`时，则设置一个`CheckBox`为选中状态
*   当`List<User>`改变时，将`ProgressBar`设置为`GONE`，将`RecyclerView`设置为`VISIBLE`。

### 编程模型/范例

你的程序有状态，但是我们如何在它们之间转换呢？我们用代码做到这一点，我们编写代码的不同方式被我们称为“编程模型”或“[编程范例](https://en.wikipedia.org/wiki/Programming_paradigm)”。当我说“不同的方式”时，我不仅仅指编程语言，或架构，或“有多少种方式将 1 加到`x`”(`++x`、`x++`、`x += 1`、`x = x + 1`等等)。有一个基本的思维过程被融入到语言设计、标准库和所有中间事物中，这定义了我们如何提出解决方案。

#### 命令式编程

TL；DR:在[命令式编程](https://en.wikipedia.org/wiki/Imperative_programming)中，你使用语句(或者所谓的“指令”)来改变程序的状态。作为程序员，你负责编写**如何**改变一个状态，像每一个`setText`，每一个`notifyDataSetChanged`，每一个`invalidate`。如果您多次触发它，您可能不会生成相同的结果；如果你在不同的状态下触发它，它可能不会产生相同的结果。

#### 声明式编程

TL；DR:在[声明式编程](https://en.wikipedia.org/wiki/Declarative_programming)中，你或多或少只是写了一个通用规则/预期结果，而不是写一个程序应该如何执行。

*   在 SQL 中，不写“转到用户表的开头，提取 5 行”，只写“从用户表提取 5 项”。
*   在正则表达式中，你不写“把输入除以`.`，然后数一数有多少组”，你去 [StackOverflow，复制“数字-点-数字-点-数字-点-数字”规则上投票最多的答案](https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp)，即 IP 地址。
*   而且在 Android 布局中(我们这里说的是 xml)，你不要写“添加`TextView`到`LinearLayout`，然后再添加一个`TextView to that`linear layout`”, you write 2`TextView`s surrounded by a`linear layout`。

一旦你写完了所有这些，你就把它交给某人:SQLite、`java.util.regex.Pattern`和`LayoutInflator`，他们会神奇地找到一种(可能)有效的方法来给你你期望的结果。

需要注意的一点是，这两个模型之间可能有相当多的重叠/交织，而`RecyclerView`就是一个很好的例子:视图持有者模式是相当声明性的:它并不真正关心谁/如何/为什么有人触发它，您只是根据输入来呈现视图持有者；而`Adapter`是非常必要的，因为您负责使用语句通知正确的位置来触发特定的视图持有者进行更新。

以声明的方式包装现有的命令式代码也并不少见，而[导航组件](https://developer.android.com/guide/navigation/navigation-getting-started)就是一个很好的例子。它由所有现有的片段 API 提供支持，但不是强制性地给管理器一个事务，而是声明性地预定义所有可能的屏幕和它们之间的方向，正确的事务将被生成和执行。其他常见的例子包括

*   用 Moshi 的 codegen 进行 JSON 解析
*   数据绑定
*   几乎所有格雷尔做的事情

有些是在编译时生成代码，有些只是在运行时将它们转换成正确的参数，无论哪种方式，作为这些库的用户，你只负责问“是什么”，而不是“如何”。虽然 Wikipedia 将 Java(可能还有 Kotlin)列为命令式语言，但它们也有一些更具声明性的特性，最著名的是注释和 DSL。

所以这就结束了“Jetpack Compose 的心理模型”的第一章，还有几章已经计划好了。如果你想知道更多，或者如果有任何意见，或者如果我有任何错误，请随时让我知道🤗