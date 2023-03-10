# 🚿关于最小/最大流量的最佳课程

> 原文：<https://dev.to/keywinf/best-courses-on-min-cut-max-flow-5f54>

[![](img/271923450ddebff70c3cfe469eb5cd12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rfkbQnpm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/iwwo8ztb7fql2f14pzhv.png)

假设你的客户与旅行者(T)一起工作，他们每个人都有一个周末想要去的目的地(D)的列表。我们希望将每个旅行者与一个目的地相关联，每个目的地只接受一个人，但是以一种优化的方式(简而言之，有尽可能多的关联)。

例如，假设鲍勃想游览巴黎和马德里，而爱丽丝想去巴黎。我们将 Bob 设置为 Madrid，Alice 设置为 Paris (2 个匹配)，因为如果我们将 Bob 设置为 Paris，我们将无法创建与 Alice 的任何进一步的匹配，并且我们最终将只有 1 个匹配。

当我们有数百万个可能的旅行者和目的地时，有没有什么算法可以在不到一秒钟的时间里告诉我们，什么是最佳组合？是的，它将使用最小割/最大流定理。

这里有两个教训。注意**它可能被用于比想象的**更多的情况。

第一个是由[理工学院](http://www.enseignement.polytechnique.fr/informatique/INF550/Cours1112/INF550-2011-2-print.pdf)完成的，相当不错(法语)。

这是普林斯顿大学的一篇文章，有代码示例，不太简洁，但也很有用。