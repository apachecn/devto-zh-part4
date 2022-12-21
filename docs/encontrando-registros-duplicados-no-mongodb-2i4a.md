# 没有 MongoDB

> 原文：<https://dev.to/shadowlik/encontrando-registros-duplicados-no-mongodb-2i4a>

需要在您的数据库中查找重复的记录[【mong odb】](https://www.mongodb.com/)？本文将说明如何使用聚合方法查找重复的文档(记录)。

## 数据库

假设我们从一个用户列表中大量加载了数据，并想知道同一 CPF 中有多少条重复记录。

我们将以下列文件结构为例:

```
db.list.findOne(); { "\_id" : ObjectId("8902a01b2ec12a2383328b61"), "nome" : "Henrique Marques Fernandes", "site": "https://marquesfernandes.com", "cidade" : "SP", "cpf": "182.983.460-68" } 
```

## 查找带有聚合的重复数据

为此，我们将使用运算子和[【match】](https://docs.mongodb.com/manual/reference/operator/aggregation/match/)的聚合方法，以 CPF 字段为唯一标识符对我们的结果进行分组和筛选，并添加两个新字段:字段□

```
db.list.aggregate([{$group: { \_id: {cpf: "$cpf"}, idsUnicos: {$addToSet: "$\_id"}, total: {$sum: 1} } }]); 
```

上述查询将返回所有 CPF 及其计数的列表。现在，要仅查找并返回具有一个或多个重复记录的 CPFs，我们必须添加$match 运算符，以便仅筛选在 total:t0 字段中具有多个记录的查询

```
db.list.aggregate([{$group: { \_id: {cpf: "$cpf"}, idsUnicos: {$addToSet: "$\_id"}, total: {$sum: 1} } }, {$match: { total: {"$gt": 1} } }]); 
```

邮件[在 mongo BD](https://marquesfernandes.com/2019/07/29/encontrando-registros-duplicados-no-mongodb/)发现重复记录，首先出现在 [Henrique Marques Fernandes](https://marquesfernandes.com) 。