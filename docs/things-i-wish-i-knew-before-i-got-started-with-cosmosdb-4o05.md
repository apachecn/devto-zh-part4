# 我希望在开始使用 CosmosDB 之前就知道的事情

> 原文：<https://dev.to/petereysermans/things-i-wish-i-knew-before-i-got-started-with-cosmosdb-4o05>

最近我一直在用 Azure 上的 Cosmos DB 做一个项目。我非常喜欢文档数据库的简单性。然而，我在工作中学到了一些东西，我希望在开始之前有人告诉我。

## 宇宙 DB？

Cosmos DB 是微软的风味文档 DB，你可以称之为运行在 Azure 上的微软的 Mongo DB。与普通的 SQL 数据库相反，文档 DB 将文档存储在数据库中。没有模式，所以不需要定义列和数据类型。相反，您将一个文档传递给文档数据库，它会将它存储在 JSON 中。这也意味着您可以在文档中添加和删除属性，而不必更改存储类似信息的其他文档。举个例子:如果数据库中有两个文档代表一个用户，第一个用户可能有一个地址，而第二个用户很可能没有地址。

第一个用户:

```
{
    id: "41bb417e-c942-4df2-96f0-cd2c3e1b2f91",
    name: "User 1",
    street: "My street",
    number: 55,
    postalCode: 1234,
    city: "Antwerp"
} 
```

第二用户:

```
{
    id: "cc4f3a5b-5e1c-4cdf-8167-dbd363538e26",
    name: "User 2"
} 
```

现在我们有了基础，让我们看看一些操作。

## 参数化查询

让我们从最基本的开始。如果您想使用 C#的参数运行查询，可以使用`SqlQuerySpec`类并将其传递给`DocumentClient`。这两个类都可以在[微软找到。Azure.DocumentDB 包](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)。

```
var querySpec = new SqlQuerySpec {
    QueryText = "select * from c where c.id = @id",
    Parameters = new SqlParameterCollection {
        new SqlParameter { 
            Name = "@id",
            Value = userId
        }
    }
}

// These properties can be found in the Azure portal.
var documentDbUri = "cosmosdb-uri";
var databaseId = "cosmosdb-database";
var collectionId = "cosmosdb-collection";

var documentClient = new DocumentClient(new Uri(documentDbUri), documentDbKey);
var database = documentClient.CreateDatabaseQuery().FirstOrDefault(d => d.Id == databaseId);
var collection = documentClient.CreateDocumentCollectionQuery().FirstOrDefault(c => c.Id == collectionId);
var queryResult = documentClient.CreateQuery(collection.DocumentsLink, query); 
```

## 数组 _ 包含

这个函数的名字本身就说明了一切。如果文档有一个数组属性，那么可以在查询中使用`ARRAY_CONTAINS`来检查数组是否包含某个值。

举个例子，这是我们的文档:

```
{
    id: "9476d2bb-6f19-483f-9a59-446ddc693050",
    name: "Jane Doe",
    roles: [
        "manager",
        "reporting"
    ]
} 
```

要查询所有具有经理角色的用户:

```
select * from c where ARRAY_CONTAINS(c.roles, "manager") 
```

如果该属性是一个对象数组，您也可以将一个对象传递给`ARRAY_CONTAINS`函数。更多信息可以在[这个堆栈溢出问题](https://stackoverflow.com/questions/35137136/documentdb-sql-with-array-contains)中找到

```
SELECT  *
FROM    food as f
WHERE   ARRAY_CONTAINS(f.servings, {"description":"bar"}, true) 
```

这个函数的另一个很好的用途是作为传统 SQL `in`关键字的替代。如果你想检查一个 ID 是否在作为参数传递的数组中，你可以这样写:

```
SELECT *
FROM Orders
WHERE CustomerId in (@customerIds) 
```

`@customerIds`将是一个从 C#传来的 id 数组。

要在 CosmosDB 实例上进行类似的查询，可以使用`ARRAY_CONTAINS`。该数组作为参数传递，并且`ARRAY_CONTAINS`可用于检查 id 是否在给定的参数中。

```
var customerIds = new List<int> { 5, 6, 7, 8 };

var querySpec = new SqlQuerySpec {
    QueryText = "select * from c where ARRAY_CONTAINS(@customerIds, c.customerId)",
    Parameters = new SqlParameterCollection {
        new SqlParameter { 
            Name = "@customerIds",
            Value = customerIds
        }
    }
} 
```

## 计数

如果您想知道给定查询的结果中有多少条记录，有两种方法可以获得结果。使用 LINQ `Count`法。

```
// These properties can be found in the Azure portal.
var documentDbUri = "cosmosdb-uri";
var databaseId = "cosmosdb-database";
var collectionId = "cosmosdb-collection";

var documentClient = new DocumentClient(new Uri(documentDbUri), documentDbKey);
var database = documentClient.CreateDatabaseQuery().FirstOrDefault(d => d.Id == databaseId);
var collection = documentClient.CreateDocumentCollectionQuery().FirstOrDefault(c => c.Id == collectionId);

var count = documentClient.CreateDocumentQuery<User>(collection.DocumentsLink)
                          .Where(u => u.city == "Antwerp")
                          .Count(); 
```

或者您可以在 SQL 查询中使用`COUNT`关键字，这个查询可以使用参数化查询来运行。

```
SELECT VALUE COUNT(d) FROM d where city = 'Antwerp' 
```

[阅读 Cosmos DB 中关于计数的更多信息](https://stackoverflow.com/questions/45183435/how-to-construct-iqueryable-query-using-linq-when-i-just-need-count-without-read)

## 客户端

尽管 Azure portal 允许对文档数据库进行任何操作，但这是一种非常笨拙的工作方式。我非常喜欢使用专用客户端。尽管微软 Azure Storage Explorer 仍然有一些粗糙的边缘，Cosmos DB 支持仍然处于预览阶段，但它工作得很好。除了管理 Azure 上的 Cosmos DB 中的文档，你还可以管理你的存储帐户。

另一个选择是开源的 Cosmos DB explorer，但是它的选项比 Storage Explorer 少。

## 喜欢关键字

在传统的 SQL 中，`like`关键字可以用来过滤某个值的字符串字段。CosmosDB 预见到类似关键字的[等价物。让我们看一下不同的选项。](https://stackoverflow.com/questions/36663017/does-documentdb-support-the-like-keyword-in-queries)

### 显示特定列包含特定值的所有行

SQL:

```
select * from Table where Column like '%value%' 
```

CosmosDB:

```
select * from d where CONTAINS(d.Column, 'value') 
```

### 显示特定列以特定值开始的所有行

SQL:

```
select * from Table where Column like 'value%' 
```

CosmosDB:

```
select * from d where STARTSWITH(d.Column, 'value') 
```

### 显示特定列以特定值结束的所有行

SQL:

```
select * from Table where Column like '%value' 
```

CosmosDB:

```
select * from d where ENDSWITH(d.Column, 'value') 
```

## 
 [T3】
结论](#conclusion) 

如您所见，如果您将 CosmosDB 与关系型 SQL 数据库进行比较，会发现有些事情的工作方式完全不同。我希望这能让您对 Cosmos DB 有一个良好的开端。如果您有任何其他提示或意见，请随时联系我。