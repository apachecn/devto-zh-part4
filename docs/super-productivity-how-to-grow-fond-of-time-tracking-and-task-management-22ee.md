# 超级生产力:如何变得喜欢时间跟踪和任务管理

> 原文：<https://dev.to/johannesjo/super-productivity-how-to-grow-fond-of-time-tracking-and-task-management-22ee>

## 简介

[超级生产力](https://super-productivity.com/)是一个[开源](https://github.com/johannesjo/super-productivity)时间追踪/任务组织工具。在接下来的文章中，我想分享我如何使用这个应用程序，让这些任务变得简单一点，至少对我来说，更有趣一点。

你可以[在它的网页上找到更多信息](https://super-productivity.com/)，[在浏览器中亲自尝试](https://app.super-productivity.com/)或者[前往 github 亲自破解](https://github.com/johannesjo/super-productivity)。

该应用程序的功能包括:

*   **完全控制您的数据**，无跟踪、无用户账户、无云
*   子任务
*   时间跟踪
*   性感的全球书签栏添加快速链接，项目相关文件，甚至命令和应用程序
*   带有自定义提醒的性感笔记功能
*   计时器番茄
*   “休息一下”提醒
*   全键盘交互
*   主题不同！
*   移动支持
*   日常杂务的可重复任务
*   可配置和自动化的吉拉集成，适用于:
    *   从 jira 中搜索和添加任务
    *   为您的 jira 票证创建(本地/个人)子任务
    *   将过渡设置为“进行中”或“完成”
    *   一旦列表中的某个问题发生变化或在 jira 上被评论，就会自动通知= >不再需要杂乱的电子邮件通知
*   可配置和自动化的 Git 集成，适用于:
    *   从 git 中搜索和添加任务
    *   一旦列表中的问题发生变化或被评论，系统会自动通知您
*   将你的时间表导出到 Google Sheets(当然是可选的)
*   在多个实例之间同步数据(当然是可选的)

## 我的日常

(虚构，但受现实启发)

### 制定早上的基本计划

早上的第一件事是打开我所有的标准应用程序，按照我习惯的方式排列它们([我还为那个](https://github.com/johannesjo/linux-window-session-manager)构建了一个小工具)。然后我切换到超级生产力，开始在一个叫做“组织”的通用任务上跟踪时间，准备计划我的一天。

[![first task](img/8d4a537bca3fa22597c3ca976b575a1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--79pxii9X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xfbeeifwm4r4jcj6ott5.png)

如果我现在没有要担心的任务，我就会去吉拉 Scrum 板看看是否有我可以处理的任务。当我找到一个或多个任务时，我回到超级生产力，在“选择或创建任务”栏中输入任务，然后按回车键。

接下来，我倾向于把我想做的第一个任务分成小块。当一个任务被聚焦(由粉色轮廓指示)时，您可以按下`a`，或者点击任务最右边的三个小点，从菜单中选择“添加子任务”。在添加子任务时，我可以粗略估计一下每个步骤需要多少时间。我可以简单地通过输入“我的任务标题 1h”来分配一个小时或“其他 30m”来分配 30 分钟。

[![estimate short syntax](img/3f6c315aeae1ce3445d010ad97bc1e7c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bPTnB7FD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2l6g64tcjltzh9m6l7qu.png)

在任务列表的上方有一个栏，它给你“估计剩余时间”，“今天工作”花费的时间和你“没有休息”工作的时间，让你快速了解你现在的状态。

在那之后，我会去参加每日站立会议，通常会持续很长时间。我一回来，超级生产力就亲切地问我，我离开的这段时间该做些什么:

[![idle](img/03930671f683d3a24d8f90c581756d8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZR0NhHKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xtehwkwduntlm1w4ac8i.png)

我选择我的“组织”任务，然后单击“任务”按钮。是时候研究这个新特性了！

### 白天

#### 吉拉通知

随着我对技术需求有了更深入的理解，我倾向于在一天中对原始计划进行一些改进。添加子任务可以通过按下`a`来完成，如果主任务被聚焦，你可以使用`Shift+A`作为快捷键从任何地方添加新的主任务。

设计师 Marc 告诉我，我们绝对需要支持书签的自定义图标，这在用户故事中根本没有提到，但这就是我们在这里的做法！他答应我把新图标贴在吉拉的票上。值得庆幸的是，当你的每日待办事项列表中的一张吉拉票有更新时，Super Productivity 会提供通知，并立即下载附件，所以我不需要担心查看数百封吉拉电子邮件通知。

[![jira update](img/c808fdd07787a1d9acd16abcec533e9f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WMFVCHbT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/70seeh228hxstu1lzofw.png)

#### 休息时间

我感到有点压力，因为我答应了我的项目经理下午准备好这个专题。特别是当我压力大的时候，我会忘记休息，当我压力大的时候，休息对我来说特别重要，因为我有时需要获得一些观点。这就是另一个特性的用武之地:休息提醒。

[![break](img/8742d4274be96c58ea5cd8b41c67aeff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H9oyiZVa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rlaqjc7cg1xmt3v7lt5q.png)

我决定做我的 5 分钟背部练习。我不再年轻了，我的右肩因为像疯子一样过度推搡鼠标和打字而有点疼。我按下`Shift+N`打开注释面板，查看放在那里的图片上的练习。

做这个练习感觉很好，尽管我的同事像往常一样取笑我。感觉不那么紧张了，我决定通知我的项目经理可能会延迟，并继续完成任务。

回来后，应用程序再次问我如何打发空闲时间。我选择“任务+休息”,它跟踪任务的时间，同时重置休息提醒计时器。我觉得我不应该因为做了一些事情而惩罚自己，这些事情实际上对我的工作效率有好处。

### 回顾当日

现在是五点半。该收工了，今天到此为止。我点击“完成一天按钮”。我填写评估表，因为我喜欢统计学，这有助于我反思这一天。给自己一些信任是一个很好的习惯，虽然没有人会在意你是如何激活这些删除按钮的，但你这样做是对的。你不必这样做，因为应用程序中的大多数东西你可以在设置中关闭评估。

[![evaluation](img/ee698b962e601f6e6574f7965762c39a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ikb25HFf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gcpsoprg2j7i1q18d08b.png)

之后，我会回顾“今天”完成的任务。有一个方便的“花费时间整数”按钮，可以让你把所有模糊的花费时间数字转换成更容易挖掘的五分钟、四分之一、半小时或一整小时的数字。

感谢上帝，今天是星期五！这也意味着我必须把我每周的时间表发给和蔼可亲的会计甜甜。我转到“周”选项卡，单击“导出周数据”。我通过电子邮件发送文件。终于到了“存钱回家”的时候了。