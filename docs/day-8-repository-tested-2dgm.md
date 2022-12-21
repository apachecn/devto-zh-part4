# 第 8 天:存储库，测试

> 原文：<https://dev.to/rahulchowdhury/day-8-repository-tested-2dgm>

# 昨天的工作

我开始重构我的存储库，以接受本地和远程源代码契约，而不是具体的类。

这一举措是为了让我的存储库可以使用 [JUnit4](https://junit.org/junit4/) 和 [Mockito](https://site.mockito.org/) 进行单元测试。

这里有一个关于如何使用 Mockito 进行单元测试的很棒的教程:[https://www.vogella.com/tutorials/Mockito/article.html](https://www.vogella.com/tutorials/Mockito/article.html)

# 今天？

我一上午都在为我的存储库编写单元测试。我不得不说，这很有趣。

我第一次使用 Mockito，这是一次很棒的体验。到处都有打嗝，但总的来说这是一次美好的经历。

我面临的一些障碍:

*   `NullPointerException`上嘲讽`Boolean`值。原来在 Kotlin 上解盒/自动装箱`Boolean`值时，Mockito v2.8.9 出现了一个问题。通过升级到 v3.0.0 解决了。

*   `IllegalStateException`因为`ArgumentMatchers.eq()`返回一个`Nullable`值，而我的参数是不可空的。我不得不通过引入这样的本地替代方法来解决这个问题:

```
fun <T : Any> safeArgumentEq(value: T): T = eq(value) ?: value 
```

我的存储库现在已经过良好的测试。可以在 GitHub 上找到测试文件[做参考。](https://github.com/rahulchowdhury/elly/blob/master/app/src/test/java/co/rahulchowdhury/elly/data/repo/DefaultElephantRepositoryTest.kt)