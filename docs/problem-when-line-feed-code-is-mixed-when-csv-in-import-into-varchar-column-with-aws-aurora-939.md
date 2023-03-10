# 使用 AWS Aurora 将 csv in 导入 varchar 列时出现换行代码混合的问题。

> 原文：<https://dev.to/yoshidaagri/problem-when-line-feed-code-is-mixed-when-csv-in-import-into-varchar-column-with-aws-aurora-939>

## 【环境】

*   AWS Aurora 或 Mysql

# 【问题】

*   当 csv 文件被导入到字符串列时，如果标准被用单引号括起来，将不会产生所需的结果。

```
where target_column = '31000000000'; 
```

*   如果标准没有用单引号括起来，那么 ok。

```
where target_column = 31000000000; 
```

## 【原因】

*   如果在加载程序中导入不带双引号的 csv 文件，会导入多达个不必要的换行代码。
*   换行代码在数据库中通常是不可见的。有必要看十六进制。

| 价值 | 备忘录 | 十六进制(值) |
| --- | --- | --- |
| Thirty-one billion | 战斗支援车 | 3331303030303030303030300d |
| Thirty-one billion | DoctorofModernLanguages 现代语言博士 | 3331303030303030303030 |

“0D”是 UTF-8 的换行代码。

## mysql 的  【点】法则。

*   对于条件表达式[string = number]，将左侧字符串作为十进制数进行比较换行符无效。

*   对于条件表达式[string = string]，左侧的字符串按原样进行比较。换行符有效。

## 【如何】

*   要查看换行代码，将其转换为十六进制。十六进制的话就是“0d”。
*   此 DML 仅删除换行符代码。

```
update target_table_name set target_column_name = TRIM( UNHEX('0D') FROM target_column_name ); 
```