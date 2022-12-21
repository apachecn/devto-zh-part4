# C# 8.0 中的零合并运算符

> 原文：<https://dev.to/mpetrinidev/the-null-coalescing-operator-in-c-8-0-4ib4>

* * *

欢迎光临！LinkedIn[上的西班牙文文章](https://www.linkedin.com/in/mpetrinidev/)。你可以在[推特](https://twitter.com/mpetrinidev/)上关注我的新闻。

* * *

## 什么是零合并？

根据 ms docs，空合并运算符`??`如果不为空，则返回其左边的值；否则，它计算右边的操作数并返回结果。那个？？如果左边的操作数计算结果为非空，则运算符不计算右边的操作数。

通过一个 Youtube 视频，我将展示如何在 C# 8.0 中使用零合并运算符，以及它的发展:

*   老办法
*   使用 C# 7
*   最后是 C# 8.0

### 举例

我们有一个名为 age 的空值变量

```
 static int? GetNullAge() => null;
    static int? GetAge() => 27;

    int? age = GetNullAge(); 
```

### 古老的方式

我们需要检查值是否为空，然后赋值

```
 if (age == null)
    {
        age = GetAge();
    } 
```

### C# 7

我们用`null coalescing operator`

```
 age = age ?? GetAge(); 
```

### C# 8.0

我们用`compound assignment operator`

```
 age ??= GetAge(); 
```

有关更多信息:

### Youtube 视频

[https://www.youtube.com/embed/unRcg7kTpDQ](https://www.youtube.com/embed/unRcg7kTpDQ)

#### Github 要诀