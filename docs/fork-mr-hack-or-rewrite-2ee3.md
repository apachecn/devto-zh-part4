# 叉，先生，黑客或重写

> 原文：<https://dev.to/elasticrash/fork-mr-hack-or-rewrite-2ee3>

所以又是一天，又是一场工作中的辩论。生活照常进行。这一点与几乎满足您需求的依赖关系有关。

假设您需要实现一个定制的功能，但是

*   你不想重新发明轮子
*   您的广泛研究表明，现有的软件包只能做您需要的一些事情

下一步行动是什么？

*   你是否选择了满足你大部分需求的包，叉开它，调整它以适应你的需求，然后继续前进？
*   做一个 MR，耐心地希望这个包仍然被维护，你的修复最终会被合并
*   你是否试图通过破解来使用那个包，直到它满足需求
*   你从头开始写所有的东西吗

如果你叉它

*   你是否觉得有必要用一个不同的名字在包管理器(npm，nuget 等)上发布它，因为其他人可能有类似的需求？

就我个人而言，5-6 年前我会尝试破解一个包，尝试做我想让它做的事情，完全信任维护那个包的开发者。

但是现在，根据软件包的复杂程度，我宁愿放弃它，添加我的特性/修复，然后继续前进。大多数时候，我甚至不考虑社区是否会从这种定制的(通常)功能中受益。所以我不想公开发布新的包。更不用说谁需要一个包装的另一种变化，它几乎污染。

现在，如果我看到一个好处，如果我能抽出一些时间(通常是在我的空闲时间)，我会真的麻烦一个先生，如果它涉及到一个维护良好的包。因为让我们现实一点，有很多包(尤其是 JavaScript)即使被维护了，它们通常也维护得很差(我把自己包括在这个列表中，维护得很差的包)，所以你的 MR 可能会被拒绝或者需要很长时间才能被合并。