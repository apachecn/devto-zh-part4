# 为什么我要建造萨吉飞

> 原文：<https://dev.to/heiskr/why-i-m-building-sagefy-2jfp>

## 挑战

技术最大的机会是人类的学习。通过提升自己，我们可以加速进步和成长。我希望[萨吉菲](https://sagefy.org)展示一种不同的方式，我们可以用技术来思考学习。

我们可以解决的最重要的学习挑战是**确保我们调整内容以适应学习者当前的知识和技能状态，以及他们的学习目标。当内容不完全符合他们先前的知识时，学习者会浪费时间和精力。如果内容涉及到学习者还不知道的东西，或者如果内容重复了学习者已经熟知的信息……那会浪费时间和精力，降低积极性。如果内容超出了学习目标，学习者就会失去注意力。**

此外，我们希望确保学习者*不受主题*的限制。我们当前的许多教育工具让知识付费播放。我们也非常依赖专家为学习者创建精选的(有限的)内容。在开放的系统中，有更多样和更深入的内容，学习者没有清晰的路径可循。

我们完全有理由希望每个人都以最省时省力的方式追求知识。我们希望鼓励每个人尽可能深入地追求自己的兴趣。

## 萨吉菲是什么

[**Sagefy**](https://sagefy.org) **是一个开放内容的自适应学习系统。**

[***开放内容***](https://en.wikipedia.org/wiki/Open_content) 表示任何人都可以查看、共享、创建和编辑内容。维基百科可能是最广为人知的例子。开放内容意味着 Sagefy 可以快速发展，并覆盖当前教育技术无法覆盖的学习科目。

[![An example of the flow of consensus-based decision making. [https://upload.wikimedia.org/wikipedia/commons/e/ed/Consensus-flowchart.png](https://upload.wikimedia.org/wikipedia/commons/e/ed/Consensus-flowchart.png)](img/95c282ca05bf7a4aff90a45c077e432a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SngUhSAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-2.png)

像大多数开放内容系统一样，Sagefy 基于一个 [**基于共识的决策**](https://en.wikipedia.org/wiki/Consensus_decision-making) **制定过程**来工作。基于共识的决策意味着不再依赖单个专家来创建内容或批准更改，而是由大量学习者自己创建内容。基于共识的系统倾向于产生和专家一样高质量的内容，但是规模要大得多。

[![In an adaptive learning system, instead of a structured course, the material will adjust and flow based on learner knowledge.](img/4f06bb8e3f906866452c3d7514298d75.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3v4TK_hR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-3.png)

一个 [***适应性学习***](https://en.wikipedia.org/wiki/Adaptive_learning) 系统试图基于学习者已经知道的东西和学习者的目标来优化学习体验。因此，Sagefy 根据学习者的反应呈现小块内容*，而不是计划好的结构化课程内容。通过将经验建立在先前的知识和目标的基础上，Sagefy 希望最大限度地利用学习者选择的任何主题所花费的时间和精力。*

我们可以通过以下方式确保内容与学习者的知识和目标相匹配…

*   评估学习者当前的知识。
*   跳过学习者已经熟知的内容。
*   当学习者不知道先决条件时进行备份。
*   当学习者需要复习时，重复内容。
*   专注于针对学习者目标的内容。

目前，我将 Sagefy 定位于*独立成人学习者*。这一目标受众使得 Sagefy 更简单，因为它不涉及机构整合的实际问题。年轻的学习者倾向于在教室环境中，这带来了期望和挑战，使 Sagefy 更难建立。这些挑战包括参与要求、预设课程要求、测试和评分、减少作弊和教师分析。机构环境中的成人学习者也面临类似的实际挑战。通过关注内在动机的学习者，我至少在最初跳过了许多更传统的课堂和培训场景中更实际的问题。

*Sagefy 完全是***开源并免费提供。我的动机是更广泛、更有效、更专注地分享知识。我对盈利不感兴趣。金钱只是一种资源。通过消除对利润的担忧，我可以专注于让 Sagefy 成为我所能想象的最好的学习平台。**

 **开放内容*、*自适应*和*自由*的组合以一种其他学习技术无法比拟的方式占据了优势。这种结合意味着 Sagefy 可以接触到各种各样的学习者，同时试图提供一个深入和有效的学习体验。

## Sagefy 如何工作

[![The three types of entities in Sagefy.](img/96fc6d441b3f5595fe7915f0b52065a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Bf2r94Ng--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-4.png)

***萨吉菲中事物的种类。***Sagefy 中有三种类型的实体:卡片、单位、主体。

[![A card in a single learning activity. In this example, this is a multiple choice question card.](img/d67c3d1d1e0b9d6f0098fe440a6c2ccb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xn0KG4q5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-5.png)

**卡**。卡片是一个单一的学习活动。示例包括一个短视频、一个多项选择题或一道数学题。一个示例问题是，“5、7 和 10 的中位数是多少？”卡片是 Sagefy 数据结构中最小的实体。一张卡总是属于一个单元。

[![Many cards belong to a single unit.](img/ccdf0fd9ed144020710980601e3aaaf2.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--ROvq87YW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-6.png) [ ![A unit represents a single learning goal. In this example, the unit is “describe the differences between mean, median, and mode.”](img/2ca745d2d36a6ebcec9ec6914c412fa9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SG48l95T--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-7.png)

**单位**。一个单元就是一个学习目标。一个单元类似于一节短课。例如，“描述均值、中值和众数之间的差异。”单元是 Sagefy 数据结构中的中等大小的实体。一个单元有许多卡片。一个单元可以在它之前要求其他单元。一个单元属于多个科目。

[![Units can require other units.](img/ab8643282b92430da6100dfa887fcada.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uVVSp6N0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-8.png)[![Many units belong to many subjects.](img/0d7112a1af59764f022238a78450a530.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7G4ZddoH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-9.png)[![A subject is a combination of units and other subjects. A collection of units on central tendency might make up one subject, which is part of a larger subject on descriptive statistics.](img/5923c54fb0a72fc581b0e376baba1414.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z162NF_M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-10.png)

**主题**。主题是单元和其他主题的集合。科目就像班级或课程，从短期研讨会到整个学位课程。例如，“集中趋势的度量”、“描述统计学”或“第一统计学课程”。主题是 Sagefy 系统中的大尺寸。一个科目包含许多单元和其他科目。

***学习者的经历。*** 对于学习者来说，Sagefy 是一个简单、专注的流程，允许在过程中进行一些选择。

1.  注册后，学习者找到自己想学的科目。
2.  然后，一旦开始该科目，学习者将看到代表其学习目标进度的单元树。
3.  接下来，学习者可以选择一个单元，并自动推荐一个选项。
4.  学习者以视频、音频、书面页面、多项选择题、简短答案等形式参与到内容中，直到学习者学会该单元。
5.  最后，学习者继续选择单元并学习它们，直到整体学习目标完成。

[![Choosing the “An Introduction to Electronic Music — Foundation” subject.](img/887d6c8fd821c1f55af53b3158884d4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zdviqssF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-11.png)[![The learner’s progress towards the subject, in the form of a tree of units.](img/0cdbe372617f0102cb89f7b19bac3efa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cDQmubpO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-12.png)[![The learner chooses a unit.](img/bc7c8beb519b1ab56cdfca17031aa682.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oN9FovHY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-13.png)[![The learner engages with the content until the learner has learned the unit. Sagefy shows the learner’s progress in a bar across the bottom of the screen.](img/1027be9539634cdc1540fb1134314494.png)T11】](https://res.cloudinary.com/practicaldev/image/fetch/s--y1PminlX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-14.png)

***内容创建过程。*** 大多数开放内容网站标榜“任何人都可以编辑”这是一个真实的陈述。然而，这并不意味着你应该去维基百科，在一个受欢迎的页面上做一些修改，然后一分钟后仍然看到这些修改。相反有一个隐含的过程:

1.  转到对话页面，描述你想要做出的改变。
2.  与那些处理内容的人建立共识以做出改变。
3.  解决任何不同意见。
4.  改变你的内容。

讨论是任何共识驱动过程的固有部分。所以对于 Sagefy 来说，不是允许直接编辑，内容过程需要讨论。除了主题本身，还有什么地方可以张贴呢？

因此，要创建一个新的单位，比如说“描述平均值、中值和众数之间的差异”，Sagefy 中的过程如下所示:

[![An outdated screenshot. A proposal with enough votes approving will update the entity automatically, focusing the content creation process on discussion. You can also see Sagefy is still rough around the edges.](img/aa46ff3a616fe576fda7e89454204fc4.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--7fCT4kYS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-15.png) [ ![An outdated screenshot. When you create a topic or post, you can choose between a regular post, a proposal, or a vote.](img/c3d3599a834f23c1662f33691315b0b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ehX4wYwJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-16.png)

1.  去相关主题的讨论区，做一个叫做*提案*的帖子。
2.  通过填写表格描述您想要添加的内容。(所以没有开销。)
3.  召集足够多的人同意你的提议。他们可以使用*投票*帖子类型来完成。
4.  如果有人投票否决了你的提议，你可以和他们一起解决分歧，或者尝试创建一个不同的提议。
5.  当有足够多的赞成票，几乎没有反对票时，改变就会自动发生。([具体的数学我已经记录下来了](https://github.com/heiskr/sagefy/wiki/Planning%3A-Contributor-Ratings)。)
6.  Sagefy 记录了对每张卡片、单位和受术者所做的和提议的所有改变。

因为 Sagefy 是一个自适应学习系统，内容创建者可以获得不同内容在真实学习者身上表现如何的统计指标。希望这些统计数据能帮助我们改进内容和实验，找到教学机会。

***自适应学习过程。*** 我们知道[一对一学习](http://www.npr.org/sections/health-shots/2015/09/08/438592588/one-tutor-one-student-better-math-scores-less-fear)往往具有最高的有效性。为什么？因为从学习者到指导者有一个即时的反馈。学习者已经知道主题了吗？向前跳。遗漏了什么？让我们后退。无聊？我们换个话题吧。这种互动很容易一对一进行，但当每个导师有多个学习者时，基本上是不可能的。然而，技术使得这种交互具有广泛的可扩展性。

如果你对我到目前为止已经建立和计划的细节感兴趣，[我有文档记录](https://github.com/heiskr/sagefy/wiki/Planning%3A-Sequencer)。我试图想出一些简单、易于计算、在服务器上运行速度快的东西，同时又足够通用，可以处理各种各样的主题。我希望有比我更聪明的人出现，并找到比我更好的方法来做这件事，但我所拥有的足以开始展示这个概念。

## 萨格菲的想法

在花了几年时间阅读关于教育研究的书籍和文章后，我得出的结论是，有七个主要观点反复出现:

1.  一次做一件事。
2.  设定并适应目标。
3.  适应学习者的先验知识。
4.  建立图表。
5.  深入探究，超越死记硬背。
6.  将学习经历与现实生活中的例子联系起来。
7.  让学习者一起学习。

[![“Do one thing at a time.” Sagefy tucks away other options behind a menu, so the learner can solely focus on the single task at the time.](img/7f38620a43154cc4aaddebf9434a27d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P7ZNrzZ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-17.png)

一次只做一件事。 [一心多用是一个广为流传的神话](http://www.apa.org/monitor/oct01/multitask.aspx)。当我们认为我们在同时处理多项任务时，实际上我们在快速地切换环境。多任务处理增加了完成单个任务的时间。我们也不能一次学习一个以上的概念。最好一次只专注于学习一个概念，或者一个概念的整合。“做一件事”也意味着减少杂乱，把手头任务之外的一切都收起来，集中注意力。

[![Sagefy shows how the content creators organized the information, in the form of a tree of unit requirements.](img/b5ee529b223d9e1fe0a9dc23615cfbad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VQ6q8WOv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-18.png)

设定并坚持目标。焦点又来了。当我们向学习者展示他们目标之外的内容时，学习者就会失去动力。有时我们错误地这样做，有时我们出于对主题的热情这样做。无论哪种方式，通过清楚地阐明学习者的目标，学习者知道内容的哪些部分是主要的，哪些部分是补充的。

**适应学习者的先验知识。**这个想法是 Sagefy 最关注的一个。到目前为止，我在这篇文章中反复提到了这个想法。如果我们让学习者学习他们没有必备知识的材料，他们就没有地方来适应新的信息。如果学习者已经知道了材料，学习者会感到厌烦并失去动力。通过优化先前的知识，我们可以减少学习新事物所花费的时间和精力。[下面是](https://www.nap.edu/read/9853/chapter/6#78) [一些](http://rer.sagepub.com/content/69/2/145.abstract) [研究](http://onlinelibrary.wiley.com/doi/10.1002/tea.3660160403/pdf) [资源](https://www.nap.edu/read/10019/chapter/6#83)供进一步调查。

**建立图表。**我们的学习系统不仅通过吸收新材料来工作，还通过将现有知识与新知识联系起来来工作。因此，展示新信息如何适应学习者现有的学习模式是至关重要的。没有这个组织图，学习者很难有效地存储新信息。

[![By showing the goal to the learner, we help create focus and motivation.](img/215f7a0f362c85bb935f24129f5680e6.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--TL5qWt-9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-19.png) [ ![A variety of card types allows for content creators to both ‘dive deep’ and connect with real-life examples.](img/702ed2c0f6a6e6430b54743a448f297b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dIq-7fZ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-20.png)

深入探究，超越死记硬背。期待一个刚刚记住了身体不同关节名称的人立即进行膝盖手术是愚蠢的。光知道信息是不够的。我们还需要知道这些信息与其他信息之间的关系，以及如何将这些信息应用到各种环境中。那些是不同的知识。期望学习者在第一次看到信息后就记住一些东西也是不够的。学习者需要定期复习信息以保持强大的记忆力。

**将学习经历与现实生活中的例子联系起来。**我们中有多少人上过数学课，老师深入讲解抽象、证明等。却没有解释这个概念是如何相关的？那是这个原则的反面。当我们向学习者展示材料的相关性，并与他们已经知道的有形的东西建立联系时，学习者会更快地将材料整合到他们的记忆中。

让学习者一起学习。通过共同学习，我们可以避免知识上的差距。此外，我们可以与具有类似先验知识水平的人一起工作，以形成知识，特别是关于我们如何将该知识应用于不同的背景和不同的理解。在这里的想法中，这个想法最微妙。在加入一群学习者之前，一个先前知识很少的学习者通常会从单独的学习中受益。除了一起创作内容，我还没有想出一个好办法将社交学习整合到 Sagefy 中。但是，社会学习是一个我想在未来继续探索的话题。

在这些问题上没有广泛的异议，尽管有一些细微的差别。尽管这些观点在文献中反复出现，但我们当前的许多学习系统并没有充分利用这些观点。

## 是什么让 Sagefy 与众不同

在这一节中，我将讨论一些现有的学习系统，以及 Sagefy 与它们的比较。

[![The classroom suffers from both a lack of scale and adaptability. [https://commons.wikimedia.org/wiki/File:Andrew_Classroom_De_La_Salle_University.jpeg](https://commons.wikimedia.org/wiki/File:Andrew_Classroom_De_La_Salle_University.jpeg)](img/16c405110b7f55e7706284cedb208bc6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xhk5YSO---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-21.jpeg)

[***课堂学习***](https://en.wikipedia.org/wiki/Classroom) ***。*** 当我们想到教育，首先想到的是课堂。教室可以是一个学习、与其他学习者互动和充分参与内容的好地方。最好的班级激励和鼓舞人心。可惜很多班都不符合这个吧。由于有如此多的学习者，很难创建一个系统，使其内容紧密适应学习者的知识和目标。无论如何，教室也不是免费的，这限制了它的范围。一些老师做得很好，他们保持专注，展示例子，展示组织，让学习者一起学习，并深入材料。但并不是所有人都这样。可能不是大多数。

[![A screenshot of Moodle, an open-source learning management system. Designed to support in person classes, but sometimes used to teach a course by itself. [https://commons.wikimedia.org/wiki/File:1_MyHomeExample.png](https://commons.wikimedia.org/wiki/File:1_MyHomeExample.png)](img/f99918d1589eaa92586a7b7155b6c09b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vKJrtjTu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-22.png)

[***学习管理系统***](https://en.wikipedia.org/wiki/Learning_management_system) ***。学习管理系统，简称 LMS，是最常用的教育技术。学校大概是使用[黑板](http://www.blackboard.com/)最频繁的。该技术的目的是支持或取代课堂体验。LMS 的功能几乎完全是围绕课堂模型驱动的。因此，课堂上的大多数相同问题也适用于 LMS，包括规模和适应性。大多数 LMS 不是自适应的。 [SmartSparrow](https://www.smartsparrow.com/) 是 LMS 的一个例子。***

[![A screenshot from Cognitive Tutor, a current adaptive learning system. The interactions it can produce are surprisingly rich and detailed. That said, it takes a long time to produce content for the system. [https://www.carnegielearning.com/learning-solutions/software/cognitive-tutor/](https://www.carnegielearning.com/learning-solutions/software/cognitive-tutor/)](img/754f7a8f549e1bb9141fa2eb7d38fd91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kkqTuTih--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-23.png)

[***当前自适应学习系统***](https://en.wikipedia.org/wiki/Adaptive_learning) ***。*** 当前的自适应学习系统将自适应学习算法与专家创建的内容配对。通常，这些系统支持课堂教育或培训。自适应能力与为给定主题创建高度定制的内容的能力相结合，意味着这些系统目前为学习者提供了一些最佳的课堂体验。也就是说，建立一个适应性学习系统需要时间和努力。我不知道这些系统中有任何支持开放内容或免费供公众使用的。我也不知道有任何关于学习者群体的方案。这方面的一些例子有[认知导师](https://www.carnegielearning.com/learning-solutions/software/cognitive-tutor/)、[阿列克斯](https://www.aleks.com/)、 [LearnSmart](https://www.mheducation.com/highered/platforms/learnsmart.html) 和 [Knewton](https://www.knewton.com/) 。

[![Screenshot from Khan Academy, a massively open online course. Here we see a ‘blackboard’ style video explanation. Some of this content is best in class. [https://commons.wikimedia.org/wiki/File:Khan_Academy_%282012%29._Transfer_Pricing_and_Tax_Havens.webm](https://commons.wikimedia.org/wiki/File:Khan_Academy_%282012%29._Transfer_Pricing_and_Tax_Havens.webm)](img/a3514a2cb12e5a76df2e05ccff1347b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6Tx9xlm4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-24.png)

[***海量开放网络课程***](https://en.wikipedia.org/wiki/Massive_open_online_course) ***。大规模开放的在线课程为大量受众提供免费或低成本的 LMS 体验。几个例子就是[可汗学院](https://www.khanacademy.org/)、 [Coursera](https://www.coursera.org/) 、 [Duolingo](https://www.duolingo.com/) 、 [EdX](https://www.edx.org/) 。MOOCs 的规模很大，但仍然依赖于专家创造的内容，而且通常适应性不强。有些有一些自适应或类似自适应的功能，如难度变化的测验或练习题。大多数 MOOCs 都有和 LMS 一样的缺点。质量千差万别；有些内容是课堂上最好的，有些是拼凑出来的。***

有几个类似 MOOC 平台的例子，做的事情有点不同。Udacity 涉及更多的实践，这是一个优势，但仍然沿着相同的路线。Duolingo 专注于语言，并使用游戏化技术。可汗学院有一个美丽的知识之树，很容易显示先决条件，并再次将实践大量集成到系统中。一些替代 MOOCs 专注于基于问题的学习，而不是基于讲座的学习。

[![A screenshot of Anki, a flash card system based on spaced repetition. [https://commons.wikimedia.org/wiki/Category:Anki#/media/File:Anki.png](https://commons.wikimedia.org/wiki/Category:Anki#/media/File:Anki.png)](img/5ed7153054c4be856503c75ef6ef1d77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lZZJT1Ek--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-25.png)

[***闪卡系统***](https://en.wikipedia.org/wiki/List_of_flashcard_software) ***。*** 闪存卡系统超级普遍。通常，这些是学习者创建的内容，并且是免费提供的。其中一些具有适应性，比如[间隔重复](https://en.wikipedia.org/wiki/Spaced_repetition)。间隔重复意味着学习者将在 1 天后，然后 3 天，然后 7 天，等等，直到材料被牢固地储存在长期记忆中。尽管闪存卡系统不超出记忆范围。这些系统的几个例子是 [Anki](http://ankisrs.net/) 和 [SuperMemo](https://www.supermemo.com/) 。

[![A screenshot from Wikipedia, the largest open-content system that exists. [https://commons.wikimedia.org/wiki/Wikipedia#/media/File:Www.wikipedia.org_screenshot_2013.png](https://commons.wikimedia.org/wiki/Wikipedia#/media/File:Www.wikipedia.org_screenshot_2013.png)](img/395fbf5d89b559302a33ee447d07e6b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--amoDgXid--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-26.png)

[***开启内容系统***](https://en.wikipedia.org/wiki/Open_content) ***。*** 当前的开放内容系统提供各种主题的深度、高质量内容。然而，他们通常不具备适应能力。最常见也是最大的例子是[维基百科](https://www.wikipedia.org/)。

## 萨格菲的背景故事

像大多数人一样，我在上学的时候考虑了学习的方法。作为一名技术人员，我经常考虑如何以不同的方式做事，或者如何优化体验。在我的一生中，每一门精彩、鼓舞人心、丰富而有用的课程，都至少有三到四门不是。在我读研期间，我也有机会为俄勒冈大学的教育网站工作。

完成学业后，我立刻意识到有多少同龄人在苦苦挣扎。我们所有人都经历了 17 年或更长时间的教育系统。17 年来基本被遗忘的材料。17 年来准备成为这个社会的成年人。大约七八年后，我的许多同龄人仍然在星巴克当咖啡师，或者在苹果商店当店员。对不起，但是没有人会用每年 4 万美元买房子，同时还有 3 万美元的学生贷款。我知道，第一世界的问题。我很幸运地发现自己走上了软件的职业道路，特别是考虑到我的学位是艺术和音乐。

> 在教育系统中大约需要 17 年才能被认为是对社会有贡献的成年人。

17 年太长了。17 年对于成为一个有贡献的成年人来说太长了。我们对学习应该如何工作的期望和真正有效的东西是如此的不同步。(我不是说 Sagefy 是解决这个问题的*方案，只是说我们需要进行对话。)*

在我生命中的一段时间里，大约在 2012 年左右，我迷上了看 TED 视频。是的，是的，很多都是相同的灵感绒毛在重复。然而，其中一些*是*独有的。在那段时间里，我的生活把我放在了这个位置上。我只是跟着前面的课程走。我开始问自己，我真正想做的是什么？*我能贡献的最好的东西是什么？*

我们面前有如此多的巨大挑战，比人类历史上任何时候都多。全球变暖、能源、水、技术变革、人口增长、医疗保健、债务。仅举几个例子。我永远不会成为环境科学家、政治家、数学家、神经科学家或火箭工程师。我没有准备好迎接任何特定的挑战。但是我们能做的最大投资是学习。如果我解决不了，我可以帮助有能力的人。

> 我问自己，我能做出的最好的贡献是什么。我得出的答案是，最好的投资是在学习上。

我开始读关于学习科学的书。这自然符合我在用户体验研究方面的知识和技能。首先，我开始大多是从一些[真的](https://www.amazon.com/dp/0716786540) [伟大的](https://www.amazon.com/dp/0470484101) [书籍](https://www.amazon.com/dp/0787988448)开始。从那时起，我开始接触发表的关于学习科学的文章，包括学术文章和新闻文章。我开始构思我所能想象的最好的学习系统。

[![An early wireframe from Sagefy.](img/941e3bc5d1598b221ba37272af2c12e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Znwuu6QW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-27.png)

2013 年初，我的朋友诺亚来我家住了几天。我谈了我的阅读和我的想法。他问我，“你为什么不直接建造它？”起初这似乎是不可能的。但我开始思考这个问题。我开始思考 [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) 。尽管我不是这方面的专家，但我知道的足够把一些东西组合起来。那年四月，我开始研究萨吉菲。

[![The equations for Bayesian Knowledge Tracing. It is simpler than it looks. P(L) is probability learned, P(S) is probability of “slip”, or making a mistake, and P(G) is probability of “guess”. P(T) is probability of “transit”, or how likely the learner learned the content from this material. From [https://www.researchgate.net/publication/249424313_Individualized_Bayesian_Knowledge_Tracing_Models](https://www.researchgate.net/publication/249424313_Individualized_Bayesian_Knowledge_Tracing_Models)](img/ac337dea1170ded4a48f886ad528ba72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--82uZ5-iw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/why-28.jpeg)

我已经知道如何构建 web API 和 web UI。所以当我开始[计划](https://github.com/heiskr/sagefy/wiki/Planning)和工作的时候，我研究了算法方面。我在不同的平台上参加了很多在线课程，其中之一是瑞安·贝克的[大数据和教育](http://www.columbia.edu/~rsb2162/bigdataeducation.html)课程。特别是，我理解了[贝叶斯知识追踪](https://www.youtube.com/watch?v=_7CtthPZJ70)部分，并且看到了一个我试图构建的完美契合。现在，一个 MVP [已经存在，并且正在运行](https://sagefy.org)，随时可以使用。

在建立了 MVP 之后，我意识到没有人会理解我建立的没有内容的东西。所以在过去的几个月里，我一直在建立一个关于电子音乐的小型课程，这是我的硕士领域。前几个单元是活的。

## 萨格菲的未来

我和其他人一样意识到，萨吉菲很可能不会成功。我没意见。有太多的事情可能出错，更重要的是，有太多正确的事情可能不会发生。缺乏兴趣，难以获得用户群，创建内容或改进自适应算法方面的挑战……未来还有许多挑战。我最大的希望是，即使萨吉菲不成功，萨吉菲也能*激发一场关于这个科技时代学习的对话。Sagefy 不一定要大或者成功，但是*某个系统*一定要成功。这个机会太大了，不容忽视。对我来说，我能做的最好的事情就是获得一些关于如何解释人们在学习系统中真正如何学习的想法。即使我失败了，我也能证明另一种选择是可能的和现实的。当然，如果萨基菲真的成功了，那就更好了。*

我希望有一天，萨吉菲会成为一个任何人都可以来学习任何东西的地方，不管他们已经知道了什么。然后，能够尽快实现自己的学习目标。无论是小项目，还是整个学位项目，Sagefy 都会以一种现实的方式提供所有信息，告诉人们如何学习。我意识到有些课程我们只能亲自教授，但是有太多的课程*我们可以*在线教授，而我们目前没有。

我也预见到有一天，一些组织，如学校、公司、非营利组织和政府，会出于教育或培训的目的，想要运行他们自己的 Sagefy 实例。我目前的财务愿景是 Sagefy 将为面向公众的实例提供*捐赠支持*，并且支持服务将可用于私人安装。

如果你想了解更多关于 Sagefy 的信息，请访问今天的网站。*