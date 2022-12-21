# 如何停止用“Manager”后缀命名 Java 类

> 原文：<https://dev.to/scottshipp/how-to-stop-naming-java-classes-with-the-manager-suffix-48la>

在 Java 应用程序中，到处都有名为 SomethingManager 的类。帐户缓存有一个 AccountCacheManager。应用程序属性有一个 ApplicationPropertiesManager。用户事务由 UserTransactionManager 完成。

你可能在某个时候这样命名了一个类。

请去换一下。

## “经理”怎么了？

经理是一个没有意义的后缀。当然，你可能会说我们需要一个东西来管理另一个东西，但是你这么说是什么意思呢？你要给代码一个经理？这是工作场所吗？你的应用中的对象需要 boss 吗？

[![Lumbergh from Office Space](img/8b4b4743be29e53fff874e85596a1fd3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--58Ka6URA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/til3q7ug0668pf7p15ah.gif)

## 怎么改？

每当我在类名中看到“Manager”时，它几乎总是多余的。最近看到一个管理另一个组件生命周期的类。不出所料，它以“LifeCycleManager”后缀命名。当然，在这种情况下，我们需要将类命名为 *manager* ，对吗？毕竟，它*管理*生命周期，这就是我们在现实世界中的说法？

不要！不对！

这个类有`initialize()`和`stop()`方法。让我们假设它“管理”的类被称为`MyComponent.`，实际上更清楚、更易读的方法是只调用“经理”类，`MyComponentLifeCycle`。“托管”的代码变成了`MyComponentLifeCycle.initialize()`和`MyComponentLifeCycle.stop()`。我可以直接初始化或停止生命周期，当我这样做时，我说我的意思，我说我的意思。我不要求经理去做，我只是去做。

[![Shia LeBouf just do it](img/eb002c69e38d0f62f9bd15da25a7989c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uefEbnO4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0axn3j2i5d12oh7yjtcj.gif)

这个简单的想法适用于大多数用例，我在这些用例中看到了类的后缀`Manager`。刚才我打开了一个代码库，找到了一个`ConnectionManager`类。它公开的一些方法有`getConnection()`、`configure()`和`close()`。抛开这个类可能被重构为其他更有意义的对象的事实不谈，它真正做的只是持有一个`javax.sql.DataSource`并围绕它提供一些实用程序代码，最终目标是为应用程序提供一个有效的`java.sql.Connection`。

难道这个类不能被称为`DbConnection`并将那个`getConnection()`方法(顺便说一下，它返回一个`java.sql.Connection`)改为`get()`或者`asSqlConnection()`？现在你有了`DbConnection.get()`而不是`ConnectionManager.getConnection()`(顺便说一下，这完全是多余的)。以后你叫`DbConnection.close()`。不需要“经理”

## 没有管理者！

如您所见，干净的代码应该没有管理器。多此一举，没有必要。它没有告诉你这个类实际上做了什么。

我的目标是经理灭绝。谁和我在一起？

[![Protest](img/c750036b0368116a741a8a4a66d828f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iK5AzFNA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2pkloolt2rlyvqf1mfhd.gif)