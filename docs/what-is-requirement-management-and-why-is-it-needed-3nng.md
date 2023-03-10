# 什么是需求管理，为什么需要需求管理？

> 原文：<https://dev.to/rlxdprogrammer/what-is-requirement-management-and-why-is-it-needed-3nng>

介绍

每当我妻子让我去买食物时，我都感到害怕。我担心我没有买到合适的食物，即使我尽力了。问题是，我去商店检查我的清单，第一点是“milch”。在这一点上，我没有机会满足要求。我不能简单地满足它们，因为它们不合适。多少牛奶？哪个牌子的？里面应该有多少脂肪等等。等等。我能怎么做呢？我可以给我妻子打电话。她没空...至少大多数情况下。然后让我们做一些假设，由两个最著名的品牌升。在 99%的情况下，这并不是我妻子所期望的...那么如何才能让它变得更好呢？我可以在离家前检查她的清单，并思考潜在的问题，所以只要问:“我们需要多少牛奶？”“你更喜欢哪个分支”等。注意她的回答。一旦我认为一切都清楚了，我就可以离开了。

当然，这还不是全部。有时我在店里时，她会打电话给我:“对不起，我忘了告诉你我们也需要糖”或者“我改变主意了，我做了别的东西”。这就是所谓的需求变更。

在其他情况下，我去商店，我意识到所有的牛肉都卖完了。我能做什么(尤其是我懒得去别的店)？打电话给她，向她宣布，并尝试提出替代方案，比如“没有牛肉，但有鸡肉吗？

因为我的目标是做一个好丈夫，所以我尽一切努力让我的顾客(柬埔寨妻子)开心。由于我也是一个懒惰的程序员，我尽量避免双重工作，在这种情况下，我需要再次回到商店。

这就是所谓的高层次的需求工程:试图理解客户的期望，以一种清晰、明确的方式记录它们，考虑关键情况，在不确定的情况下提出问题，并跟踪变更请求。作为一名需求工程师，你是客户和程序员之间的传输层，客户通常在技术上并不深入。你需要把客户的语言翻译成程序员的语言。在一些公司，这是一个专门的角色，但在一些结构中，这也是开发人员的任务。

# 功能性和非功能性需求

有两大类需求:功能性需求和非功能性需求。功能需求告诉我们关于功能的信息:软件应该做什么。大多数需求都是功能性的。

非功能性需求例如是定义环境(支持哪些操作系统，需要安装哪些其他程序)或硬件需求(最小 RAM 等。).

# 怎样才是好的要求？

一个好的软件需求是明确的、清晰的、易于理解的、可测试的、原子的(不能被分割成多个需求)并且独立于实现。实际上，你应该避免使用复杂的短语，使用不带“和”和“或”的简单句子，并且总是站在用户的角度思考。这意味着不要写“数据库层应该做…”。用户对数据库层有所了解。需求应该真正写在用户的层面上。通常情况下，需求是当前模式，并通过使用世界应该和应该来建立。示例:_ "通过单击加载按钮，应该会打开“打开文件”文件浏览器窗口，"_

这也是很好的，如果你是一个高层次的测试用例，如何测试你的需求。这样更容易理解。

# 如何记录需求？

需求需要以版本控制的格式记录下来，所有的变更都可以被存储和追踪。每一个需求都需要有一个标识符，这样它就可以与测试和设计/实现联系起来。需要对需求进行排序。一个好的策略是根据特性对它们进行排序。通过阅读需求，读者需要对软件有一个总体的了解，所以需求需要以结构化的方式来编写。

有几个软件是专门为维护需求而设计的，但是它们甚至可以存储在一个简单的文本文件中。

需求文档的某些版本需要与客户共享并逐点讨论。

# 如何处理需求变更？

需求变更也需要以下列格式记录下来:什么变更了什么，什么时间，变更的目的是什么，在哪个软件版本中需要变更相关的功能。通过这种方式，您可以避免与客户进行大量讨论。

# 要求敏捷？

在大多数经典的敏捷过程中(Scrum、看板等)。需求工程不是过程的一部分，需求被记录在用户故事中，并且它们可以被任何后来的用户故事覆盖。然而，在这样的环境中，也可能有一个集中的需求集合，并且在复杂系统的情况下，它可以长期帮助很多。

# 总结

当然，需求工程是一个复杂的话题，这篇短文无法深入。有些人也认为需求工程是过时的，但是根据我的经验，如果你和客户一起工作，它是非常有用的，它会让你的生活变得更加容易。