# 如何为 SQL 查询编写单元测试

> 原文：<https://dev.to/dataform/how-to-write-unit-tests-for-your-sql-queries-2hd7>

我之前曾[写过](https://dev.to/dataform/consider-sql-when-writing-your-next-processing-pipeline-1ojg)关于我认为我们应该更喜欢用纯 SQL 编写处理管道。然而，SQL 和更广泛使用的语言之间的一个很大的区别是，那些其他语言通常有很强的单元测试传统。

通常，当我们在 SQL 的上下文中谈论“测试”时，我们实际上并不是指单元测试。相反，这个术语通常指的是数据测试，它实际上是数据本身符合一些测试标准的断言。

单元测试不是断言。单元测试通过在一些固定的输入集合上运行 SQL 查询来验证该查询的逻辑。断言必然依赖于它们验证的真实数据集，而单元测试不应该依赖于任何真实数据。

### 单元测试的好处

单元测试是软件工程中的标准实践。单元测试有助于确保复杂的逻辑部分或组件之间的复杂交互按预期工作——并且随着周围代码的变化继续按预期工作。

单元测试不应有任何外部依赖关系；测试在一些伪造的输入上运行有问题的代码，确保代码单元之外的更改不会影响测试。这意味着测试的成败完全取决于代码的逻辑。因此，如果测试失败，您就知道从哪里开始调试。

### 为什么 SQL 单元测试不普遍？

在标准语言中，单元测试通常包括将假输入注入到被测代码中，并检查输出是否与预期结果相匹配。然而，SQL 脚本不会标记它们的输入数据集——通常，它们只是在 FROM 子句中静态内联定义的。这使得向您的 SQL 代码中注入虚假的输入测试数据变得困难。

这样做的结果是大多数 SQL 代码没有经过测试。

### 解

各种 SQL 框架允许您定义 SQL 与其输入之间的间接层；即声明并标记查询所依赖的输入数据集。单元测试框架可以使用这种间接方式用伪造的版本替换真实的输入数据。

然后，我们可以使用一些伪造的输入来运行测试中的代码，并将输出结果行与一组预期的输出进行比较。如果被测试代码的实际输出与预期输出匹配，测试通过；如果没有，它就失败了。

这种技术很简单，并赋予您真正的权力来验证 SQL 脚本是否如您所想的那样工作。您可以将真实数据当前可能不包含的虚假输入传递给 SQL，让您相信它能够可靠地处理大量数据。

### 测试用例以数据形式支持

当使用 Dataform 的[增强型 SQL](https://docs.dataform.co/guides/datasets/?utm_medium=organic&utm_source=dev_to&utm_campaign=write_sql_unit_tests) 时，可以使用`ref()`或`resolve()`函数引用输入数据集。这一功能为我们提供了一种简单的方法，将虚假的输入数据集注入到脚本中，从而使用户能够编写单元测试。

我们定义了一种新的数据形式脚本:`test`。在`test`查询中，您可以指定:

*   您正在测试的查询
*   伪造的输入，每个都标有引用的名称
*   对伪造的输入运行查询的预期输出

在幕后，当您运行测试时，我们用您伪造的输入数据动态地替换对`dataset`查询的输入。然后，我们运行`dataset`查询，以及定义预期输出的查询，并检查结果行是否匹配。简单！

### 举个例子

这里有一个使用 Dataform 的 JavaScript API 编写的实例。

```
// First, define a dataset - we’ll follow this up with the unit test.
publish("age_groups").query(
  ctx =>
    `
      SELECT
      FLOOR(age / 5) * 5 AS age_group,
      COUNT(1) AS user_count
      FROM ${ctx.ref("ages")} GROUP BY age_group
    `
);

// Now, define the unit test.
test("test_age_groups")
  // Specify the name of the dataset under test.
  .dataset("age_groups")
  // Provide the fake input “ages” dataset.
  .input(
    "ages",
    `
      SELECT 15 AS age UNION ALL
      SELECT 21 AS age UNION ALL
      SELECT 24 AS age UNION ALL
      SELECT 34 AS age
    `
  )
  // Provide the expected output of running “age_groups” on the “ages” dataset.
  .expect(
    `
      SELECT 15 AS age_group, 1 AS user_count UNION ALL
      SELECT 20 AS age_group, 2 AS user_count UNION ALL
      SELECT 30 AS age_group, 1 AS user_count
    `
  ); 
```

或者，如果您喜欢使用 Dataform 的增强型 SQL，单元测试将如下所示(注意，为了简单起见，省略了发布数据集):

```
config {
  type: "test",
  dataset: "age_groups"
}

input "ages" {
  SELECT 15 AS age UNION ALL
  SELECT 21 AS age UNION ALL
  SELECT 24 AS age UNION ALL
  SELECT 34 AS age
}

SELECT 15 AS age_group, 1 AS user_count UNION ALL
SELECT 20 AS age_group, 2 AS user_count UNION ALL
SELECT 30 AS age_group, 1 AS user_count 
```

更多详情，请参见我们的[文档](https://docs.dataform.co/guides/tests/?utm_medium=organic&utm_source=dev_to&utm_campaign=write_sql_unit_tests)。

我们已经发布了这个功能，作为我们的 [@dataform NPM 包](https://www.npmjs.com/package/@dataform/cli)的 v1.0.0 版本的一部分。Dataform Web 也将很快支持测试用例。让我们知道你的想法！