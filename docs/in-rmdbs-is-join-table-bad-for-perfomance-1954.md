# 在 RMDBS 中，连接表对性能有影响吗？

> 原文：<https://dev.to/hongduc/in-rmdbs-is-join-table-bad-for-perfomance-1954>

在我工作的公司，我们使用 Postgres，有一次当我们创建一个简单的聊天应用程序时，我建议我们将`messages table`和`user table`连接起来，这样我们就可以得到`message author name`。但是我的老板说它不好，他说用大表连接会降低速度，越多的数据需要连接，速度越慢。

我只学习关于 RMDBS 的基本知识，如连接、内部连接等...。所以我真的不明白，为什么我的老板会这么说。我从学校学到的是不同的，当你设计 RMBDS 模型时，你必须使用 join，这是普通的东西。

模型是这样的:

```
Message {
  author_name,
  body
}

User {
  user_name
} 
```

我真的需要一些帮助，我现在很困惑，谢谢你