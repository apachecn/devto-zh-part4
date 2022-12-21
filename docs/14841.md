# 德国柏林 GraphQL 会议的主要收获

> 原文：<https://dev.to/rinky_05/key-takeaways-from-graphql-conference-berlin-germany-34d1>

这篇文章最初发布在我的媒体账号[这里](https://medium.com/@rinx.goyal/key-takeaways-from-graphql-conference-berlin-germany-40ead85a9c2)

# 第 1 天

以下是我在德国柏林举行的 GraphQL 会议第一天的收获。精彩演讲者的精彩演讲！！

# Dan≤talk—graph QL 之前的图形 SQL

所有东西都必须编码成异步的——即使你认为不需要异步，也要让它异步。
解析器必须是异步的

1.  GraphQL 在开发时从来没有打算解决授权、结果缓存、速率限制等问题

2.  解析器应该是瘦的，应该只映射到业务逻辑，不包含实际的业务逻辑

# Sashko Stubailo 解释了为特定用例构建定制的 GraphQL 工具是多么容易

1.  使用更少的样板代码编写测试，并能够使用这个 graphql-tools 编写用于测试边缘情况、错误状态的自定义覆盖。点击阅读更多[。](https://www.freecodecamp.org/news/a-new-approach-to-mocking-graphql-data-1ef49de3d491/)
2.  通过检查 GraphQL 字段是否被引用来防止重大更改。在这里阅读[这里](https://gist.github.com/stubailo/7a2071c4e568a185726c583073695bc0)

# Sasha Solomon 谈到了 GraphQL 响应中的错误数组以及如何以更好的方式处理它们。

1.  所有错误都在错误数组中结束，但并非所有错误都是相同的。
2.  其中一些是真正的错误，如服务器不可用，坏网关，但其他的可能只是“替代结果”，如阻止用户，暂停用户等。

3.  要区分如上所述的错误，使用联合类型并基于响应中的类型，客户端可以决定做什么。union User result = User | is blocked | Suspended

关键是不要创建太多类型，只保留重要的类型。
这个演讲一开始就很容易掌握和实施:)

# 迁移到 GraphQL 的最佳实践

1.  考虑进行增量更改、边缘情况和安全回滚的能力
2.  构建易于移植的适配器自动化测试套件对于完成这项艰巨的任务非常重要

# Marc-André Giroux 谈在 GraphQL 中分布模式

1.  总是问这样一个问题:分发模式真的有必要吗？
2.  模式拼接有助于我们实现模式分发，但业务逻辑通常会泄漏到 API 网关中，最终会变得比简单的“代理”更复杂。理想情况下，API 网关应该如下所示:
3.  将客户从用例中涉及的“服务”中分离出来
4.  避免领域逻辑
5.  模式应该很容易导入到 gateway Talk 幻灯片[这里](https://speakerdeck.com/xuorig/so-you-want-to-distribute-your-graphql-schema-16fd4e47-eae7-4437-be2a-5e9b803a675e?slide=44)。

这是我尝试写下在我的笔记本和大脑:D 中捕捉到的东西，我将会写另一篇关于从第二天开始学习的博文。敬请期待！