# 用函数式编程消除 C#中的空值

> 原文：<https://dev.to/integerman/eliminating-nulls-in-c-with-functional-programming-iaa>

这是一篇短小精悍的文章，展示了`Option`类如何将函数式编程概念引入 C#代码库并防止空引用异常。

本文将使用非常流行的语言 ext T1 作为最流行的语言。为面向对象语言(除了 F#)构建的. NET 函数式编程库。

# 安装语言-扩展库

为了使用 [language-ext](https://github.com/louthy/language-ext) 库，你需要[添加一个 NuGet 引用到包](https://www.nuget.org/packages/LanguageExt.Core)。这可以通过 Visual Studio 中的 NuGet 包管理器或从包管理器控制台运行`Install-Package LanguageExt.Core`来完成。

# 什么是选项？

选项是一个引用类型，要么包含*一些*值，要么没有*。作为一个例子，请看下面的方法，它搜索一个字典并从中返回一个值:* 

```
public Option<ResumeKeyword> GetKeyword(
   IDictionary<string, ResumeKeyword> keywords, 
   string word) { 

  // Ensure we're searching for a lower-cased non-null word
  string key = word ?? word.ToLowerInvariant() : string.Empty;

  // If the keyword is present, return it
  if (keywords.ContainsKey(key)) {
    return keywords[key]; // Implicitly converted to Some<ResumeKeyword>
  }

  // No keyword found
  return Option<ResumeKeyword>.None;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个方法中，由于返回类型是`Option<ResumeKeyword>`，返回类型将是`Some<ResumeKeyword>`或`None`。结果永远不会显式为`null`，因此调用者永远不必担心空值。

编译器能够将标准值转换成`Some`值，但是`None`返回类型的语法不太理想，因为我们可以开始看到使用`Option`增加了复杂性。

幸运的是，如果我们在文件的顶部添加以下代码，返回类型可以被简化:`using static LanguageExt.Option<ResumeKeyword>;`

这允许我们简单地`return None;`而不是做`return Option<ResumeKeyword>.None;`，这样读起来明显更好。

# 使用选项工作

现在你有了一个`Option`，你希望能够和它一起工作。这就是`Option`的吸引力开始发挥作用的地方。因为我们正在使用一个`Option`，编译器不允许我们编写像下面这样有潜在错误的代码:

```
// Give a bump for various words in the title
foreach (var word in job.Title.Split())
{
    var keyword = FindKeyword(keywordBonuses, key);
    jobScore += keyword.Value; // Does not compile
} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码不能编译，因为`keyword`是一个`Option<ResumeKeyword>`而不是一个`ResumeKeyword`，所以它不能直接访问`ResumeKeyword`的成员。这种不便实际上是一件好事，因为我们知道`FindKeyword`有时找不到任何关键字匹配，如果我们忘记检查`null`，这将导致标准应用程序中出现`NullReferenceException`。

相反，我们写下如下:

```
// Give a bump for various words in the title
foreach (var word in job.Title.Split())
{
    var keyword = FindKeyword(keywordBonuses, key);
    jobScore += keyword.Some(k => k.Value)
                       .None(0);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们说，如果*的某个*关键字存在，我们希望通过`ResumeKeyword`的`Value`来增加分数，如果*没有*关键字存在，我们希望将该单词的分数设为 0 或填充符。

编译器的类型检查将强制执行这些规则，并确保我们得到正确的类型，迫使我们考虑该值是否存在并相应地采取行动。

如果您只想在某个值存在或不存在的情况下做一些事情，并且不需要实际的值，那么您可以检查`IsSome`或`IsNone`属性。比如:

```
if (keyword.IsNone) {
   Console.WriteLine($"No value defined for keyword '{word}'); } 
```

Enter fullscreen mode Exit fullscreen mode

# 可空类型呢？

。NET 通过`Nullable<T>`(通常编码为`T? myVar`)已经有了[可空类型](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/nullable-types/)的概念。

可空类型是将值类型表示为可空对象的一种方式。这与期权不同，因为:

1.  选项对引用类型和值类型都有效
2.  当`Nullable<T>`提供了`HasValue`和`Value`成员时，`Option<T>`提供了方便的方法来以特定的方式对*某些*或*无*的情况做出反应，正如我们上面看到的。

# 总结

Language-Ext 的选项类迫使你做出明智的决定，并以安全的方式处理可能丢失的值。

这样做的缺点是增加了读写代码的复杂性，但是在代码库的质量关键方面或经常可能出现空值的地方，将`Option<T>`合并到代码中可能是有意义的。

此外，记住`Option<T>`只是 Language-Ext 提供的一小部分功能。查看完整的库以了解更多信息，或者关注我以获得未来帖子的通知。

如果你对消除整类缺陷的更多方法感兴趣，可以看看我的文章《让缺陷变得不可能》。

[![integerman](img/c4617a5228f5ea62d27f47eb980b349d.png)](/integerman) [## 使缺陷成为不可能

### matt Eland Aug 31 ' 195min read

#testing #dotnet #javascript #codequality](/integerman/making-defects-impossible-16c)*