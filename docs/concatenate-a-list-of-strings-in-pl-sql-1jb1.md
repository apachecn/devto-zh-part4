# 在 PL/SQL 中连接字符串列表

> 原文：<https://dev.to/pesse/concatenate-a-list-of-strings-in-pl-sql-1jb1>

我今天遇到了一个需求，我必须将一些字符串(varchars)组合成一个分隔列表。一些字符串可能为空，应该被忽略。

我试了一下，发现了一些不同的方法，我想和大家分享一下。

让我们假设我们有一个带有函数`person`的包`info_util`，它将一个人的大量信息组合成一个定界列表(为了简化这个例子，所有信息都通过参数传递):

```
dbms_output.put_line(info_util.person(
  i_name => 'Luke Skywalker',
  i_alignment => 'light',
  i_comment => 'Most powerful jedi of all times'));
-- Output: Luke Skywalker, light, Most powerful jedi of all times

dbms_output.put_line(info_util.person(
  i_name => 'Vader',
  i_title => 'Darth',
  i_alignment => 'dark',
  i_comment => 'Pretty evil'));
-- Output: Vader, Darth, dark, Pretty evil 
```

## 基本字符串串联

最简单的解决方案是用一些 if 逻辑检查一切，并连接字符串

```
function person_concatenate(
  i_name varchar2,
  i_title varchar2,
  i_alignment varchar2,
  i_comment varchar2
) return varchar2 deterministic
as
  l_result varchar2(32000);
  begin
    if i_name is not null then
      l_result := l_result || i_name;
    end if;

    if i_title is not null then
      if l_result is not null then
        l_result := l_result || ', ';
      end if;
      l_result := l_result || i_title;
    end if;

    if i_alignment is not null then
      if l_result is not null then
        l_result := l_result || ', ';
      end if;
      l_result := l_result || i_alignment;
    end if;

    if i_comment is not null then
      if l_result is not null then
        l_result := l_result || ', ';
      end if;
      l_result := l_result || i_comment;
    end if;

    return substr(l_result, 1, 4000);
  end; 
```

但是这很难阅读、理解和维护。如果你不相信我，你需要多长时间来确保对`i_alignment`和`i_comment`的处理没有区别？

其人也单调乏味。

## 使用 LISTAGG

`LISTAGG`功能或多或少地满足了我们的需求，那么我们为什么不使用它呢？

```
function person_listagg(
  i_name varchar2,
  i_title varchar2,
  i_alignment varchar2,
  i_comment varchar2
) return varchar2 deterministic
as
  /* We have to collect all parts
     in a nested table collection first
   */
  l_parts sys.odcivarchar2list := new sys.odcivarchar2list();
  l_result varchar2(4000);

  /* This function makes our code more
     readable: just extend the collection and add an item
   */
  procedure add_part( i_string varchar2 )
  as
    begin
      l_parts.extend;
      l_parts(l_parts.last) := i_string;
    end;
  begin
    /* Now we can simple add the parts */
    add_part(i_name);
    add_part(i_title);
    add_part(i_alignment);
    add_part(i_comment);

    /* And can use LISTAGG to aggregate */
    select listagg(column_value, ', ') within group (order by rownum)
      into l_result
      from table(l_parts);

    return l_result;
  end; 
```

这里的主要问题是，由于 PL/SQL -> SQL 上下文切换，它可能会非常慢。

## 串联在一个 PL/SQL 循环中

因此，也许我们可以在 PL/SQL 循环中处理串联，而不是使用 LISTAGG。

```
function person_plsql_comb(
  i_name varchar2,
  i_title varchar2,
  i_alignment varchar2,
  i_comment varchar2
) return varchar2 deterministic
as
  /* So lets take the thing that improves readability */
  l_parts sys.odcivarchar2list := new sys.odcivarchar2list();
  l_result varchar2(4000);
  procedure add_part( i_string varchar2 )
  as
    begin
      l_parts.extend;
      l_parts(l_parts.last) := i_string;
    end;
  begin
    add_part(i_name);
    add_part(i_title);
    add_part(i_alignment);
    add_part(i_comment);

    /* But handle the concatenation in PL/SQL */
    for i in l_parts.first..l_parts.last loop
      if l_parts(i) is not null then
        if l_result is not null then
          l_result := l_result || ', ';
        end if;
        l_result := l_result || l_parts(i);
      end if;
    end loop;

    return l_result;
  end; 
```

对我来说看起来不错。但是性能呢？

下面的代码运行测试函数 20，000 次(每个用例 10，000 次)。为了消除关键字`DETERMINISTIC`的任何优势，我给每个名字添加了 run，所以不涉及缓存。

```
declare
  l_ts timestamp := current_timestamp;
  l_info varchar2(4000);
begin
    for i in 1..10000 loop
        l_info := info_util.person_concatenate('Luke Skywalker'||to_char(i), null, 'light', 'Most powerful jedi of all times');
        l_info := info_util.person_concatenate('Vader'||to_char(i), 'Darth', 'dark', 'Pretty evil');
    end loop;
    dbms_output.put_line('Basic concatenation: ' || to_char(current_timestamp-l_ts));
end;
/ 
```

|基本串联| 0.02 秒|
| LISTAGG | 0.8 秒|
| PL/SQL 循环| 0.1 秒|

所以我们看到 LISTAGG 的上下文切换确实降低了我们的功能。循环和集合初始化的东西也带来了很大的性能代价，但是到目前为止还没那么糟糕。

如果我们利用`DETERMINISTIC`的优势，基本上只是反复调用两个不同的语句，会怎么样？

|基本串联| 0.0008 秒|
| PL/SQL 循环| 0.0008 秒|

几乎没有任何区别，这意味着如果您的数据是同质的，循环收集方法的额外性能成本不会很明显(性能总是取决于您的数据)。

## PL/SQL 对象

我们现在可以做的是将整个聚集部分提取到一个 PL/SQL 对象中(是的，当然我们可以提高可读性，但仍然不使用对象，但它们非常方便，尤其是在这些情况下):

```
create or replace type t_string_aggregator force is object
(
  /* The nested table is now part of our object */
  c_parts sys.odcivarchar2list,

  constructor function t_string_aggregator return self as result,
  member procedure add_string( i_string varchar2 ),
  member function get_aggregate( i_delimiter varchar2 default ', ' )
    return varchar2
);
/

create or replace type body t_string_aggregator as
  constructor function t_string_aggregator return self as result
  as
    begin
      /* Lets not forget to initialize the collection */
      c_parts := new sys.odcivarchar2list();
      return;
    end;

  /* This is basically the same as the internal procedure
     we used in the other approaches
   */
  member procedure add_string( i_string varchar2 )
  as
    begin
      c_parts.extend;
      c_parts(c_parts.last) := i_string;
    end;

  /* We can even make the delimiter dynamic */
  member function get_aggregate( i_delimiter varchar2 )
    return varchar2
  as
    l_result varchar2(4000);
    begin
      /* Little tweak if we dont have any items */
      if c_parts.count < 0 then
        return null;
      end if;

      for i in c_parts.first..c_parts.last loop
          if c_parts(i) is not null then
            if l_result is not null then
                  l_result := l_result || i_delimiter;
              end if;
            l_result := l_result || c_parts(i);
          end if;
      end loop;

      return l_result;
    end;
end;
/ 
```

最终的函数应该是这样的:

```
function person_plsql_obj(
  i_name varchar2,
  i_title varchar2,
  i_alignment varchar2,
  i_comment varchar2
) return varchar2 deterministic
as
  l_aggregator t_string_aggregator := new t_string_aggregator();
  begin
    l_aggregator.add_string(i_name);
    l_aggregator.add_string(i_title);
    l_aggregator.add_string(i_alignment);
    l_aggregator.add_string(i_comment);

    return l_aggregator.get_aggregate();
  end; 
```

一旦我们知道了`t_string_aggregator`有什么用途，它就会变得非常小而且易读，对吗？

就性能而言，它比 PL/SQL 循环方法慢 10%左右——这在使用`DETERMINISTIC`优势时也不明显——取决于您的数据。

_ **警告:**一旦字符串部分的总和超过 4000 个字符，这个解决方案就会出现严重的问题。但那是另一个话题，另一个例子。_

您可以在 [LiveSQL](https://livesql.oracle.com/apex/livesql/s/iv1mwj39gviei711gc0gue6ng) 上运行完整的示例，并从我的 [GitHub 库](https://github.com/pesse/sith-demo-db/blob/master/100CodeExamples/objects/20_string_aggregation.sql)中获取源代码。

## 思想

那么，为什么我如此喜欢通过 PL/SQL 对象的最后一种方法呢？

在我看来，它很好地模块化和封装了我的功能。这带来了以下优点:

*   我在其他地方用的时候更容易理解
*   它有明确界定的范围
*   更容易扩展(例如溢出机制)
*   它使干燥成为可能(不要重复)
*   这很容易测试。

期待听到你们的想法。