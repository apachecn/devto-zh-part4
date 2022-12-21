# PL/SQL 对象类型:将结构放入 Force Powers 表中

> 原文：<https://dev.to/pesse/pl-sql-object-types-putting-the-structure-into-the-force-powers-table-1i12>

本周早些时候，我介绍了一种使用 Oracle 数据库的面向对象功能将视图中的功能块[提取为对象类型](https://dev.to/pesse/structure-force-powers-with-pl-sql-object-types-4lcm)的方法。

今天我想更进一步，让对象类型不是视图逻辑的一部分，而是底层表的一部分。目标是去掉仍然相当复杂的视图，用一个`t_numeric_structure`对象替换`VARCHAR2` `STRUCTURE`列。

快速提醒一下我们的表格:基于排列(万能、光明、黑暗)和所需熟练程度(使用“力推”的先决条件是“心灵遥控”)的结构异能

| 身份证明 | 结构 | 名字 |
| --- | --- | --- |
| one | one | 普遍的 |
| Two | One point one | 心灵遥感 |
| three | 1.1.1 | 强制推送 |
| ... | ... | ... |

添加新的对象类型列非常简单:

```
-- First add a new column to the table
alter table force_powers
  add struct t_numeric_structure;

-- Then fill the new column
update force_powers
  set struct = t_numeric_structure(structure);

-- We can now easily select from the type
select
  p.id,
  p.name,
  p.struct.structure() structure
  from force_powers p;

-- By the way: It doesnt work to select
-- without a table alias
select
  p.id,
  p.name,
  struct.structure() structure
  from force_powers p; 
```

到目前为止很容易，但是我们希望确保`STRUCT`列是唯一的，这样我们就可以安全地删除旧的`STRUCTURE`。

```
-- It doesnt work to add a unique constraint on the type
alter table force_powers
  add constraint force_powers_uq_struct_new 
    unique ( struct );

-- Neither can it be done via a function
alter table force_powers
  add constraint force_powers_uq_struct_new 
    unique ( struct.structure() );

-- And also not via a unique function-based index
-- because the function is not deterministic
create unique index idx_force_powers_struct on
  force_powers ( struct.structure() );

-- But its totally possible to have a unique constraint
-- on the PROPERTIES of the type
alter table force_powers
  add constraint force_powers_uq_struct_new unique
    ( struct.c_level1, struct.c_level2, struct.c_level3 );

-- Now lets get rid of the old structure column
alter table force_powers
  drop column structure;

-- And Assure the unique index works
insert into force_powers ( name, struct )
select 'some name', t_numeric_structure('4.0.0')
from dual connect by level <= 10;

-- Also via update
update force_powers p
  set struct = t_numeric_structure('4.0.0')
  where p.struct.structure() like '3%';

-- We still can insert a new row
insert into force_powers ( name, struct )
  values ( 'Force healing', t_numeric_structure('2.2'));

-- And update
update force_powers p
  set struct = t_numeric_structure('3.1.3')
  where p.struct.structure() = '3.1.2'; 
```

我们现在可以将`SELECT`简化为以下内容

```
select
  base.id,
  base.struct.structure() structure,
  base.name,
  base.struct.level1() level1,
  base.struct.level2() level3,
  base.struct.level3() level3,
  base.struct.depth() depth,
  base.struct.sort() sort,
  (select id
     from force_powers parent
     where parent.struct.structure() =
           base.struct.parent().structure()
  ) parent_id
  from
    force_powers base; 
```

| 身份证明 | 结构 | 名字 | 级别 1 | 级别 3 | 级别 3 | 深度 | 分类 | 家长 ID |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| one | one | 普遍的 | one | Zero | Zero | one | 001000000 | 空 |
| Two | One point one | 心灵遥感 | one | one | Zero | Two | 001001000 | one |
| three | 1.1.1 | 强制推送 | one | one | one | three | 001001001 | Two |
| four | 1.1.2 | 强制拉动 | one | one | Two | three | 001001002 | Two |
| five | 1.1.3 | 强制跳转 | one | one | three | three | 001001003 | Two |
| six | Two | 光 | Two | Zero | Zero | one | 002000000 | 空 |
| seven | Two point one | 心灵诡计 | Two | one | Zero | Two | 002001000 | six |
| eight | 2.1.1 | 强行劝说 | Two | one | one | three | 002001001 | seven |
| nine | three | 黑暗 | three | Zero | Zero | one | 003000000 | 空 |
| Ten | Three point one | 强制伤口 | three | one | Zero | Two | 003001000 | nine |
| Eleven | 3.1.1 | 用力抓握 | three | one | one | three | 003001001 | Ten |
| Twelve | 3.1.3 | 强制扼流 | three | one | three | three | 003001003 | Ten |
| Fifteen | Two point two | 强制治疗 | Two | Two | Zero | Two | 002002000 | six |

## 但是这些基于函数的 indizes 呢？

函数必须是确定性的，才能在基于函数的索引中使用。

确定性意味着函数将总是基于输入参数返回相同的值。但是确定性对于存储在数据库表中的对象类型的成员函数意味着什么呢？

我有一个理论，并希望与 AskTOM 的优秀人员讨论——不幸的是，新问题目前已经关闭，因此他们可以处理积压的问题。

*(我要强调，我真的很欣赏那种做法！设定界限是很重要的，对我来说，暂时不允许新的问题是他们致力于质量的表现。谢谢大家！)*

我的猜测是，属性被当作成员函数的输入参数。毕竟，`SELF`是每个成员函数的隐式输入参数，所以如果一个对象类型的函数只依赖于对象的属性，它可以被看作是确定性的。

我在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/irck35ywp4pc55h6jg8e4xrdk) 上准备了一个简化的例子，一旦他们再次对新问题开放，我会用 AskTOM 问题更新这个帖子。

### 让我们使用 structure()作为基于函数的索引

```
-- So ... back to that function-based index problem
create unique index idx_force_powers_uq_struct on
  force_powers ( struct.structure() );

-- Function is not deterministic - so lets change that.

create or replace type t_numeric_structure force as object
(
  c_level1 number(3,0),
  c_level2 number(3,0),
  c_level3 number(3,0),

  constructor function t_numeric_structure(
    i_struct varchar2 )
    return self as result,
  member function p$_position_for_level(
    i_string varchar2,
    i_level positiven ) return pls_integer,
  member function level1 return pls_integer,
  member function level2 return pls_integer,
  member function level3 return pls_integer,
  member function structure return varchar2
    deterministic,
  member function sort return varchar2,
  member function depth return pls_integer,
  member function parent return t_numeric_structure2
);
/

-- We cant do this because our type is used in a table
-- Therefore we have to remove it from the table,
-- change it and then re-add it:

-- First store the value in a new column
alter table force_powers
  add strucutre_backup varchar2(12);

update force_powers p
  set strucutre_backup = p.struct.structure();

-- Now remove the type-column and change the type
alter table force_powers
  drop column struct; 
```

我在这里跳过了类型的重新创建，它实际上只是将`deterministic`添加到成员函数中。

```
-- Now Re-add the type
alter table force_powers
  add struct t_numeric_structure;

update force_powers p
  set struct = t_numeric_structure(strucutre_backup);

-- Add the unique index
-- We are using substr here to tell the index that the
-- resulting string will be a varchar2(12)
-- Otherwise it will assume varchar2(4000) for we can not
-- hint the length of a varchar2 returned by a function
create unique index idx_force_powers_uq_struct on
  force_powers ( substr(struct.structure(),1,12) ); 
```

现在让我们再次测试唯一索引是否有效:

```
insert into force_powers ( name, struct )
select 'some name', t_numeric_structure('4.0.0')
from dual connect by level <= 10;
-- Fails with Unique Index violated

update force_powers p
  set struct = t_numeric_structure('4.0.0')
  where p.struct.structure() like '3%';
-- Fails with Unique Index violated

-- The index is also used when querying
set autotrace on
select
  p.id,
  p.name,
  p.struct.structure() structure
  from force_powers p
  where p.struct.structure() = '1.1.1'; 
```

整个例子可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/ircf33yw485zr1uzniu7ofzl9) 上运行，也可以在 [GitHub](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/objects/19_numeric_structure_as_column.sql) 上获得。

我可能应该编写这两个代码示例，但是如果不能对其函数或属性进行索引，那么在表中包含一个对象有什么好处呢？

这可能有点不常见，但我确实看到了当对象类型被仔细使用时，对数据库代码可读性的巨大好处。

哦——我真的很喜欢在这上面度过一些夜晚！