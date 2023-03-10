# DIY 平台上 5 分钟的人工智能模型部署

> 原文：<https://dev.to/tsuz/5-minute-ai-model-deployment-on-diy-platforms-5egb>

### 背景

虽然部署人工智能模型通常被证明是一个合理的挑战，但 DIY 平台正在改变游戏规则，部署变得既容易又迅速，甚至对相对缺乏经验的
来说也是如此。
对于大多数开发人员来说，人工智能模型开发似乎是一件很好的事情，但是真正的挑战开始于他们需要创建和部署模型的时候。

*   了解特征工程
*   理解机器学习模型背后的数学原理并选择正确的模型
*   设计访问人工智能模型预测特征的界面
*   在云或自托管平台上实现服务于人工智能模型的基础设施

正如你在上面看到的，这些技能需要一个工程师和开发人员的团队，而大公司是雇得起的。通过使人工智能建筑民主化，SuperAI 创造了巨大的价值，任何拥有有意义数据的人都能够生成一个模型，并开始将其集成到他们的应用程序中。

### 这是怎么做到的

1.  访问 [SuperAI](https://www.superai.io/?marid=NeG2Cv) ，这是一个简化模型构建和部署的人工智能平台。

2.  注册并创建一个项目。我选择了一个用例，“文档分类器”。文档分类是从句子中预测类别。
    [![](img/72bf5c2ff700af19610a7e43816993b6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--KJGCrYl7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hpyv4653qh60gom2u9bh.png)

3.  按照数据准备的说明，我拖放了要上传的文件。如果我不照着说明去做，它似乎不起作用..
    [![](img/4fdc462ef01ad8a214f3778d62ffb657.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--rs6_46vk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q80w7064urwg19q1ox9c.png)

4.  数据中有一些警告；平台要求我选择目标列。目标列是我们想要预测的结果。在这种情况下，我想预测情绪，所以我选择“情绪”作为目标列，警告消失了。
    [![](img/66165edc7f3e75a97eefc73f701f1fe4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--zttV9QU_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qk8gda5qm9ljj6hxcn0r.png)

5.  现在，我单击“培训”选项卡，我可以培训一个模型。我选择了 5 分钟作为训练时间，但根据数据的大小和复杂性，这个时间可以更短或更长。
    [![](img/766b28b8c4fc79e09f22b0d4b117660b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--j1iF8To1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w743982s8e9ccqvaldfy.png)

6.  5 分钟后，我能够以 94.75%的准确率得到一个模型。对我的申请来说，这似乎足够好了。
    [![](img/0452d31922a6baa9ff8f95b948cebd1e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--46TsqVj9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rhglty11hnu2lqohh4zz.png)

7.  现在，我想部署这个模型，所以我单击“阶段”，选中“部署”
    [![](img/4d77639e143a55e6ca0662a87674ce68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Wx_dN2C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jeo88rke894t8drhyo06.png)

8.  过了一段时间，似乎出动了
    [![](img/fa524bbc2759cf1f37a551d5b245f644.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LtJ8Mxf---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7mxvx5adstzimux2jii9.png)

9.  现在，我转到“预测”选项卡，测试它的工作情况。我输入一个句子来预测，然后我能够得到分类的结果和它们的置信度。
    [![](img/57823cf2d50c89350cd52b01c280f31d.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NBcH3cZ---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ew1a961shwmo5m8itcys.png)

10.  然后我生成了 API 密匙，并在终端中进行了测试，以验证结果
    [![](img/6a871168b9308d29703f0564c6ccc375.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---iZVgUaH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t3dgou9r2ju2pcu44jw8.png)

太好了！现在我可以将它集成到我的应用程序中
,看起来秘诀是理解你的数据，并以他们接受的方式格式化它。如果数据没有意义，你会期待一个不准确的模型。如果数据的格式不正确，它似乎不能很好地解析。

### 结论

DIY 人工智能平台正在改变游戏规则，以相对更好、更快地输出人工智能模型。
只要你理解你的数据，剩下的过程就很简单了。我希望这个 DIY AI 平台能够改进，以便它可以处理更多的用例和更广泛的数据格式。

感谢阅读，