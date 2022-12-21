# 添加到 D 中的列表

> 原文：<https://dev.to/jessekphillips/add-to-a-list-in-d-42hl>

我认为这很容易，考虑到我甚至不能提供一个布尔值，这应该是一个惊喜。d 是一个可以理解的不同故事。d 提供数组和关联数组。这些通常就足够了，没有必要使用容器库。

数组利用[]来表示类型并创建文字。

```
string[] arrayVarName;
arrayVarName = ["array literal value"]; 
```

Enter fullscreen mode Exit fullscreen mode

与其他语言不同，D 不使用加法来表示连接。

```
arrayVarName ~= "additional value"; 
```

Enter fullscreen mode Exit fullscreen mode

这通常被称为追加操作符，因为数组在不会导致分配的地方保留了空间。

这也适用于数组。

```
arrayVarName ~= ["more", "values"]; 
```

Enter fullscreen mode Exit fullscreen mode

两个列表可以通过连接进行组合。

```
arrayVarName = ["value"] ~ ["more", "values"]; 
```

Enter fullscreen mode Exit fullscreen mode

由于 D 有操作符重载，用户类型可以在类或结构上有适当定义的行为。同样，这使用了强大的模板，超出了本教程的范围。

[https://stack overflow . com/questions/7826891/elegant-operator-overloading-in-d](https://stackoverflow.com/questions/7826891/elegant-operator-overloading-in-d)