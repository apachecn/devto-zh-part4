# 你的单元测试没有帮助的迹象

> 原文：<https://dev.to/megazear7/signs-that-your-unit-tests-are-not-helpful-5amg>

您可以在单元测试中查看以下两个指标:

1.  仅是单元测试的问题，而不是在真实环境中运行的代码的问题的测试失败的百分比。这经常发生在你模仿接口的时候。
2.  bug 修复或其他非突破性变更需要更新单元测试的次数百分比。

目标是使这两个百分比都为 0%。这表明每一次测试失败都代表了代码中的一个实际问题，只有特性的改变才需要更新单元测试。

当这些百分比接近 100%时，你的单元测试就变得越没用。然而，当你的单元测试是一个净正数或者是一个净负数的时候，应该有多少百分比作为截止呢？我们可以做两个假设:

1.  反映真实代码问题的每一次测试失败都超过了每一次误报。
2.  来自非特性变化的每个单元测试更新都被来自特性变化的单元测试的好处所抵消。

有了这两个假设，那么当你的单元测试变成一个净负值时，每个度量的 50%就是截止值。这些都是宽泛的假设，可能不总是正确的，但需要根据现有的技术逐个项目地确定。然而，它确实为思考单元测试的效用提供了一个框架。

查看我的博客,了解更多我对技术和其他各种话题的思考。