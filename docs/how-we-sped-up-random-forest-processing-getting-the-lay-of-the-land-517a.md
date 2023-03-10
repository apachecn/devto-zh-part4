# 我们如何加快随机森林处理，了解土地的情况

> 原文：<https://dev.to/nicklarsen/how-we-sped-up-random-forest-processing-getting-the-lay-of-the-land-517a>

从[这里](https://cultureofdevelopment.com/blog/how-we-sped-up-random-forest-processing-getting-the-lay-of-the-land/)贴十字。

我在[stackoverflow.com](https://stackoverflow.com)负责招聘广告选择模型的团队工作。这些事情:

[![Stack Overflow Job Ads](img/4f3f020707eaa2ac3e3087735422532b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u6l2maMu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cultureofdevelopment.com/img/so-job-ads.PNG)

该模型预测用户相对于所有其他作业点击某个给定作业的可能性。在任何给定的时间，当前访问者可以看到工作板上的一堆工作，因此我们计算每个工作的期望值，然后对这些值执行加权随机选择。

我们的问题是，该模型需要不时更新，以适应不断变化的市场条件。基本上，新技术不断涌现，哪种技术用于堆栈溢出会随着时间的推移而变化，哪种作业会一直变化。

在最近一轮的模型更新中，我们决定跳出使用[套索回归](https://en.wikipedia.org/wiki/Lasso_(statistics))的典型过程，而是决定尝试各种其他模型。最终，基于我们选择标准的最佳模型变成了一个[随机森林](https://en.wikipedia.org/wiki/Random_forest)。一个随机森林基本上是一个决策树的集合，所有这些决策树组合起来进行预测。

你可能听说过决策树，因为它们是人工智能中最大的笑话之一。

[![Decision Trees unmasked!](img/56499af057bf751a54718cafb26d675c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8tqrwa53--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cultureofdevelopment.com/img/scooby_doo_ai.jpg)

###### 图片来源:【9gag.com T2】

但事实证明，它们毕竟非常强大，尤其是当你一起使用许多它们时，就像随机森林一样。当我看到迷因时，我往往会有点不安，因为有趣的部分不是它们如何做出预测，而是它们是如何构建的，这是合法的 ML。

然而，训练一个随机的森林是非常昂贵的，而且我们不能提供所有的样本数据。相反，他们决定尝试一个类似的模型，XGBoost，我们可以在相同的时间内输入更多的数据。这是以完全不同的方式训练的，即使用[增强](https://en.wikipedia.org/wiki/Boosting_(machine_learning))，然而在预测方面，它被证明几乎与随机森林完全相同。

因此，我们有了一个新的模型，我们希望它会很好，现在我们需要运行一个测试，这意味着我们需要将这个东西放入一个可以从我们的广告服务器调用的 API 中。

### 先试最容易的

我们在这里遇到的一个问题是，我们的大多数模型都是用 R 语言训练的，然后我们必须转换成 C#来运行，这是我们在栈溢出时用于几乎所有生产的东西。

长期以来，我们一直在考虑在生产中运行 R，这样我们就可以避免将模型转移到新系统的步骤。由于这种转移，出现了不止一个重大错误，一旦我们从数据科学家那里获得新模型，这也会减少我们推出新模型的时间。

所以我们试了一下。然后我们就想，好吧，让我们看看它有多快。

[![R API times](img/de24d0c1a3e03487585c82a81b51cefd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pC8zRlmf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cultureofdevelopment.com/img/xgboost-api-time-r.png)

###### 图片来源:[茱莉亚西尔格](https://twitter.com/juliasilge)

那是...嗯，不快。完成一个*单个*任务大约需要 25 毫秒的平均时间。但是等等，我们在任何时候都有很多工作。

[![Number of jobs on Stack Overflow right now](img/1fef2172162a9b60d4c9c1a058d1d4de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EnIPzhHS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cultureofdevelopment.com/img/num-jobs-on-the-board.PNG)

如果你住在美国，这就是你现在能看到的数量，你也可以通过访问 stackoverflow.com/jobs T2 来了解你所在地区现在有多少工作机会。如果我们把它乘以平均完成一项工作所需的时间，我们就会看到`3810 * 25ms = 95,250ms`。渲染一个招聘广告需要很多时间。事实上太多了。我想我们需要看看我们的总体需求。

### 要求

首先，由于各种营销和联合努力，以及一般的季节性波动，工作岗位的数量会不时波动。我们需要在这里建立一些缓冲，所以假设这需要为 5000 个工作岗位工作，当然越多越好。

[![Job Ad Request Engine times](img/66e071d687fee06ac9685750dc4956a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nM6yKE4P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cultureofdevelopment.com/img/ad-request-time.PNG)

看看我们当前的广告请求时间，看起来整个工作选择过程平均需要大约 25 毫秒，对于第 99 百分位大约需要 65-70 毫秒。这是做所有的评分和选择工作的基础上，这些分数。我不完全确定有多少时间是工作选择过程，但我们应该给自己一些缓冲，让我们称之为 25 毫秒，以匹配我们所拥有的，我们需要将第 99 百分位保持在 65 毫秒以下。

这个应用程序运行在我们的 web 层上，与 Stack Overflow 和所有其他 Stack Exchange 站点运行的机器完全相同。这些盒子被疯狂的请求所包围。在我们的 web 层，请求路径中的几乎所有东西都需要以单线程的方式处理。为了测试并行运行的东西，这是一个独立的测试，超出了这个项目的范围。

我们还需要了解这个应用程序的内存使用情况。请求处理需要分配尽可能少的内存，但是这里有一些余地，因为盒子本身已经运行了大约 80%的内存容量。每个机箱上有 64 GB 的 RAM，我们需要在其中保留一些缓冲，所以去架构团队要求每个机箱上有超过几 GB 的内存是一个相当大的要求。无论运行一个请求需要多少内存，我们都需要将它乘以 48，因为机器上有 48 个内核。如果我们希望保持在 3 GB 以下，那么每个请求最多 62.5 MB。

最后，我们真正关心的只是让这个特定的模型足够快地运行测试。这里没有必要构建一个通用的随机森林实现，只需要测试我们得到的模型，这样我们就可以验证它是否比我们的旧模型有所改进。在必要时做出让步，只支持这一个实例是完全可以的，如果这个模型证明比我们目前拥有的更好，*那么*我们可能会考虑将其推广，以支持更广泛的模型实例。

简而言之，处理单个请求的要求是:

*   必须为一个用户得分 5000 个工作
*   必须在大约 25 毫秒内完成，99%的请求需要在 65 毫秒内完成
*   必须是单线程的
*   总共必须使用不到大约 65 MB 的空间
*   只要*这个模型实例*正确工作，所有的技巧都是允许的

### 让我们坚持我们的面包和黄油

对于性能关键的应用程序，我们在 C#上比在 R 上做得更好。此外，现在要做的最重要的事情是验证这个新模型是否比旧模型有所改进，因此建立一个将 R 包括在生产中的管道可能是我们不需要考虑的大量额外工作。

所以我们决定用 C#写这个，我们用了最简单的解决方案。几乎所有运行 XGBoost 模型的代码都是这样的:

```
public sealed class DecisionTree
{
    public class DecisionTreeNode
    {
        public int FeatureIndex { get; set; }
        public double Value { get; set; }
        public int TrueBranch { get; set; }
        public int FalseBranch { get; set; }
    }

    const int LeafIndex = -1;

    private readonly DecisionTreeNode[] nodes;

    public DecisionTree(DecisionTreeNode[] tree)
    {
        nodes = tree;
    }

    public double Evaluate(double[] features)
    {
        var node = nodes[0];
        while(node.FeatureIndex != LeafIndex)
        {
            int nodeIndex = features[node.FeatureIndex] < node.Value ? node.TrueBranch : node.FalseBranch;
            node = nodes[nodeIndex];
        }
        return node.Value;
    }
}

public sealed class RandomForest
{
    private readonly DecisionTree[] trees;

    public RandomForest(DecisionTree[] trees)
    {
        this.trees = trees;
    }

    public double EvaluateProbability(double[] instance)
    {
        var sum = trees.Sum(t => t.Evaluate(instance));
        var result = Logit(sum);
        return result;
    }

    // this converts the output to a probability
    private double Logit(double value)
    {
        return 1f / (1f + Math.Exp(-value));
    }
} 
```

这基本上是所有工作的一部分。我省略了解析代码，因为它不是特别重要，但是为了让您知道 R 中 XGBoost 模型的实际输出是什么样子，这里有一个片段:

```
booster[0]
0:[f0<0.99992311] yes=1,no=2,missing=1,gain=97812.25,cover=218986
1:leaf=-0.199992761,cover=27584.75
2:[f17<0.000367681001] yes=3,no=4,missing=3,gain=10373.0732,cover=191401.25
3:[f2<0.5] yes=5,no=6,missing=5,gain=4121.85938,cover=103511.5
5:[f6<0.00216802233] yes=9,no=10,missing=9,gain=873.340759,cover=50533.25
9:[f732<0.5] yes=17,no=18,missing=17,gain=515.368896,cover=33356.75
17:leaf=-0.00213295687,cover=25503.5
18:leaf=-0.0314288437,cover=7853.25
10:[f732<0.5] yes=19,no=20,missing=19,gain=276.522034,cover=17176.5
19:leaf=0.0253729131,cover=13474
20:leaf=-0.00548130181,cover=3702.5
6:[f734<0.237819463] yes=11,no=12,missing=11,gain=2141.23145,cover=52978.25
11:[f8<0.00104575348] yes=21,no=22,missing=21,gain=566.334961,cover=35689
21:leaf=-0.0620479584,cover=24457.5
22:leaf=-0.0349165387,cover=11231.5
12:[f762<0.308019817] yes=23,no=24,missing=23,gain=483.886719,cover=17289.25
23:leaf=-0.0144120604,cover=16450.5
24:leaf=0.063411735,cover=838.75
4:[f2<0.5] yes=7,no=8,missing=7,gain=2694.23291,cover=87889.75
7:[f27<0.000739371637] yes=13,no=14,missing=13,gain=928.447266,cover=44100.5
13:[f732<0.5] yes=25,no=26,missing=25,gain=285.069702,cover=17082.25
25:leaf=0.032621529,cover=13427.25
26:leaf=0.00112144416,cover=3655
14:[f285<0.000919258455] yes=27,no=28,missing=27,gain=421.745117,cover=27018.25
27:leaf=0.0483669229,cover=20145
28:leaf=0.077062957,cover=6873.25
8:[f734<0.103942066] yes=15,no=16,missing=15,gain=1591.2124,cover=43789.25
15:[f101<0.000240761583] yes=29,no=30,missing=29,gain=608.92157,cover=24192.75
29:leaf=-0.0209285971,cover=14574.75
30:leaf=0.0114876805,cover=9618
16:[f722<0.5] yes=31,no=32,missing=31,gain=601.422363,cover=19596.5
31:leaf=0.0258833747,cover=18429.75
32:leaf=0.099892959,cover=1166.75
booster[1]
0:[f0<0.99992311] yes=1,no=2,missing=1,gain=80168.1719,cover=218645.734
1:leaf=-0.181867003,cover=27310.75
2:[f17<0.000390548841] yes=3,no=4,missing=3,gain=8405.98535,cover=191334.984
3:[f2<0.5] yes=5,no=6,missing=5,gain=3338.54272,cover=103498.18
5:[f5<0.000531250262] yes=9,no=10,missing=9,gain=750.746765,cover=50538.3281
9:[f732<0.5] yes=17,no=18,missing=17,gain=355.950684,cover=32388.959
17:leaf=-0.00289925188,cover=24663.1035
18:leaf=-0.0274959896,cover=7725.85449
10:[f732<0.5] yes=19,no=20,missing=19,gain=279.289886,cover=18149.3711
19:leaf=0.0230532587,cover=14319.1934
20:leaf=-0.00734602008,cover=3830.17749
6:[f734<0.237819463] yes=11,no=12,missing=11,gain=1736.53467,cover=52959.8516
11:[f5<0.000720986514] yes=21,no=22,missing=21,gain=514.774414,cover=35668.0508
21:leaf=-0.0571310222,cover=22939.1699
22:leaf=-0.0320498347,cover=12728.8799
12:[f722<0.5] yes=23,no=24,missing=23,gain=468.159485,cover=17291.8027
23:leaf=-0.0137320729,cover=16250.6074
24:leaf=0.0554070361,cover=1041.19482
4:[f2<0.5] yes=7,no=8,missing=7,gain=2186.99561,cover=87836.8047
7:[f27<0.000739371637] yes=13,no=14,missing=13,gain=755.193359,cover=44065.7109
13:[f668<0.5] yes=25,no=26,missing=25,gain=245.545715,cover=17078.1777
25:leaf=0.0203356724,cover=16096.4053
26:leaf=0.0718296021,cover=981.772095
14:[f58<0.000670915877] yes=27,no=28,missing=27,gain=312.375,cover=26987.5352
27:leaf=0.0368296206,cover=10639.0859
28:leaf=0.0588531196,cover=16348.4482
8:[f734<0.237819463] yes=15,no=16,missing=15,gain=1328.46338,cover=43771.0977
15:[f27<0.000467836275] yes=29,no=30,missing=29,gain=631.899902,cover=28666.8477
29:leaf=-0.0222451035,cover=11761.168
30:leaf=0.00793752726,cover=16905.6797
16:[f722<0.5] yes=31,no=32,missing=31,gain=362.48999,cover=15104.248
31:leaf=0.0279367212,cover=14043.1475
32:leaf=0.0885346606,cover=1061.10071

```

这个代码片段显示了两棵树，由以`booster[`开头的线条分隔，共有 1000 棵树。由于该模型的训练方式，当样本中缺少某个特征时通常会采用的缺失分支总是“否”分支。这是因为如果所有值缺失，我们用 0 替换它们，并且所有特征在我们的问题空间中都有正值。gain 和 cover 变量只是从模型中输出的信息，但对树没有任何影响。第一个数字是由“yes”和“no”参数引用的行的索引。在我们的数据中，特性编号为 0 到 840，所以当您看到`fXXX`时，它表示输入中的特性编号 XXX。正如你从`Evaluate`函数中看到的，这些函数的输入只是一个双精度数组。

### 我们还有多近？

让我们快速试验一下，看看。这只是使用了一个简单的计时器来运行这个包含 5000 个例子的代码。这里有一个警告，确保你在发布模式下运行，否则你的时间将会大不相同。

> [2019 年 6 月 20 日上午 4:02:34]5000 次评估所用的时间:319.5548 毫秒

嘿，这还不算太坏，只差大约 12-13 倍。我应该在这里指出，当您试图达到一个特定的性能目标时，您将要运行它的实际机器会有所不同。我的 dev box 大约每隔一年更新一次，比我们的许多生产服务器更强大，尤其是将要运行它的服务器。事实上，如果你在最近一两年发布的处理器上运行这个，你可能会看到时间低至 220 毫秒，就像我在我的笔记本电脑上运行的那样，它只有 4 个月大。[此处是强制性的“在我的机器上工作”徽章]在理想世界中，我们会在我们关心的机器上运行这个测试，但是由于我们还远远没有达到我们的要求，现在还不是时候。

但是等一下，我们不要急于求成，让我们验证一下这实际上也产生了正确的值。请记住，这里有一堆我们用于计时的样本，我们现在可以用它们来验证它是否输出了正确的值。这些样本和预期输出是由我们的数据科学家提供给我们的。

```
RandomForest model; // loaded in test setup
double[][] samples; // loaded in test setup
const int probablity_feature_index = 840;

foreach(var sample in samples)
{
    var actual = model.EvaluateProbability(sample);
    var expected = sample[probablity_feature_index];
    Assert.InRange(actual, expected - 1e-06, expected + 1e-06);
}
output.WriteLine("Correctness verified."); 
```

> [2019 年 6 月 20 日上午 4 时 02 分 34 秒]正确性已验证。

所以\o/我们是在正确的轨道上！

让我们花一点时间来认识到，我们将在这里看到的最大的胜利是从我们不熟悉的工具，即上面描述的 R API，转移到我们更擅长的东西。我们能够将单个样品评估的时间从大约 25 ms 减少到大约 0.047 ms，这大约是三个数量级。我要明确一点，这并不是因为 *R* 慢，而是因为*我们*对 R 不够熟悉，无法写出高效的代码。这整个系列的其余部分是关于尽可能多地研磨出最后一个数量级。

### 接下来是什么？

下一次，我们将看看一些真正的基本改进，这将为我们赢得相当多的胜利。设计任何东西的第一步都是让它正常工作，并为性能设定一个基线。我们接下来要解决的几个问题将是非常常见的技巧，只需很小的改动就能显示出应用程序中的一些重大改进。

如果您有兴趣跟随并亲自尝试这些东西，请克隆 repo 并运行自述文件中显示的简单测试。