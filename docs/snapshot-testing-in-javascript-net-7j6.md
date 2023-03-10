# JavaScript &中的快照测试。网

> 原文：<https://dev.to/integerman/snapshot-testing-in-javascript-net-7j6>

今年早些时候，我发现了脸书制作的奇妙的 Jest JavaScript 单元测试库，通常与 React 相关联。Jest 建立在 Jasmine 之上，但是增加了一些非常好的特性，包括一个叫做快照测试的东西。

虽然单元测试通常被认为是精确的外科手术工具，集中于孤立地测试一两件非常小的事情，但是快照测试却是截然相反的。快照测试实际上是针对状态验证的猎枪。快照测试查看一段状态，并将其与该状态的存储版本进行比较。如果有任何差异，单元测试将会失败，该状态的 JSON 表示中的差异报告将作为测试失败输出。

这对于“牵制测试”非常有用。这些测试旨在捕捉当前应用程序的状态和行为，不管它是对是错。如果相同的操作后来产生了稍微不同的结果，那么测试应该失败，开发人员将面临状态的改变——无论是有意的还是意外的。在意外变更的情况下，开发人员修复了他们刚刚引入的潜在错误，测试返回到通过状态。对于有意的变更(比如修复之前在快照中出现的 bug)，快照实际上是错误的，因此开发人员更新了快照，测试再次通过。

快照测试并不十分清楚它们测试的确切行为，但是它们确实为那些还没有被特定单元测试测试过的东西提供了很好的安全网。

# 玩笑式的 JavaScript 快照测试

开玩笑地说，快照测试可能看起来像这样:

```
myVariable.shouldMatchSnapshot(); 
```

就是这样。

第一次运行测试时，会生成一个快照(因为不存在)并放在一个特殊的`__snapshots__`目录中。这个文件应该被置于版本控制之下，因为它对测试行为至关重要。

随后，Jest 会根据存储的快照文件进行检查，并根据任何差异决定通过或失败。

如果您需要更新您的快照，您只需向 Jest 传递一个参数，它就会自动更新快照。

`jest -u`

然后，更新的快照文件被提交到源代码控制，并且在没有`-u`标志的情况下进行后续的测试运行。

参见[https://jestjs.io/docs/en/snapshot-testing](https://jestjs.io/docs/en/snapshot-testing)了解 Jest 快照测试的更多细节，以及 Jest 测试框架的设置步骤。

# 快照测试中。带抓鱼的网

自从。NET 有着从其他语言掠夺概念、库和技术的光荣历史，我花了相当多的时间用它编程。NET 中，我寻找能够提供类似 Jest 的快照测试功能的东西。

虽然有很多可用的库，比如非常多样化和合格的 ApprovalTests.Net，但为了简单起见，我选择了更简单和更自动化的面向单元测试的 Snapper。

Snapper 中的一个示例测试如下所示:

```
[Fact]
public void SamplePinningTest()
{
  // Arrange
  var resume = new ResumeInfo("Matt Eland");
  resume.Jobs.Add("Software Engineering Manager");
  // Act
  var result = Analyze(resume);
  // Assert
  result.ShouldMatchSnapshot();
} 
```

这将在`__snapshots__`目录中查找快照 JSON 文件，执行 diff，如果检测到任何差异，测试就会失败。

快照失败出现在单元测试结果中，如下所示:

```
Snapper.Exceptions.SnapshotsDoNotMatchException : 
Snapshots do not match
- Snapshot
+ Received

{
  "Resume": {
-      "FullName": "Some Body",
+      "FullName": "Matt Eland",
    "Jobs": [
      {
-          "Title": "Test Engineer",
+          "Title": "Software Engineering Manager",
        "Organization": "Universal Exports",
        "MonthsInJob": 42,
...
    ]
  },
-    "Score": 42
+    "Score": 2147483647
} 
```

在这方面，Snapper 在很大程度上遵循 Jest 的惯例，但是 Jest 使用命令行参数来更新其快照，Snapper 必须做一些不同的事情。

Snapper 要求您在测试方法中添加或删除`UpdateSnapshots`属性，以便生成初始快照或更新快照。

我认为 Snapper 应该足够智能，能够像 Jest 一样自动生成快照，但在本文发布之日，它还没有这样做。

总的来说，快照测试是一种省力的方法，可以在您将测试覆盖范围扩展到更具体、更有针对性的单元测试时，为您的应用程序增加一个广泛的安全网。Jest 和 Snapper 是易于使用的简单框架，使得快照测试在通用编程语言中可用。

试试快照测试，让我知道你的想法。希望你能看到它在你的测试套件中的特殊作用，减少你无意中改变应用程序行为的几率。