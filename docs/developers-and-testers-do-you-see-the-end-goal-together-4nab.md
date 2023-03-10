# 开发人员和测试人员，你们一起看到最终目标了吗？

> 原文：<https://dev.to/atapas/developers-and-testers-do-you-see-the-end-goal-together-4nab>

# 从前

自从 1960 年软件工程起源以来，有人设计原型，设计和开发原型，执行测试，向利益相关者演示，并发布供最终用户使用的产品。

[![vintage-computers.jpg](img/7661745a393956b318a155348ba227d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Oasky9_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565766806483/nI0z1VqmX.jpeg)

在软件工程的现代时代，这些角色变得更加复杂、分类、分支和描述。然而，基本原理是相同的，例如:

*   开发人员(很多情况下我们称他们为，*程序员*)是负责设计和实现解决方案的人。开发者使用*编程语言*开发软件程序。

并非所有的开发人员都真正参与了解决方案的设计。可能还有其他的角色，比如做这件事的架构师和实现解决方案的另一个开发人员(或程序员)。

*   测试人员是负责解决方案/产品/服务等质量保证的人。他们执行*自动化*和*手动*测试，以确保开发人员创建的软件符合目的。

如果存在任何缺陷(即问题)，他们会报告这些缺陷，以便在解决方案/产品部署到*最终客户(或用户)*之前，在解决方案/产品中移除(或修复)这些缺陷。

# 保持同步

有[个非常严重的例子](https://www.google.com/search?q=developer+tester+cartoon&rlz=1C1GCEU_enIN820IN820&source=lnms&tbm=isch&sa=X&ved=0ahUKEwiEoMbvnILkAhUVH48KHROXDSAQ_AUIESgB&biw=1280&bih=529)让我们相信，开发人员和测试人员不可能同步。这可能会导致误解，自我，你的事情对我的事情，只做我的工作这种副作用。

我个人最喜欢的是这一个(老笑话但是，意味深长):
[![Difference-between-Testing-and-Debugging.png](img/8ac78fd02b219d9560f48518d7ac4cf9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NHby5CFU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565781356938/JEzG8IqBh.png)

## 它们在哪里需要同步？

*   理解需求。整个故事的大部分。
*   理解人物角色。故事中重要的*人物*部分。
*   理解用例及场景。连接这些点的线。

这些都是来自开发人员和测试人员的非常基本的期望，理解并在同一页上，这样他们可以清楚地一起呼叫，

*   `6 as '6'`和`9 as '9'` = >同侧看。
*   `6 can be seen as '9'`和`9 can be seen as '6'` = >从对面看。

虽然以上理解有助于开发人员和测试人员达成共识，但是这还不够。同样重要的是，在整个项目中，针对每个发现的问题和要解决的问题进行集中而健康的对话。

[![critical_issue.gif](img/4a8f292d0bcd77243103ffdbca4175ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ENCzB1SU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565859333235/7VbTOlvCF.gif)

## 常见问题和模式

我在许多项目中看到了许多常见的问题和模式，它们可以很容易地避免，以获得更好的同步和结果。这里有几个，我觉得是这个问题的主要原因。我确信，还会有更多。

#### 过度依赖问题跟踪工具

问题跟踪工具是沟通和管理问题(即 bug)生命周期的最佳方式。为得出结论而做出的每个决定都必须记录在工具中。然而，这不应该是开发人员和测试人员之间唯一的交流方式。还有一个简单且平行的，就是*谈*。

[![talk_to_each_other.gif](img/309a9a34a30cd8dbbed3159d7369a02c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eYuNvj__--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565861906147/Me4lWLmwJ.gif)

在你的每日站立更新之后，进行侧边栏，互相交谈以意识到差距并继续前进。让我们不要仅仅因为你们不在同一页上就膨胀问题跟踪工具。节省能源，节省时间。请注意根据得出的结论更新工具。

#### 没有预先共享测试用例

通常，测试人员会编写各种积极的和消极的测试用例，这些用例来自于对用例的理解以及对手头问题的解决方案。我已经看到，测试人员通常在编写、管理、维护和执行测试用例方面做得非常出色。然而，我发现他们没有提前分享。

[![share.gif](img/15a47ffa37278e4750ab55b1f25fc097.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EYLvUbKa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565868212341/uCSpS_Y28.gif)

如果开发人员和测试人员事先知道一些与某个特性相关的常见测试用例，这将减少测试中出现问题和错误的机会。无论是测试人员分享还是开发人员主动查看那些测试用例，或者他们安排一个会议来讨论，结果将是节省时间，更少的错误和更早的发布。

#### 数量胜质量

我每天要开 3 期，每周至少 20 期，本期 230 期左右！如果我能去那里，我会觉得自己是个赢家，最重要的是，我的老板会很高兴！听着耳熟？

[![quality_quality.gif](img/69714da1130d558b62b5b390d935cf16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WGUWDDlc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565863149284/dNn7v8Imz.gif)

作为一名测试人员，如果我们中的任何一个人有这种感觉，现在是时候回顾并走出这种感觉了。*质量比目标更重要*，无论是你还是你的经理设定的数字。仅仅通过制造一些没有人愿意修复的错误并不能使产品变得更好，因为它们根本不重要。让我们回到质量上来，因为测试人员是负责*质量保证*的。

#### 反正测试人员是要测试的

我刚刚完成了我的编码，做了一个`git push`。这肯定会成功的。反正测试人员是要测试的！当问题被报告时，我可以修复它。

[![i_am_done.gif](img/b38b6eef5a72e5af1c5014ef87c12739.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P1IK6LyN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565863991477/mzQJr5J7m.gif)

这种想法并不那么受欢迎。作为开发人员，我们需要编写和执行单元测试，测试出我们将要提交的代码影响。此外，通知测试人员已经提交了什么，比您的提交消息更详细一点，这是非常好的。还记得*在同一页上的重要性吗？*

#### 自动化？谁拥有它？

测试自动化是产品整体测试策略中非常重要的一部分。但是谁拥有它呢？测试人员？特殊技能的专业人士？开发商？谁啊。

[![giphy.gif](img/88c34138afb7c57d9c298eb45c9e1ec7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---bglWO3I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565864645578/ysJh_Vv2I.gif)

在我看来，是整个团队。开发人员可以参与构建自动化需求，如框架、执行策略、代码等。测试人员在发布过程中使用、维护它。自动化是代码的两倍。随着我们编写更多的源代码，自动化代码必须被修改、增强并做得更好。

#### 团队中的团队

开发人员、测试人员是由于所需的技能和工作期望而产生的角色。scrum 团队通常将这两种角色的人结合起来。角色可以很多，但是团队的感觉一定是， ***一个*** 。

[![team_teams.gif](img/63ce79222382ae1fc96519d1a48198dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IXosKFrS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565865830914/UImcoKBoa.gif)

在团队中，开发人员和测试人员之间不应该有基于角色的生理界限。他们属于一个有着共同目标的团队。

# 结论:共同看到最终目标

随着时间的推移，开发人员和测试人员的角色定义也在发生变化。我们已经看到了各种类型的开发人员角色，如前端、后端、全栈等等。类似地，在测试、手动、自动化、性能等方面。

我们已经看到了对一个全新角色定义的新需求，叫做 [SDET](https://blogs.msdn.microsoft.com/seliot/2010/04/18/what-is-an-sdet/) ，代表测试中的软件开发工程师(或者测试中的软件设计工程师)。如果你更深入地了解 SDET，那就不仅仅是通常的开发人员和测试人员的角色了。

总之，这个故事的寓意是，从你自己对传统开发人员和测试人员的定义中走出来。拥有整个解决方案/产品。将最终目标和期望放在一起，相应地调整你的动机、计划和技能。

即使你的组织结构要求你是独立的，没有人能阻止你保持同步，成为一个高度协作的团队。

带着这样的想法，这就是这个故事的全部内容。如果帖子有用，请点赞/分享。我也很想听听你的经历。

[![endgoal.gif](img/ca007ebeb2355599fa23875088d555db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4O6zrkZQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1565860627580/4jzswS6UP.gif)