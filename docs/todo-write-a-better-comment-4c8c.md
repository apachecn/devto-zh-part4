# //TODO:编写更好的注释

> 原文：<https://dev.to/adammc331/todo-write-a-better-comment-4c8c>

这是在纽约 2019 年 Droidcon 上展示的。点击此处查看视频:

[https://player.vimeo.com/video/362742364](https://player.vimeo.com/video/362742364)

* * *

本月早些时候，Java 官方 Twitter 帐户发布了一条非常有争议的推文，告诉您停止编写代码注释。

> ![unknown tweet media content](img/576d1fbd625f2d6ffc66f90d1b53182f.png)![Java profile image](img/ca933fae894acd44caf826b6593c98a9.png)Java@ Java![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)停止编写代码评论
> 
> [# clean coder](https://twitter.com/hashtag/cleancoder)
> 
> [medium.com/@bpnorlander/s…](https://t.co/JnYSj9NGhi)16:31PM-02 Jun 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1135222291007062021)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1135222291007062021)299】

它链接到一篇[中型文章](https://blog.usejournal.com/stop-writing-code-comments-28fef5272752)，这篇文章解释了代码注释的一些问题，并建议解决方案是完全停止编写代码注释。

这是个糟糕的建议。看到它来自如此流行的编程语言的官方社交媒体账户，我感到震惊。代码注释可以为您的代码库提供很多价值，并真正帮助下一个继承您代码的人。我还发现这篇文章过于苛刻:

> 当你需要写注释的时候，通常意味着你没有写出足够有表现力的代码。每次写评论都应该感觉肚子一阵刺痛。

我在这里告诉你，你是*而不是*写评论的失败者。你也不应该为此感到内疚。作为一个社区，我们不应该停止写评论。我们需要学会写*更好的*评论。我希望这将作为一个指南，帮助你做到这一点。

# 风险评论构成

首先，让我们对评论给我们带来的好处和风险有一个基本的了解。

注释是有帮助的，因为它们可以向代码的读者提供代码本身可能无法提供的洞察力。

注释是一个潜在的风险，因为它们可能会过时。更改代码并不能保证我们更改了注释，因此我们可能会有误导我们的注释。这就是为什么，默认情况下，我们应该尽量避免评论。

然而，我们不应该为了避免而避免评论。我们应该确保我们是有意写评论的。

根据我的经验，我遇到的代码注释可以分为三类:

*   这个评论是多余的。
*   这个评论是无益的。
*   这个评论很有帮助。

我们的目标是确保我们所有的评论都在第三个桶里——有帮助的。我们可以从删除任何不必要的评论开始，然后确保剩余的评论对读者有帮助。

# 避免不必要的评论

有些代码注释就是不需要。在这种情况下，我们应该摆脱他们。否则，我们的文件中会有更多的混乱，评论变得过时的风险更大，实际上从不必要的变成无用的。

## 删除多余的注释

我们可以删除的第一种不必要的注释是多余的。

您可能有记录一个方法及其所有参数的冲动，但有时您的代码太富于表现力，很难写出独特的注释。这可能会让你想到这样的事情:

```
interface AccountDAO {
    /**  * Inserts an account into the database.  *  * @param[account] The account that we're inserting.  * @return The ID of the inserted account.  */
    suspend fun insert(account: Account): Long
} 
```

关于 insert 方法的文档并不是那么必要。第一行提供了一些信息，但是给定方法名和它所属的接口，就可以推断出它做了什么。account 参数的解释再次重复了代码已经告诉我的内容。然而，return 语句可以提供一些好处。

所以这个评论的 2/3 为我提供了代码已经提供给我的信息。这意味着它是多余的。我认为没有评论比多余的评论更好，因为它不会让我们面临评论过时的风险。所以我建议只包括有益的部分:

```
interface AccountDAO {
    /**  * @return The ID of the inserted account.  */
    suspend fun insert(account: Account): Long
} 
```

### 一个例外

这条规则的一个例外是，如果您正在构建一个公共 API 或库供他人使用。在这种情况下，您应该记录所有可用的内容。尽管如此，我们可以对我们写的评论深思熟虑，并确保它们是有帮助的，我们将在后面讨论。

## 修改代码以避免需要注释

让我们继续基线目标，以避免评论。你已经开始写一个了，你已经确定它不是多余的了。接下来，您应该看看是否可以重写代码，使这个注释变得不必要，这样您就可以删除它。

下面是一个简短的一行注释的例子，它有助于阐明一个方法可以做什么:

```
// Saves data to database
fun saveData() {
} 
```

这个评论的作者(剧透:我)认为这个评论有助于澄清这个方法在做什么。事后看来，我可以完全避免这个注释，只需创建一个更有表现力的方法名:

```
fun saveDataToDatabase() {
} 
```

另一个常见的例子是，当我们编写一个做多件事的方法时，我们想把这个方法的各个部分分开，就像这样:

```
fun transferMoney(fromAccount: Account, toAccount: Account, amount: Double) {
   // create withdrawal transaction and remove from fromAccount
   // ...

   // create deposit transaction and add from toAccount
   // ...
} 
```

先不说让一个方法做多件事会破坏其他最佳实践，这导致了我们想要避免的额外注释。避免它们的一种方法是将这些步骤放入它们自己的方法中，并恰当地命名。

```
fun transferMoney(fromAccount: Account, toAccount: Account, amount: Double) {
   withdrawMoney(fromAccount, amount)
   depositMoney(toAccount, amount)
} 
```

# 撰写有帮助的评论

如果你已经读完了上一节，你仍然觉得你应该加入你的评论，因为它是一个公共 API，或者因为你觉得它提供了额外的价值，我们需要问问自己它是否对读者有帮助。

## 注释告诉你为什么，代码告诉你什么

我不记得第一次听到这句话是在哪里，但它总是让我产生共鸣。代码应该告诉你*什么*正在发生，但是注释可以告诉你*为什么*。

让我们看看我最近写的一个糟糕的评论，它重复了*什么* :

```
/**  * A list of updated questions to be replaced in our list by an interceptor.  */
private val updatedQuestions: MutableMap<Long, Question> = HashMap() 
```

这个评论是*几乎*有帮助的，但是我不得不把它归入无帮助的一类。它为我提供了一点额外的信息，但主要是重复代码已经告诉我的内容。在上一节中，我们说我们应该删除这些，但是这背后的故事是，我实际上想提供我为什么添加这个散列表的见解。

让我们通过关注*为什么* :
把这个评论变成一个有用的评论

```
/**  * The `PagedList` class from Android is backed by an immutable list. However, if the user answers  * a question locally, we want to update the display without having to fetch the data from the network again.  * 
 * To do that, we keep this local cache of questions that the user has answered during this app session,  * and later when we are building the list we can override questions with one from this list, if it exists,  * which is determined based on the key of this HashMap which is the question ID. 
 */
private val updatedQuestions: MutableMap<Long, Question> = HashMap() 
```

现在我们有了一个注释，它实际上提供了一些关于我为什么保留本地散列表的见解，这是因为我想覆盖一个不可变的列表，我不容易修改它(谢谢，Android)。因此，我们有一个有用的评论。

## 有例子的评论很有帮助

我们不断地提到应该避免多余的评论。一个困难的例子是，当我们创建一个公共 API 供其他人使用时，我们希望确保一切都有文档记录。这带来了一些重复信息的风险。这里还有一个例子:

```
class Pokedex {
    /**  * Adds a pokemon to this Pokedex.  * 
 * @param[name] The name of the Pokemon.  * @param[number] The number of the Pokemon.  */
    fun addPokemon(name: String, number: Int) {

    }
} 
```

如果我们*有*保留这些评论，我们可以尽力确保它们是有帮助的。如果你不能提供额外的信息，一个方法就是提供例子:

```
class Pokedex {
    /**  * Adds a pokemon to this Pokedex.  * 
 * @param[name] The name of the Pokemon (Bulbasaur, Ivysaur, Venusaur).  * @param[number] The number of the Pokemon (001, 002, 003).  */
    fun addPokemon(name: String, number: Int) {

    }
} 
```

这并不是很好，但是我们并没有为读者重复信息，而是给了他们一个预期的例子。

## 链接到外部资源会有帮助

我们都在 StackOverflow 上找到了解决问题的方法。有时这是一件简单的事情，我们可以复制并粘贴到我们的项目中，但有时它可能是我们重用的整个文件或方法。在这种情况下，读者可能会搞不清楚这些代码或概念来自哪里，或者为什么需要它们。

您可以通过链接到适当的问题或答案来提供这些见解。最近，由于 StackOverflow 的帮助，我创建了一个编程式的 viewparager，如果这个 viewparager 有任何问题的话，我想对它表示感谢，并确保我有更多的信息可以参考

```
/**  * A ViewPager that cannot be swiped by the user, but only controlled programatically.  *  * Inspiration: https://stackoverflow.com/a/9650884/3131147  */
class NonSwipeableViewPager(context: Context, attrs: AttributeSet? = null) : ViewPager(context, attrs) {
} 
```

## 可操作的评论是好的评论

这可能会令人震惊，因为评论通常不被认为是可操作的。可操作的评论是有帮助的，因为你给了读者一些可以带走的东西，防止他们问“我应该如何处理这些信息？”

我认为我们可以关注两种类型的评论，因为它们是可操作的。

### 待办事宜注释

一般来说，TODO 注释是一个很大的风险。我们可能会看到一些我们想以后做的事情，所以我们会很快放弃，以为我们会回来做，但从来没有这样做。

如果你要写一个 TODO 注释，你应该做两件事情中的一件:
1)就这么做
2)链接到你的外部问题跟踪器。

TODO 注释有有效的用例。也许你正在开发一个大的特性，你想做一个只修复部分特性的拉请求。您还想调用一些仍需完成的重构，但您将在另一个 PR 中修复这些重构。链接到让你负责的东西，比如一张 JIRA 的票:

```
//TODO: Consolidate both of these classes. AND-123 
```

这是可行的，因为它迫使我们转到问题跟踪器并创建一个票证。这比可能永远不会再看到的代码注释更不容易丢失。

### 不赞成意见

另一种情况是，如果你在你不赞成的方法或类周围留下了一个注释，你想告诉用户下一步该怎么做。

我直接从 Android 上举了一个例子，他们反对`CoordinatorLayout.Behavior`而支持`CoordinatorLayout.AttachedBehavior` :

```
/**
 * ...
 * @deprecated Use {@link AttachedBehavior} instead
 */
@Deprecated
public interface DefaultBehavior {
    ...
} 
```

这个评论实际上是有帮助的，因为它不仅告诉我一些东西被弃用了，还告诉我什么东西取代了它。

# 结论

虽然评论会带来风险，但并不是所有的评论都是不好的。重要的是，您理解这种风险，并作为程序员尽自己的努力来避免这种风险。归结起来就是:

*   删除你实际上不需要的评论。
*   如果可以的话，重写代码删除注释。
*   确保剩余的评论是有帮助的。
    *   这可以通过阐明*为什么*、提供例子或采取行动来实现。

有问题吗？请在评论中告诉我！我也很容易在推特上联系到。