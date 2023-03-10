# 自文档化是一个神话，如何让你的代码自文档化

> 原文：<https://dev.to/woubuc/self-documenting-is-a-myth-and-how-to-make-your-code-self-documenting-3h2n>

自文档化代码通常被描述为程序员的乌托邦，在那里你根本不需要写注释！但是代码不可能完全自我记录。这里有一些关于如何清理你的代码和写更少的注释而不忽略大局的提示。

## 什么是自文档化代码

为了编写代码，您必须了解周围的代码。为了理解它，你需要阅读它。经常反复频繁地。因此，这是每个人的最佳利益，这个代码是明确的，简洁的，并妥善记录。

自我文档化代码是许多开发人员(包括我自己)至少为自己设定过一次的目标。对大多数人来说，这意味着你应该写干净的、结构良好的代码，让它能立即明白发生了什么，所以你不需要写注释来解释它。

### 其实还好...

你可以写出人们所见过的最好的、最干净的、结构最良好的代码，但事实是:**你仍然需要写注释并记录你的代码**。你不必给*写那么多*评论，但是你不能完全停止写评论。

### 三问

当有人阅读你写的东西时，为了让他们建立理解它所需的心理模型，你的代码需要回答三个问题:

*   **这段代码的目标是什么**？
*   **it 如何实现这一目标？**
*   **为什么**它以这种方式实现目标？

让我们取一小段*不太理想的*代码，检查一下为什么要这样写，并尝试改进它，使它能回答所有三个问题。

*注意:代码示例是用 Javascript/Typescript 编写的，但是理论应该适用于任何语言*

## 代码

在尝试一个新功能时，你只是想让某些东西工作起来。因此，很容易想到“我稍后会清理这个”并写下这样的话:

```
function get() {
    const arr = db.getAll();
    return arr.map(i => i.name);
} 
```

只是你没有清理，因为截止日期快到了，在明天的脱口秀之前你还有十件事情要完成。所以这段代码在你的代码库中放了 7 个月，直到另一个开发人员——让我们称他为史蒂夫——需要更新那个文件中的某些内容。

第一次看完函数，Steve 大概会有几个疑问:

*   **这个函数得到什么**？
*   **什么**在`arr`里？
*   **为什么**会映射到`name`？

通过分析正在发生的事情，史蒂夫可以推断出这些问题的部分答案:

*   变量`arr`必须是一个数组，给定它的名称和使用`arr.map()`的事实。
*   由于被映射，`arr`中的值必须有一个名为`name`的属性。
*   该函数返回一个数组。

正如你所看到的，史蒂夫正试图根据他仅有的东西推断出**什么**和**为什么**:T4 如何。在所有类型的代码库中，这种情况经常发生，大多数开发人员对此甚至不会三思。但是最终，你花在解析这类代码上的时间和精力会累积起来，占用你处理代码的时间。

那么，我们怎样才能让史蒂夫轻松一些，让他一眼就能明白发生了什么？

### 添加评论

一个解决方案是添加一些注释来解释正在发生的事情。

```
/**
* Gets the names of all participants from the database
*/
function get() {
    // Load the participants from the database
    const arr = db.getAll();

    // Map to get just the participant's names
    return arr.map(i => i.name);
} 
```

它已经变得有点清晰了，但是添加注释正是我们想要在自文档化代码中避免的。

### 命名那些变量

与其添加注释，不如我们改变变量的名字来反映它们的意思？

```
function getParticipantNames() {
    const participants = database.getAllParticipants();
    return participants.map(p => p.name);
} 
```

现在，我们已经传达了与之前基本相同的内容，但我们不需要所有这些评论。正确命名你的变量是自文档化代码的基石之一，因为它们确切地传达了**它们所代表的**。

请注意，我仍然在`participants.map()`函数中使用了一个短变量`p`，因为从上下文中很明显它将包含参与者。

因此，通过对变量名的这些更改，我们回答了最初的问题:

*   ***这个函数得到什么**？*它获取参与者的姓名。
*   ***`arr`中的**是什么？*参与实体。
*   ***为什么**会映射到`name`？*因为我们只需要名字

Steve 下次阅读我们的代码时会发现简单多了！

### 为什么？

你可能会问的另一个问题有点牵强，并不特别关注这个函数，但我还是要问:**为什么**没有名为`database.getAllParticipantNames()`的函数，只查询数据库中的姓名(而不是我们不需要的所有其他数据)？

这可能有一百万种不同的技术原因，但是对于这个例子，假设数据库查询被缓存。这意味着当查询运行时，接收到的数据会暂时存储在内存中，因此后续调用不需要再次往返数据库。所以在这里使用相同的查询实际上是一种优化，即使我们从调用中获得了太多的数据。

这种优化是一个例子，说明你不可能仅仅用代码来交流。事实证明，纯粹的“自文档化”代码不足以描绘全貌。所以我们终究需要一些评论。

```
function getParticipantNames() {
    // Because queries are cached, using the `allParticipants` query 
    // prevents another roundtrip to the database
    const participants = database.getAllParticipants();

    return participants.map(p => p.name);
} 
```

这样，我们使得**为什么**变得更加完整。我们需要注释来完整地记录代码，但是这些代码仍然可以被认为是“自我记录的”。

### 什么？

还有最后一个问题，这个问题不是由史蒂夫问的，他必须查看你的函数，而是由汤姆问的，他必须在代码库的另一部分使用它:**这个函数的返回类型是什么？**

最好的解决方案是类型注释。像 Java、C#或 Rust 这样的静态类型语言不需要任何额外的工作，因为它们需要显式的类型信息才能工作。但是像 Javascript 和 Python 这样的动态类型语言没有这种奢侈。幸运的是，大多数动态类型语言都有(可选)类型的解决方案。Javascript 甚至有几个——我曾经和 [JSDoc 注释](https://devhints.io/jsdoc)、[流类型](https://flow.org/)和 [Typescript](https://www.typescriptlang.org/) 一起工作过。

我们已经尝试了上面完整的 JSDoc 注释，但是注释返回类型所需要的只是注释中的一个`@returns`语句:

```
/**
* @returns {string[]}
*/
function getParticipantNames() {
    // Because queries are cached, using the `allParticipants` query 
    // prevents another roundtrip to the database
    const participants = database.getAllParticipants();

    return participants.map(p => p.name);
} 
```

Typescript 和 Flowtype 使用语法符号，而不是注释。注意函数名
后面的`: string[]`

```
function getParticipantNames() : string[] {
    // Because queries are cached, using the `allParticipants` query 
    // prevents another roundtrip to the database
    const participants = db.getAllParticipants();

    return participants.map(p => p.name);
} 
```

我个人最喜欢的是打字稿。它可以帮助你创建干净的代码和严格的接口，并且在你需要的时候让重构变得更加容易。我在几乎所有的项目中都使用 Typescript。

然而，需要注意的是，向项目中添加 Typescript 通常不是一个轻易做出的决定——尤其是如果您已经开发了代码库的话——所以在开始之前一定要考虑后果。JSDoc 几乎总是开始输入代码的最容易的选择，因为它基于注释块，对代码本身没有影响。

## 结论

让我们根据以下三个问题，从这篇文章中总结出三条最重要的规则:

*   使用清晰一致的**命名、类型和函数签名**与**交流每段代码的目标**。
*   使用**结构良好的代码**向**展示**你将如何实现目标。
*   使用**评论**来解释**为什么**你以某种方式做事，尤其是当那种方式可能不明显的时候。

最后一个对很多开发人员来说是最难的，因为在你写代码的时候, **why** 通常是显而易见的。但是花一点时间想想那些需要在你写完代码几个月甚至几年后再看你的代码的开发人员。他们会感激你的。

让史蒂夫开心。记录您的自我记录代码。