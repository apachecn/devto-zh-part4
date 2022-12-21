# CFScript 中的表单处理第二部分:将表单条目保存到数据库

> 原文：<https://dev.to/mikeborn/form-processing-in-cfscript-part-two-saving-form-entries-to-the-database-4ko1>

如果一棵树倒在树林里，没有人听见，它真的倒了吗？同样，如果用户提交了一个表单，但是它没有以某种形式持久化，那么这个表单提交**是没有价值的**。

*   [将表单提交保存到数据库](#saving-form-submissions-to-the-database)
    *   [方法一:用硬编码表设计存储表单](#method-one-storing-the-form-with-a-hard-coded-table-design)
    *   [方法二:将表单存储为 JSON 包](#method-two-storing-a-form-as-a-json-packet)
*   [结论](#conclusion)

今天我们将讨论表单处理中非常重要的，也许是最重要的步骤:将提交的表单保存到数据库中。

## 将表单提交保存到数据库

当处理表单提交时，第一步也是最重要的一步是**持久化**数据——确保表单数据被保存并可供将来参考。这篇文章将向您展示如何使用两种主要方法在 SQL 数据库中存储表单数据。这两种方法都不难，但是我发现每种方法都有自己的用处，适合不同的用例。

### 方法一:用硬编码表设计存储表单

这里有一个简单的方法——只需将每个表单提交保存到一个新行中，每个表单域有一个单独的列。

构建 HTML 表单后，创建一个数据库表，为每个表单字段添加适当的列:

```
CREATE TABLE contactForm(
  id VARCHAR(35) NOT NULL UNIQUE PRIMARY KEY,
  name VARCHAR(255),
  email VARCHAR(255),
  message TEXT,
  dateSubmitted TIMESTAMP
); 
```

然后在表单处理程序中，使用`queryExecute()`插入行就很容易了。

```
var sql = " INSERT INTO contactForm(id, name, email, message, dateSubmitted)
    VALUES(:id, :name, :email, :message, :dateSubmitted) ";
var params = {
    id: createUUID(),
    name: form["name"],
    email: form["email"],
    message: form["message"],
    date: { value: now(), cfsqltype: "timestamp" }
};
queryExecute( sql, params ); 
```

我个人很喜欢这样声明`sql`和`params`变量，但是如果你是一个只使用内联的人，你可以这样写:

```
queryExecute( " INSERT INTO contactForm(id, name, email, message, dateSubmitted)
    VALUES(:id, :name, :email, :message, :dateSubmitted) ", {
    id: createUUID(),
    name: form["name"],
    email: form["email"],
    message: form["message"],
    date: { value: now(), cfsqltype: "timestamp" }
}); 
```

这种方法很酷的一点是，修复程序现在可以捕捉你的 SQLi 漏洞

这种方法很简单，效果也很好。构建与表单匹配的表模式的好处是，由于良好的数据库集成，存储和检索数据既简单又强大。基本上，让我们的表格设计与表单设计相匹配，无需额外的工作就能为我们提供强大的搜索、排序和查询功能。

### 方法二:将表单存储为 JSON 包

对于较大的表单，创建表模式可能是一件痛苦的事情。如果那个大的形式经常改变，我们就在谈论一个真正的痛苦。我无法告诉你我已经创建了多少个表模式来匹配一个联系人或捐赠表单，只是为了在表单不可避免地发生变化时更改这些表。

因此，对于更大、更复杂的表单，我建议将表单提交存储为 JSON 字符串。将主要字段(姓名和电子邮件)放在专用的表列中进行排序和搜索仍然很有用，但是 JSON 对于大多数其他字段来说已经足够了。另存为 JSON 意味着我们不需要为每个表单输入创建一个定制字段，我们可以存储表单提交*和*——更少的设置和维护。

创建存储表要简单得多:

```
CREATE TABLE formSubmissions(
  id VARCHAR(35) NOT NULL UNIQUE PRIMARY KEY,
  form TEXT,
  dateSubmitted TIMESTAMP
); 
```

而将表单数据作为 JSON 插入就像使用`serializeJSON(form)`一样简单。当从表中读取表单提交时，我们可以使用`deserializeJSON()`将 JSON 字符串解码回 CF 结构。在 Lucee 上，你可能需要在 Lucee admin 中[启用“保留密钥箱”。请注意，Adobe](https://www.bennadel.com/blog/3667-understanding-struct-key-casing-using-serializejson-in-lucee-5-3-2-77.htm) [ColdFusion 11 及更高版本默认情况下会保留关键大小写](https://coldfusion.adobe.com/2014/04/language-enhancements-in-coldfusion-splendor-improved-json-serialization/)。

```
var sql = " INSERT INTO formSubmissions(id, form, dateSubmitted)
    VALUES(:id, :form, :dateSubmitted) ";
var params = {
    id: createUUID(),
    form: serializeJSON(form),
    date: { value: now(), cfsqltype: "timestamp" }
};
queryExecute( sql, params ); 
```

这种方法的一个显著缺点是在数据库中进行本地排序和搜索变得更加困难——根据您的数据库软件的不同，可能会更加困难。但是，如果您使用的是 MySQL 5.7.8 或更新版本，`JSON`数据类型使得排序或过滤相当容易:

```
SELECT id
    , JSON_EXTRACT(data, '$.name') AS name
    , JSON_EXTRACT(data, '$.email') AS email 
FROM formSubmissions
ORDER BY name ASC 
```

我必须指出，这种方法并不完美。将数据存储为一个大的旧 JSON 字符串并不完全符合[第一范式](https://en.wikipedia.org/wiki/First_normal_form)，尝试通过提取的 JSON 字段进行排序或搜索可能会带来不可忽视的性能损失。不管怎样，我发现这种方法很方便，因为它只需要很少的维护。编辑 HTML 表单通常比编辑 SQL 表结构更容易，这种方法意味着第一个表单的更改不需要第二个表单的更改。

更多信息，请查看 *[如何在 MySQL 数据库中使用 JSON 数据字段](https://www.sitepoint.com/use-json-data-fields-mysql-databases/)* 。

## 结论

坚持并不难。这篇博文旨在强调一种更简单的存储表单条目的方法；如果您稍微考虑一下表单处理，就可以在表单的整个生命周期中节省大量的维护时间。