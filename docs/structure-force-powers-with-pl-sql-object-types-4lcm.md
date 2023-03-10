# PL/SQL 对象类型的结构强制能力

> 原文：<https://dev.to/pesse/structure-force-powers-with-pl-sql-object-types-4lcm>

在过去，我有两个关于 PL/SQL 对象类型的 [例子](https://dev.to/pesse/100codeexamples--use-the-force-to-replace-objects-5b0o)，但是还没有展示它们的实际用途:对于现代 SQL 来说，turing complete 你可以用某种方式用 SQL 解决每一个问题，用过程化 PL/SQL 的功能甚至更容易。

但是，在某些情况下，对象类型(SQL 类型)的使用会对您有很大的帮助，因为它们在 Oracle 数据库 8i (1999)版中就可用，所以它们应该是每个数据库开发人员的工具包的一部分。

我们刚刚得到了以下结构化的原力列表，按照它们的主要性质(普遍、光明和黑暗)和技能依赖性分类:

| 身份证明 | 结构 | 名字 |
| --- | --- | --- |
| one | one | 普遍的 |
| Two | One point one | 心灵遥感 |
| three | 1.1.1 | 强制推送 |
| four | 1.1.2 | 强制拉动 |
| five | 1.1.3 | 强制跳转 |
| six | Two | 光 |
| seven | Two point one | 心灵诡计 |
| eight | 2.1.1 | 强行劝说 |
| nine | three | 黑暗 |
| Ten | Three point one | 强制伤口 |
| Eleven | 3.1.1 | 用力抓握 |
| Twelve | 3.1.2 | 强制扼流 |

为了能够掌握原力拉、推或跳，你首先必须掌握属于宇宙异能的心灵遥控的基础知识，为了使用原力说服某人，你首先必须学习绝地心灵技巧的基础知识(“这些不是你要找的机器人”)。

我们现在想根据结构列添加几个附加信息:

*   整数级别 1-3
*   每种力量的深度
*   001001001 类型的排序字段
*   父元素的 ID

我们可以通过纯 SQL 实现这一点:

```
with leveled_powers as (
  select
      id,
      structure,
      name,
      -- Get the value of each level from structure or 0
      nvl(to_number(
        regexp_substr(structure, '[0-9]+', 1, 1)
        ), 0) level1,
      nvl(to_number(
        regexp_substr(structure, '[0-9]+', 1, 2)
        ), 0) level2,
      nvl(to_number(
        regexp_substr(structure, '[0-9]+', 1, 3)
        ), 0) level3
      from
        force_powers
)
select
  id,
  structure,
  name,
  level1,
  level2,
  level3,
  -- Use the level to determine depth of current element
  case
    when level3 > 0 then
      3
    when level2 > 0 then
      2
    when level1 > 0 then
      1
    else
      0
  end depth,
  -- Concatenate the levels to a sortable string
  lpad(level1, 3, '0')
    || lpad(level2, 3, '0')
    || lpad(level3, 3, '0') sort,
  -- Get the id of the parent by structure
  (select id
    from force_powers p
    where
      p.structure =
      case
        when base.level3 > 0 then
          to_char(base.level1)||'.'||to_char(base.level2)
        when base.level2 > 0 then
          to_char(base.level1)
      end
  ) parent_id
  from
    leveled_powers base; 
```

这将产生下表:

| 身份证明 | 结构 | 名字 | 级别 1 | 级别 2 | 级别 3 | 深度 | 分类 | 家长 ID |
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
| Twelve | 3.1.2 | 强制扼流 | three | one | Two | three | 003001002 | Ten |

虽然它完全以这种方式工作，但我发现 SQL 很难阅读，维护起来可能很痛苦:您需要解析和理解 SQL 代码的每一行，以获得关于它将产生什么的线索。

因此，让我们尝试将所有与结构相关的逻辑提取到一个 PL/SQL 对象类型中，首先定义一个包含所有必要函数的类型头:

```
create or replace type t_numeric_structure force as object
(
  -- for the levels are our most basic data
  -- lets store them in the object
  c_level1 number(3,0),
  c_level2 number(3,0),
  c_level3 number(3,0),

  -- constructor to create structure from string
  constructor function t_numeric_structure(
    i_struct varchar2 )
    return self as result,
  -- This is just a little helper function
  member function p$_position_for_level(
    i_string varchar2,
    i_level positiven ) return pls_integer,
  -- Functions to get the different levels
  member function level1 return pls_integer,
  member function level2 return pls_integer,
  member function level3 return pls_integer,
  -- Function to get the structure string
  member function structure return varchar2,
  -- Function to get the sort value
  member function sort return varchar2,
  -- Function to get the depth
  member function depth return pls_integer,
  -- Function to get an instance of the parent structure
  member function parent return t_numeric_structure
); 
```

然后，我们使用与视图中完全相同的逻辑实现该函数:

```
create or replace type body t_numeric_structure as

  constructor function t_numeric_structure(
    i_struct varchar2 )
    return self as result
  as
    begin
      -- We do exactly the same here as in the
      -- leveled_powers-with - just a bit more readable
      self.c_level1 := p$_position_for_level(i_struct, 1);
      self.c_level2 := p$_position_for_level(i_struct, 2);
      self.c_level3 := p$_position_for_level(i_struct, 3);
      return;
    end;

  member function p$_position_for_level(
    i_string in varchar2,
    i_level in positiven ) return pls_integer
  as
    begin
      return
        nvl(to_number(
          regexp_substr(i_string, '[0-9]+', 1, i_level)
        ),0);
    end;

  member function level1 return pls_integer
  as
    begin
      return c_level1;
    end;

  member function level2 return pls_integer
  as
    begin
      return c_level2;
    end;

  member function level3 return pls_integer
  as
    begin
      return c_level3;
    end;

  member function structure return varchar2
  as
    l_result varchar2(50);
    begin
      -- Because we use the atomic levels we have
      -- to (re-)create the structure
      if ( c_level1 > 0 ) then
        l_result := to_char(c_level1);
      end if;
      if ( c_level2 > 0 ) then
        l_result := l_result || '.' || to_char(c_level2);
      end if;
      if ( c_level3 > 0 ) then
        l_result := l_result || '.' || to_char(c_level3);
      end if;
      return l_result;
    end;

  member function sort return varchar2
  as
    begin
      -- Same logic as in the view
      return lpad(c_level1, 3, '0')
        || lpad(c_level2, 3, '0')
        || lpad(c_level3, 3, '0');
    end;

  member function depth return pls_integer
  as
    begin
      -- The same logic as in the view
      -- with different flavour
      if c_level3 > 0 then
        return 3;
      elsif c_level2 > 0 then
        return 2;
      elsif c_level1 > 0 then
        return 1;
      else
        return 0;
      end if;
    end;

  member function parent return t_numeric_structure
  as
    begin
      -- We basically do the same here as in the subselect
      -- for parent_id, but return a new instance of
      -- the numeric_strucutre-type or NULL
      if ( c_level3 > 0 ) then
        return new t_numeric_structure(
          to_char(c_level1)||'.'||to_char(c_level2));
      elsif ( c_level2 > 0 ) then
        return new t_numeric_structure(
          to_char(c_level1));
      else
        return null;
      end if;
    end;
end; 
```

有了这个对象类型，我们现在可以像这样重写 SELECT 语句:

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
     from force_powers p
     where p.structure =
           base.struct.parent().structure()
  ) parent_id
  from
    (
      select
        id,
        name,
        t_numeric_structure(structure) struct
        from
          force_powers
    ) base; 
```

### 但是为什么我要增加 130 行对象类型代码来减少我的 SQL 代码从 54 行到 23 行呢？

我在这里看到的好处如下:

*   当阅读新的 SQL 语句时，我可以更快地理解代码做了什么，而不必理解实现细节:因此，我需要更少的精神负担来理解意图
*   当处理 numeric_structure 的不同函数时，我可以一次专注于一个功能。这再次减少了所需的精神负荷
*   我成功地分离了不同的关注点，并使处理结构化信息的逻辑成为一个模块，而不是我的 SELECT 语句的一部分。这使得维护代码变得更容易，并且再次减少了我需要的精神负担，因为我可以只看一个模块
*   分离的模块更容易测试(我将在一个单独的例子中展示这一点)

您可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/iqwqhw19803djac04in0w4uxs) 上运行整个示例，并在我的 [github repo](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/objects/18_numeric_structure.sql) 上获得它。

接下来，我们将使 T_NUMERIC_STRUCTURE 类型成为 FORCE_POWERS-table 的一部分，敬请关注。