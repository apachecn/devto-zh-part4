# 添加到 D 语言的词典中

> 原文：<https://dev.to/jessekphillips/add-to-a-dictionary-in-d-ga4>

字典，哈希表，关联数组，映射它有很多名字。字典添加的主要挑战在于嵌套关联数组，如果我每次选择不同的名字，你会不高兴吗？

```
int[string] dict;
dict["Key"] = 55; 
```

Enter fullscreen mode Exit fullscreen mode

D.
中有一个字典字面

```
auto data = ["key" : 55] 
```

Enter fullscreen mode Exit fullscreen mode

但是这对于添加数据并不好，因为它会用一个新的对象替换变量。然而，如果你是嵌套的并且内部字典可能是空的，那么它可以很好地用于开始一个字典。

```
string[int][string] data;
data["hello"] = [95: "value"]; 
```

Enter fullscreen mode Exit fullscreen mode