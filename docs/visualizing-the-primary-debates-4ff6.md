# 可视化初选辩论

> 原文：<https://dev.to/charlesdlandau/visualizing-the-primary-debates-4ff6>

过去几天，美国民主党人一直在辩论谁应该与特朗普竞选。对于 dataviz 来说，这些文字记录似乎是一个有趣的话题。

这些可视化的所有代码都在不同的提交中发布到这里的。

首先，我认为这将有助于获得一个简单的酒吧显示多少候选人发言。

注意:正如您将看到的，我没有花时间来确保数据的完美清理。有一些伪影和错误，在 word clouds 里会很明显。

[![dems_word_count](img/72d58e12aee0984277e5b92b5d352a1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--41w8afKW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kyf360vwjpq4r9z74zj9.png)

我还惊讶地发现，如果你创建一个基于 TF-IDF 的距离矩阵...

[![dems_distance_matrix_viz](img/3471fe0e842b99cbd4eb6858767bdfa7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y5kUFXCe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lqwcaw8eb59o7jwfdaqi.png)

...演讲者很好地整理了自己。我见过的被称为 T1 的投票率最低的人是皮特市长，无论你是否把他算作 T1，这种模式都成立。

这意味着什么吗？我不这么认为，至少不是完全靠自己。

最后是一些单词云:

[![dems_word_clouds](img/aea35ee94bd686484f23c28edd65fd0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lkktaC9w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n5rqyatws8ozfot1pz9n.png)

总的来说，我认为这是一个有趣的小练习，但我不怀疑它对比赛说了太多。

让我知道你的想法！尤其是当你发现一个错误的时候。