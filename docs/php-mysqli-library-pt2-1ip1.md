# PHP MySQLi 库 Pt2

> 原文：<https://dev.to/godsgood33/php-mysqli-library-pt2-1ip1>

在我之前的文章[第一部分](https://dev.to/godsgood33/php-mysqli-library-pt1-5778)中，我谈到了我写的 PHP MySQL 库的基础知识。在这篇文章中，我将讨论你可以运行的不同查询。我将假设您正在自己的数据库类中操作，该类是用 Php_Db 扩展的。

几个音符。默认情况下，函数调用将返回根据传入的参数构建的 SQL。如果将`$autorun`静态变量改为`true`，它将自动执行任何调用并返回查询结果。所以你会想把返回存储在一个变量中，或者用一个`if()`块来检查它。如果`$autorun`保持`false`，它返回字符串 SQL，但是它也存储它(直到另一个函数被调用)。如果你想运行它，调用`$this->execute()`。

## 选择

### 返回

```
`stdClass` or `array:stdClass` 
```

Enter fullscreen mode Exit fullscreen mode

### 定义

```
select($strTableName, $fieldList = [], $whereClauses = [], $flags = []) 
```

Enter fullscreen mode Exit fullscreen mode

所以 select 语句显然是大多数人都会做的。正如前一篇文章所示，您需要一个参数，另外 3 个参数是可选的。因此对`$this->select("users");`的调用将从表`users`中提取所有字段和所有记录。例如，如果您只想提取 id 和 name 字段，您可以再次运行`$this->select("users", ['id', 'name']);`，这将返回所有记录的 id 和 name 字段。所以现在，真正的力量！您希望提取一个包含“George”名称的所有用户的特定列表。那看起来像是...

```
$where = new DBWhere('name', '%George%', DBWhere::LIKE);
// You have to set escape equal to false so that it doesn't escape the wildcards
$where->escape = false;

$this->select("users", ['id', 'name'], $where); 
```

Enter fullscreen mode Exit fullscreen mode

`SELECT 'id', 'name' FROM users WHERE name LIKE '%George%'`

同样，这可以通过“flags”参数进一步扩展或细化。

## 选择计数

### 定义

```
selectCount($strTableName, $whereClauses, $flags) 
```

Enter fullscreen mode Exit fullscreen mode

### 返回

```
number 
```

Enter fullscreen mode Exit fullscreen mode

selectCount 查询将只返回满足 WHERE 子句的行数。最大的不同是，这个调用中没有“字段列表”。要返回姓名中包含字符串“George”的所有用户的数量，可以调用...

```
$where = new DBWhere('name', '%George%', DBWhere::LIKE);
$where->escape = false;

$this->selectCount("users", $where); 
```

Enter fullscreen mode Exit fullscreen mode

`SELECT COUNT(1) FROM users WHERE name LIKE '%George%'`

## 插入

### 定义

```
insert($strTableName, $params, $blnIgnore = false) 
```

Enter fullscreen mode Exit fullscreen mode

### 返回

```
number of affected rows 
```

Enter fullscreen mode Exit fullscreen mode

insert 查询将创建一个 insert 语句，允许您向表中插入一行。

```
$this->insert("users", [
    'id' => 1,
    'name' => 'George Jetson',
    'email' => 'george.jetson@spacelysprockets.com'
]); 
```

Enter fullscreen mode Exit fullscreen mode

`INSERT INTO users ('id', 'name', 'email') VALUES ('1', 'George Jetson', 'george.jetson@spacelysprockets.com')`

它还存储了来自前一个查询的插入 id，因此您可以通过访问`$this->_insertId`来检索它。如果您想在查询中添加一个“IGNORE”子句，您还可以指定第三个布尔参数

```
INSERT IGNORE INTO... 
```

Enter fullscreen mode Exit fullscreen mode

## extendedInsert

### 定义

```
extendedInsert($strTableName, $arrFields, $params, $blnToIgnore = false) 
```

Enter fullscreen mode Exit fullscreen mode

### 返回

```
number of affected rows 
```

Enter fullscreen mode Exit fullscreen mode

该查询将创建一个多插入语句，允许您一次插入多行。这个查询有一点不同，因为它需要为第二个参数添加字段列表，并添加一个数组，其中包含要放入每个字段的项目列表。顺序必须一样！

```
$this->extendedInsert("users", ['id', 'name', 'email'], [
    ['1', 'George Jetson', 'george.jetson@spacelysprockets.com'],
    ['2', 'Fred Flintsone', null]
], true); 
```

Enter fullscreen mode Exit fullscreen mode

`INSERT IGNORE INTO users ('id', 'name', 'email') VALUES
('1', 'George Jetson', 'george.jetson@spacelysprockets.com'),
('2', 'Fred Flintstone', NULL)`

**注意:**正如你看到的，如果`null`是一个字段的值，在大多数情况下，它会在 MySQL 中转换为`NULL`。

## 更新

### 定义

```
update($strTableName, $params, $whereClauses = [], $flags = []) 
```

Enter fullscreen mode Exit fullscreen mode

### 返回

```
number of affected rows 
```

Enter fullscreen mode Exit fullscreen mode

该查询将创建更新查询。这非常类似于插入查询，但是第三个参数是 where 子句。您还可以使用标志来影响连接表中的更改。

```
$this->update("users", ['email' => null], new DBWhere('id', 1)); 
```

Enter fullscreen mode Exit fullscreen mode

`UPDATE users SET email = NULL WHERE id = 1`

## 扩展更新

### 定义

```
extendedUpdate($strTableToUpdate, $strOriginalTable, $strLinkField, $fieldsToUpdate) 
```

Enter fullscreen mode Exit fullscreen mode

### 返回

```
number of affected rows 
```

Enter fullscreen mode Exit fullscreen mode

扩展更新查询创建一个使用一个表更新另一个表的更新查询。所以最好的选择是创建一个临时表，在其中插入更新的数据。然后使用这个查询用临时表中的数据更新永久表数据。

第一个参数是要更新的表。第二个参数是更新的源表。第三个参数是两个表之间的公共字段，用作决定要更新哪些记录。第四个参数可以是一个字符串或数组，是一个列表或您想要更新的特定字段。因此，在这种情况下，只有姓名和电子邮件字段将被更新。

```
$this->extendedUpdate('users', 'tmp_users', 'id', ['name', 'email']); 
```

Enter fullscreen mode Exit fullscreen mode

`UPDATE users tbu INNER JOIN tmp_users o USING ('id') SET tbu.name = o.name, tbu.email = o.email`

## 替换

### 定义

```
replace($strTableName, $params) 
```

Enter fullscreen mode Exit fullscreen mode

### 返回

```
number of affected rows 
```

Enter fullscreen mode Exit fullscreen mode

replace 函数的构建与 insert 函数完全一样，只是 replace 函数首先运行一个`delete`语句，然后运行一个`insert`

## 扩展替换

### 定义

```
extendedReplace($strTableName, $fieldList, $param) 
```

Enter fullscreen mode Exit fullscreen mode

### 返回

```
number of affected rows 
```

Enter fullscreen mode Exit fullscreen mode

extendedReplace 函数的构建与 extendedInsert 函数完全一样

## 删除

```
returns number of affected rows 
```

Enter fullscreen mode Exit fullscreen mode

delete 函数创建一个删除查询来从表中删除记录。第二个参数是要删除的字段的数组列表。这样做是为了让您可以从特定的表中移除行，而不是从连接的表中移除行

```
// true and false will automatically be converted to '1' and '0', respectfully
$where = new DBWhere('um.active', false);
// the backticks parameter must be set to false to not surround the field with backticks
// e.g. `um.active`, which is not valid
$where->backticks = false;

$this->delete('users u', ['u.*'], $where, [
    'joins' => [
        "JOIN user_meta um ON um.user_id = u.id"
   ]
]); 
```

Enter fullscreen mode Exit fullscreen mode

`DELETE u.* FROM users u JOIN user_meta um ON um.user_id = u.id WHERE um.active = '0'`

这涵盖了 Php_Db 可以运行的大多数主要查询。它还可以构建一些其他的 DDL 查询，但是我将在下一部分中详细介绍。