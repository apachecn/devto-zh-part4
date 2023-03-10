# 说出你的限制

> 原文：<https://dev.to/techelevator/name-your-constraints-4n10>

这是一篇简短而有趣的文章，讨论了命名数据库约束的重要性。它是从最熟悉 SQL Server 数据库的人的角度编写的。

## 什么是约束？

简而言之，约束是某种形式的数据库规则。SQL Server 支持的各种类型的约束包括:

*   不为空
*   独一无二的
*   支票
*   主关键字
*   外键
*   默认
*   索引

当添加这些约束时，它们在数据库内部定义了一些行为——通常是实施一些规则，如参照完整性、唯一性或内容验证。在键约束和索引约束的情况下，约束也会对索引产生影响。

## 约束名称

出于数据库引用的目的，约束必须有一个名称。但是，基本的 SQL 语法不要求约束名称，因此，如果添加的约束没有名称，SQL Server 将自动为您生成一个名称。

例如:

```
CREATE TABLE Resumes
(
  ID INT NOT NULL,
  Name NVARCHAR(50) NULL,
  UNIQUE (ID)
) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`UNIQUE`约束将被添加一个类似如下的随机名称:`UQ__Constrai__1234AB67C890D123`

*   请注意，在本例中，您更有可能使用主键，但是为了便于说明，我们使用了一个 unique 约束*

## 命名一个约束

具有随机名称的约束的问题在于，如果生产数据库和质量保证数据库具有不同的约束名称来表示相同类型的约束，则很难将该约束作为变更脚本的一部分删除。

因此，在创建约束时命名它是很重要的。检查您正在处理的每种类型的约束的语法，但是我们之前的例子会变成:

```
CREATE TABLE Resumes
(
  ID INT NOT NULL,
  Name NVARCHAR(50) NULL,
  CONSTRAINT UC_ResumeID UNIQUE (ID)
) 
```

Enter fullscreen mode Exit fullscreen mode

从那里，您可以很容易地按名称删除`UC_ResumeID`,而不管脚本是在哪个环境下运行的。

再一次，这是一个有点像稻草人的例子，因为你通常会使用一个主键，但这应该说明了没有明确命名的约束时可能出现的核心问题。