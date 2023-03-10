# 无受害者的金丝雀测试与科学家。网

> 原文：<https://dev.to/integerman/victimless-canary-testing-with-scientist-9nn>

如果你厌倦了用户在你之前发现错误，一个名为科学家的图书馆有一些答案。

当我们发布新代码时，我们会尽可能严格地测试它，但要复制用户使用我们软件的全部数据、工作流和环境是非常困难的。

这使得早期用户实际上成为金丝雀测试者的一种形式，就像金丝雀煤矿工人带进矿井，以获得有害气体的早期警告。

[![Canary Testers getting access to new code before other users](img/dd83ce6f1e2d7989ecadaa29a9207308.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_U9HtrlY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/vy6nhFg.png)

问题是，我们从来不希望我们的用户实际上成为一只“死金丝雀”,遇到一个错误，如果其他人可以先发现它，我们就可以让他们免受其害。

# 科学家

科学家图书馆提供了一个解决方案。使用 Scientist，新代码与旧代码一起部署，并且 Scientist 运行一个实验，在这个实验中，它执行旧代码和一个实验性的新版本，然后一起比较两个版本。

不管这两种方法的结果是否匹配，都使用遗留版本的结果并将其返回给调用者，这意味着用户不会受到新版本引起的任何问题的影响。

[![An experiment featuring a legacy and new version of a routine](img/d75025d89f4be228a53a9315f9136a08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vIr4A3Jz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/fPwimYJ.png)

这意味着新例程中的错误可以被发现，而用户根本看不到它们。如果用户的数据在新版本的代码中发现了一些错误或逻辑漏洞，用户应该完全不知道这个事实，继续使用软件，就好像它在更新之前的工作方式一样。

相反，这种比较的结果被发送到结果发布者，该发布者可以记录到许多地方，并允许开发团队在启用该特性之前调整新例程。

[![In cases where the new routine errors or doesn’t match the old, the results can be recorded and fixed later and the customer is ignorant that an issue even occurred.](img/5ff27c3ce4887dfcffdc350ef1ece668.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K_ZTM6A2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/FDAgrQO.png)

这允许我们重写或扩展部分代码，将固定版本与旧版本一起发布，然后将实现与实际生产数据进行比较。一旦我们收集了足够的数据来满足新的例行程序，我们可以发布一个新的版本，将科学家和传统的例行程序从等式中删除。

# 利用科学家

C#中使用 [Scientist 的一个例子。NET](https://github.com/scientistproject/Scientist.net) 下面列出:

```
var legacyAnalyzer = new LegacyAnalyzer();
var newAnalyzer = new RewrittenAnalyzer();

var result = Scientist.Science<CustomResult>("Experiment Name",
                                             experiment =>
{
   experiment.Use(() => legacyAnalyzer.Analyze(resume, container));
   experiment.Try(() => newAnalyzer.Analyze(resume, container));
   experiment.Compare((x, y) => x.Score == y.Score);
}); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们调用了`Scientist.Science`并声明我们期望从方法调用中返回一个自定义结果类型`CustomResult`。在那里，我们给 Scientist 一个实验名称(结果发布者可以使用)，并告诉它使用一个遗留实现。该方法的返回值将总是被返回。我们还可以通过 Try 方法声明一个到多个不同的实验来进行比较。最后，我们可以定义一个自定义方法来比较两个结果，寻找相等性。

注意[科学家。NET](https://github.com/scientistproject/Scientist.net) 将按照设计，以随机顺序运行不同的例程。

# 与科学家一起测试

科学家也可以在单元测试中用来比较遗留的和重构的做事方式。在这种情况下，您不希望重构后的版本表现出不同的行为，因此您可以依靠自定义的结果发布程序来使单元测试失败。

[![Unit Testing with Scientist](img/1e73c2747cd761579ece9c9e52319461.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9pMmnhy7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgur.com/qJ8JOk6.png)

# 结果发布

实验结果随后被发布给结果发布者。自定义结果发布者定义如下，以供参考:

```
public class ThrowIfMismatchedPublisher : IResultPublisher
{

  public Task Publish<T, TClean>(Result<T, TClean> result)
  {
     if (result.Mismatched)
     {
       var sb = new StringBuilder();
       sb.AppendLine($"{result.ExperimentName} had mismatched results: ");

       foreach (var observation in result.MismatchedObservations)
       {
         sb.AppendLine($"Value: {observation.Value}");
       }

       result.Mismatched.ShouldBeFalse(sb.ToString());
     }

     return Task.CompletedTask;
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

这将允许科学家以不匹配抛出异常的方式运行，这仅在单元测试场景中有用(对于生产场景，您可能希望记录到日志文件、错误报告服务、数据库等)。
然后可以通过设置
将该发布者提供给科学家

```
Scientist.ResultPublisher = new ThrowIfMIsmatchedPublisher(); 
```

Enter fullscreen mode Exit fullscreen mode

# 总结

Scientist 对于您想要一点一点地替换应用程序、尝试性能改进或其他形式的增量更改的情况很有价值。然而，Scientist 不适合代码产生一些外部副作用的场景，例如修改数据库、发送电子邮件或进行某种修改外部 API 调用，因为遗留例程和新例程都将运行。

该库提供了从 Ruby 到。NET 到 JavaScript 到 R 到 Kotlin 等。此外，该库的核心概念很简单，可以被任何语言使用。

最终，Scientist 是一个非常有用的库，用于比较新旧版本的代码库，并为用户提供一个缓冲区，让他们在前沿功能和无意中充当一只死金丝雀之间保持平衡。