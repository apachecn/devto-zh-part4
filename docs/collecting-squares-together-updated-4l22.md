# 一起收集方块！

> 原文：<https://dev.to/becodeorg/collecting-squares-together-updated-4l22>

☝️ **注:**这篇文章是我之前关于媒体的文章之一[的*更新版本*，增加了一些更新。也是我在**开发到**的第一篇帖子！](https://medium.com/becode/collecting-squares-together-cc3177a65c86)

* * *

我爱*游戏化*。真的。像任何一个 rpg 玩家一样，为了成就而收集成就。
这就是为什么我真的很喜欢 GitHub 个人资料上*贡献图*的*绿色方块*。

但是绿色方块本质上是个人的:它是*你的*提交，*你在 GitHub 上的*活动。没有团队或组织的数据。

在贝科德，[我们作为一个团队工作，我们作为一个团队生活](https://www.youtube.com/watch?v=pGFGD5pj03M)。我们所做的一切都是团队合作！

这就是为什么我想用所有*监护人*的信息创建一个*贡献图*。

使用绿色方块感觉有点*不对*，所以我使用蓝色调的 BeCode 标识，并创建了 [Kareble](https://kareble.becode.xyz) 。

* * *

## 获取数据

我已经知道了 GitHub 的 GraphQL API

> 我告诉过你我真的很喜欢 GraphQL 吗？我喜欢 GraphQL。非常喜欢。真的。这是未来，*处理它*。

但遗憾的是，这些信息被深深地隐藏在 GitHub 服务的所有数据中。获取我需要的所有数据非常慢，所以我无法实时完成。

想法是创建一个脚本来获取数据，并每周运行一次。

经过一些测试后，我以这个 graphql 查询结束: