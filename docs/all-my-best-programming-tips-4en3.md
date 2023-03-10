# 我所有最好的编程技巧

> 原文：<https://dev.to/jasonswett/all-my-best-programming-tips-4en3>

以下是我能想到的所有值得分享的编程技巧的列表。

这些技巧中的许多似乎是显而易见的，不言而喻，但是这些技巧中的每一个都在这里，因为我见过程序员在多个场合没有利用这些技巧，即使是非常有经验的程序员。

我将这些技巧分为两部分:开发技巧和调试技巧。我没有按特定的顺序排列它们。

## 发展提示

### 明确表达目标

在你坐下来开始打字之前，清楚地写下你想要完成的事情。这将帮助你避免走上随机的道路，这会降低你的工作效率。它也将帮助你在小的、完整的工作单元中工作。

### 把大工作分解成小工作

有些工作看起来既庞大又模糊，以至于难以处理。例如，如果我的任务是创建一个包含定期约会的日程，我该从哪里开始呢？如果我从一个较小的工作开始，例如，考虑如何精确地指定和编码一个循环规则，这可能会使我的工作更容易。

### 让一切一直工作

重构是在不改变程序行为的情况下改进程序结构的行为。我见过很多这样的情况，程序员试图执行重构，却没能改变他们试图重构的代码的行为。他们最终不得不扔掉他们的工作，重新开始。

如果在重构的过程中，我破坏或更改了一些现有的功能，那么我必须想办法让代码恢复原来的行为。这很难。

或者，如果我注意不让代码偏离它的原始行为，我就不必担心带回任何被改变的东西。这个相对容易。我只需要在非常小的单元中工作，并在每个小变化结束时进行回归测试。

### 不断传递

连续交付是使您的程序始终处于可部署状态的实践。

假设我在 1 月 1 日开始一个项目，它的发布日期是 7 月 1 日。但是，出乎意料的是，领导层在 3 月 1 日找到我，告诉我发布日期现在是 3 月 10 日，届时我们将使用我们拥有的任何产品。

如果我一直在练习连续交付，3 月 10 日发布没什么大不了的。在每周结束时(每天结束时，甚至每小时结束时)，我都确保我的应用程序 100% *完成*，即使它可能不是 100% *完成*。

### 一次只做一件事

完成一半的任务总比完成一半的任务好。

如果我 100%完成了 8 个特性中的 50%,我可以部署 4 个特性。如果我完成了 8 个特性的 50%和 100%,我可以不部署任何特性。

此外，开放式工作耗费精神带宽。即使你相信在不同的任务之间跳跃和一次只专注于一件事一样快，有 5 个球在空中比只有一个球在空中花费更多的精神代价。

### 使用自动化测试

所有的代码最终都会被测试，这只是一个时间、方式以及由谁来测试的问题。

与将一个特性发送给 QA 人员，然后由于 bug 而被踢回去相比，在开发过程中通过自动化测试发现 bug 要便宜得多，也快得多。

测试有助于防止引入新的错误，有助于防止退化，有助于改进代码设计，有助于实现重构，并通过作为文档来帮助理解代码库。

### 对事物使用明确的名称

当一个[变量名由于用词不当而不清楚](https://www.codewithjason.com/variable-name-anti-patterns/)或者由于缩写成混淆而不清楚时，它增加了不必要的心理摩擦。

不要缩写变量名，除了普遍理解的缩写(如 SSN 或 PIN)或超局部临时变量(如`records.each do { |r| puts r }`)。

给事物命名的一个好规则是:事物是什么就叫什么。

### 不要过早地优化

肯特·贝克有一句名言:“让它工作，然后让它正确，然后让它快速。”

对于我所做的绝大多数工作，我甚至不需要达到“快速完成”这一步。除非你正在为一个高知名度的消费者应用程序开发高可见性的特性，否则性能瓶颈可能要到特性最初部署后几周或几个月才会出现。

### 一次只写一点代码

我有一句话反复对自己说:“永远不要低估你把事情搞砸的能力。”我经常惊讶于一个“绝对”有效的小改变经常不起作用。

只能以微小的增量工作。在每次变更后测试你正在做的事情，最好使用自动化测试。

### 使用原子提交

原子提交是只“关于”一件事的提交。

原子提交的价值至少有两个原因。第一，如果您需要回滚一个提交，因为它引入了一个 bug，那么您不必回滚混合在同一个提交中的其他不相关的(并且非常好的)代码。第二，原子提交比非原子提交更容易查明 bug 的引入。

一个独立但相关的想法是使用小提交，这只是“一次只写一点代码”的一个具体应用。

### 不要让自己停留在原地

陷入困境是最糟糕的状态。沿着错误的道路前进(违反直觉)比停滞不前更有成效。沿着错误的道路走下去，激起新的思想和新的信息；站着不动不会。

想不出好的前进方式，就用不好的方式前进。您总是可以在以后恢复您的工作，特别是如果您使用小的、频繁的、原子的提交。

如果你想不出继续前进的方法，试着准确地说出你到底困在哪里，为什么困在那里。有时候，正是这种行为帮助你摆脱困境。

如果你认为你陷入困境的原因是缺乏一些必要的知识，去谷歌搜索一下。看一些相关的博文和书籍段落。贴一些论坛问题。在一些松弛小组中询问一些人(如果你不在任何技术松弛小组中，加入一些)。出去走走，然后回来再试一次。如果其他方法都失败了，把问题放在一边，转而做其他事情。但是无论你做什么，不要只是坐在那里思考。

## 调试提示

### 清楚表达问题

如果你能准确地说出问题是什么，你就已经朝着解决问题的方向前进了一大步。相反，如果你不能清楚地说出到底是什么问题，你的情况几乎是没有希望的。

### 不要陷入逻辑谬误

调试中最大的危险之一是欺骗自己，认为自己知道一些实际上并不知道的事情。

如果在你调查的过程中，你发现了一个坚如磐石的事实，把它写在你认为是真实的事情清单上。

另一方面，如果你发现某件事*看起来*是真的，但你没有足够的证据 100%确定它是真的，不要把它作为真理写下来。把它作为一个假设写下来是没问题的，但是如果你把假设和事实混为一谈，那么你很容易被弄糊涂，浪费一些时间。

### 偏爱孤立胜于理智

我知道有两种方法可以确定 bug 的原因。一是我可以研究代码，进行实验和调查，直到我找到问题所在的代码行。另一种方法是我可以分离出 bug。后者通常要快很多倍。

我最喜欢的隔离 bug 的方法之一是使用`git bisect`。一旦我找到了引入 bug 的提交，通常就很容易看出提交的哪一部分引入了 bug。如果不是，我通常会通过执行`git revert --no-commit`违规提交来否定违规代码，然后一点一点地重新引入违规代码，直到找到罪魁祸首。这种方法几乎从未失败过。

### 比起思考，更喜欢搜索

精神能量是一种宝贵的资源。不要在别人已经解决的问题上浪费精力，尤其是当解决那个特殊的问题对提高你的技能没有什么帮助的时候。

如果你运行一个命令，并得到一个神秘的错误，不要坐在那里眯着眼睛看它。将错误信息复制并粘贴到 Google 中。

为自己考虑当然有它的时间和地点。深刻理解事物也有它的时间和地点。在什么时候你应该停下来理解你所看到的，什么时候你应该尽可能轻松地解决问题，这样你就可以继续你的计划工作。

### 学会为错误信息制定良好的搜索查询

假设我运行一个命令，得到以下错误:`/Users/jasonswett/.rvm/gems/ruby-2.5.1/gems/rspec-core-3.8.0/lib/rspec/core/reporter.rb:229:in require': cannot load such file -- rspec/core/profiler (LoadError)`

我应该谷歌错误的哪一部分？

如果我把整个东西粘贴到谷歌，我的电脑、我的 Ruby 版本和我的`rspec-core`版本独有的`/Users/jasonswett/.rvm/gems/ruby-2.5.1/gems/rspec-core-3.8.0`，缩小了我的搜索范围，以至于我甚至得不到一个结果。

在另一个极端，`require': cannot load such file`太宽泛了。许多不同的错误消息可能包含该文本。要复制/粘贴的错误消息的敏感部分是`require': cannot load such file -- rspec/core/profiler (LoadError)`。具体到有希望匹配到其他类似问题，但又没有具体到不会返回任何结果。

### 学会概括地制定好搜索查询

我不知道如何表达什么是好的搜索查询，但我的尝试是这样的:一个尽可能短的查询，同时还包含所有相关的搜索词。

### 不要太相信错误信息

每当我得到一个错误消息，我几乎从来没有注意它说了什么，至少第一步没有。

当我得到一个错误消息时，我会查看行号和文件。然后，我查看行号，看看是否有明显的错误。大概 90%的时间都有。

其余 10%的时间，我将返回到错误消息并阅读它，看看它是否提供了任何线索。如果是的话，我会做些改变，看看是否能解决问题。如果错误太隐晦，以至于我不知道该怎么做，我会用谷歌搜索错误信息。

### 使终端窗口变大

如果我每帮助一个试图用便利贴大小的终端窗口调试问题的学生一次就能得到一美元，我就成了一个富人。最大化窗口，这样你就可以清楚地看到你在做什么。

### 关闭不需要的标签页

打开标签耗费精神能量。在稍后重新找到页面的成本和被所有这些打开的标签弄得头脑混乱的成本之间，稍后重新找到页面要便宜得多。平均来说，我自己通常在任何时候都会打开两个标签页。