# Firestore 建模数据

> 原文：<https://dev.to/nikomontana/firestore-modelling-data-1kmo>

嗨，

我想用 firebase 创建一个 instagram 克隆，但在处理数据结构时发现了一些限制。

由于文档要求文档尽可能小，集合尽可能大，所以我创建了这个模型。

```
+ Profile (collection)

  |--- uid (document)

      |--- userName (String)

      |--- firstName (String)

      |--- lastName (String)

      |--- profileImgUrl (String)

      |--- userName (String)

      |--- UserPosts (collection)

         |--- uid (document)

            |--- title (String)

            |--- createdAt (timeStamp)

            |--- description (String)

            |--- imgUrl (String) 
```

我已经决定采用这种模式，因为通过将`Profile`和`Post`分割成单独的根集合，在我获得用户的`uid`后，如果没有第二次查询，我就无法通过`userName`查询帖子。

我现在的问题是，我不知道如何通过`userName`写一个查询来获取用户的帖子