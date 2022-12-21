# PHP MySQL 库 Pt3

> 原文：<https://dev.to/godsgood33/php-mysql-library-pt3-4bj1>

在本系列的第 1 部分中，我谈到了我创建的 Php_Db 库。在第 2 部分中，我展示了大量可以使用它创建的函数和查询。在这一部分，我将讨论可以用 Php_Db 创建的 DDL 查询

## 下降

```
returns mysqli_result 
```

Enter fullscreen mode Exit fullscreen mode

drop 函数将创建一个 drop 语句，允许您从模式中删除一个表或视图。默认情况下，它将下拉一个表

```
$this->drop('users') 
```

Enter fullscreen mode Exit fullscreen mode

**删除表格用户**

```
$this->drop('user_view', 'view') 
```

Enter fullscreen mode Exit fullscreen mode

**DROP VIEW user_view**

还有第三个参数，允许您指定该表是一个临时表。

```
$this->drop('tmp_user', 'table', true) 
```

Enter fullscreen mode Exit fullscreen mode

**删除临时表 tmp_user**

## 截断

```
returns mysqli_result 
```

Enter fullscreen mode Exit fullscreen mode

truncate 函数允许您创建 truncate table 语句来删除表中的所有记录

```
$this->truncate('users') 
```

Enter fullscreen mode Exit fullscreen mode

**截断表格用户**

## 创建表格

```
returns mysqli_result 
```

Enter fullscreen mode Exit fullscreen mode

createTable 函数允许您使用数组或 select 语句创建一个表或临时表(这也可以使用现有的已保存的 select 语句)。第二个参数是这个表是否是临时表。第三个参数定义了将在该表中创建哪些字段。

```
$this->createTable('new_users', false, [
    [
        'field' => 'id',
        'datatype' => 'int(11)',
        'option' => 'AUTO_INCREMENT PRIMARY KEY'
    ],
    [
        'field' => 'name',
        'datatype' => 'varchar(255)',
        'default' => null
    ],
    [
        'field' => 'email',
        'datatype' => 'varchar(64)',
        'default' => null
    ]
]); 
```

Enter fullscreen mode Exit fullscreen mode

**不存在则创建表 new _ users(
id int(11)AUTO _ INCREMENT 主键，
name varchar(255)，
email varchar(255)
)；**

正如我上面所说的，您也可以使用选择查询。

```
$this->createTable('new_users', false, "SELECT * FROM users"); 
```

Enter fullscreen mode Exit fullscreen mode

**如果不存在，则创建表格 new _ users AS(SELECT * FROM users)；**

## createTableJson

createTableJson 允许您传递一个预先格式化的 Json 字符串，它将创建一个表字符串。/vendor/godsgood 33/PHP-db/examples/中有一个 json 文件格式的例子

## 可变

```
returns mysqli_result 
```

Enter fullscreen mode Exit fullscreen mode

alterTable 函数允许您创建一个语句，通过添加、删除或修改列来改变数据库表。alterTable 通过 3 个参数来实现这一点:要编辑的表、要采取的操作(ADD_COLUMN、DROP_COLUMN、MODIFY_COLUMN 和 ADD_CONSTRAINT)以及参数。这些操作是数据库类中的常量。

### 添加 _ 列:

```
$phoneColumn = new stdClass();
$phoneColumn->name = 'phone';
$phoneColumn->dataType = 'varchar(20)';
$phoneColumn->default = null;

$this->alterTable('users', \Godsgood33\Php_Db\Database::ADD_COLUMN, [
    $phoneColumn
]); 
```

Enter fullscreen mode Exit fullscreen mode

**ALTER TABLE 用户添加列 phone varchar(20)默认 NULL**

### 降 _ 列:

```
$phoneColumn = new stdClass();
$phoneColumn->name = 'phone';

$this->alterTable('users', \Godsgood33\Php_Db\Database::DROP_COLUMN, [
    $phoneColumn
]); 
```

Enter fullscreen mode Exit fullscreen mode

**更改表用户降列电话**

### 修改 _ 列:

```
$emailColumn = new stdClass();
$emailColumn->name = 'email';
$emailColumn->new_name = 'user_email';
$emailColumn->dataType = 'varchar(255)';
$emailColumn->default = null;

$this->alterTable('users', \Godsgood33\Php_Db\Database::MODIFY_COLUMN, [
    $emailColumn
]); 
```

Enter fullscreen mode Exit fullscreen mode

**ALTER TABLE users 修改列 email user _ email varchar(255)DEFAULT NULL**

### 添加 _ 约束:

## [一百](#setvar)

```
NULL return 
```

Enter fullscreen mode Exit fullscreen mode

允许您设置 SQL 变量

```
$this->setVar('foo', 'bar'); 
```

Enter fullscreen mode Exit fullscreen mode

## 表存在

```
returns boolean 
```

Enter fullscreen mode Exit fullscreen mode

检查表格是否存在

```
$this->tableExists('schema', 'table'); 
```

Enter fullscreen mode Exit fullscreen mode

## 
  
【外地存在者】