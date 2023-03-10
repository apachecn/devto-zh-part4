# 如何在 Crystal 中解析带双引号(")字符的 CSV

> 原文：<https://dev.to/jetrockets/how-to-parse-csv-with-double-quote-character-in-crystal-2a6c>

我们用 Crystal 写的微服务解析大型 CSV 文件(约 1.5Gb)。这些文件中的某些行可能不包含闭合的“字符:

```
,Y,FEDERAL NATIONAL MORTGAGE ASSOCIATION "F,, 
```

使用 Crystal 默认 CSV 解析设置，这一行及其后的所有内容都不会被正确解析，因为`DEFAULT_QUOTE_CHAR`常量等于`"`。当然，您可以用文档中找不到的东西覆盖`CSV`构造函数中的`quote_char`参数。

从我的角度来看，最好是使用零字节，这是在水晶`'\u0000'`。

```
csv = CSV.new(file, headers: true, strip: true, quote_char: '\u0000')
while csv.next
  # ... 
end 
```

黑！