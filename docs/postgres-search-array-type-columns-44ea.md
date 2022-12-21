# 波斯特格雷。搜索数组类型列

> 原文：<https://dev.to/vitalyplatonov/postgres-search-array-type-columns-44ea>

假设我们有一个包含一个类型为的[数组的列的表。在某些时候，我们希望能够选择数组列可能具有的特定值的记录。](https://www.postgresql.org/docs/11/arrays.html)

这里有三种不同的搜索方式。

1)用一个值搜索时使用任意运算符:

```
SELECT * FROM mytable WHERE 'first_type' = ANY(types_column); 
```

2)当您寻找一组特定的值时(值的顺序无关紧要)，请使用“包含”运算符(“@>”):

```
SELECT * FROM mytable WHERE types_column @> '{"first_type", "second_type"}'; 
```

值`“first_type”`和`“second_type"`必须在`types_column`列中，以便选择记录。

3)每当你需要搜索一个列可能有的任何值时——使用[“重叠”操作符](https://www.postgresql.org/docs/current/functions-array.html)(&&)

```
SELECT * FROM mytable WHERE types_column && '{"first_type", "second_type"}'; 
```

对于要选择的记录，值`“first_type”`或`“second_type"`之一必须在`types_column`列中。