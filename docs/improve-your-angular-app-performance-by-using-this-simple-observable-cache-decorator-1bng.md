# 使用这个简单的可观察缓存装饰器来提高 Angular 应用程序的性能🎉

> 原文：<https://dev.to/angelnikolov/improve-your-angular-app-performance-by-using-this-simple-observable-cache-decorator-1bng>

当我们即将在 SwiftViews 中完成应用程序的开发时，我们注意到所有数据获取用户流中的一个模式。尽管这些应用都是数据驱动的，看起来非常动态，但在同一个用户会话中真正发生变化的并不多，但我们还是发出了新内容的 http 请求。
最简单的解决方案是缓存
缓存在哪里？在服务器上？我们已经有了，但这并不能阻止我们所有的应用程序访问我们的服务，从而增加它们的负载。
也许我们可以使用服务工人，因为它允许缓存 API 调用？
是的，这是选项之一，因为它已经与 Angular 进行了很好的集成，并允许一个简单的解决方案来选择性地缓存资源和 API。然而，我们想要的是不仅能够选择缓存什么，而且能够选择何时缓存。
给你一个简单的例子——我们基本上想缓存下面页面的所有 API 调用结果，但前提是我们确定数据源没有以某种方式改变。
[![Inventory](img/20feb8cedc77c2e5843794215cba420d.png)T8】](https://res.cloudinary.com/practicaldev/image/fetch/s--iSUzMmMb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x5df5ibqm2u4nqndgtz1.png)

由于这是我的个人库存页面，它实际上可以改变的唯一方式是如果我从这个平台添加一个项目(目前唯一的方式)，所以我有关于这个页面何时会改变以及我可以返回缓存内容多长时间的信息。
我们认为以选择性和可配置的方式应用这种缓存的最佳方式是使用平台已经提供给我们的东西
由于我们使用角度和类型脚本❤️，并且我们所有的数据调用都通过 RxJs，我们认为我们可以创建一个缓存可观察装饰器，我们可以用它来只为某些方法提供缓存能力。
之前，这是我们的方法，它调用我们的服务器来获取上面页面上的产品:

这就变成了:

请注意,@Cacheable()仅应用于我们想要的方法，并且还被传递给 cacheBusterObserver，cacheBusterObserver 基本上是我们基于主题的机制，它告诉这个精确修饰的方法在流中发出任何值时释放所有缓存。
例如，如果下面的方法返回的可观察对象发出一个值，那么它将“缓存爆炸”上面方法的缓存😵

这可能是我们拥有的最复杂的示例，但我们还有其他配置，如 maxCacheCount、maxAge、slidingExpiration 和自定义缓存决策器和解析器，它们可以让我们对缓存内容、时间和方式进行精细控制。请参阅自述文件中的内容。在下面的两张 gif 图中，你可以看到我们的应用在没有缓存和有缓存时的性能差异。

[https://www.loom.com/share/71a03e4cd478407fa53f131fc112e09a](https://www.loom.com/share/71a03e4cd478407fa53f131fc112e09a)

[https://www.loom.com/share/380a1fa93a9c42d0a5adaa987cd51efb](https://www.loom.com/share/380a1fa93a9c42d0a5adaa987cd51efb)
应用了可缓存的装饰器🎉
应用了可缓存装饰器，我们可以看到所有连续的页面加载都更快了。此外，用户配置文件页面会立即加载，因为我们已经在清单页面上调用了/user/{ id }端点。此外，在我们添加一个新项目后，缓存崩溃，因此我们的用户获得了新的数据
因此，总而言之，这个简单的装饰器允许我们:
有选择地缓存可观察的方法(不仅是端点，还可能是流中计算量大的计算)
提高我们的应用程序的性能，而无需任何业务逻辑重构(感谢 ts 装饰器❤️)
大大降低我们的服务器上的负载
如果您想在您的项目中使用这个装饰器，只需从 NPM 安装它！
npm install ngx-cacheable
如果你对它有任何疑问或者想投稿，不介意打开 pull request @github 或者在下面评论。
此外，如果您喜欢该应用程序，请注册并构建您自己的 inventory @ swiftviews。
谢谢！🐦