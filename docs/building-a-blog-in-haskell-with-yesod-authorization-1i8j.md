# 使用 Yesod 在 Haskell 中创建博客——授权

> 原文：<https://dev.to/riccardoodone/building-a-blog-in-haskell-with-yesod-authorization-1i8j>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-08-05-building-a-blog-in-haskell-with-yesod%E2%80%93authorization.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

这是一个关于 [Yesod](https://www.yesodweb.com/) 的系列:一个 Haskell web 框架，遵循类似于 [Rails](https://rubyonrails.org/) 的哲学。事实上，它非常固执己见，并且提供了许多现成的功能。

一本关于 Yesod 的好书可以在网上免费获得:[用 Haskell 和 Yesod 开发 web 应用](https://www.yesodweb.com/book)。这就是为什么这个系列将是对来自[回购](https://github.com/3v0k4/yesod-blog)的提交的评论，我们将使用它来开发一个超级简单的博客。

换句话说，这不是学习如何使用 Yesod 的好材料。然而，它有望给出该框架如何工作的概述。

## 作者是谁？

到目前为止，登录用户与帖子没有任何关系。提交[b 9 ed 6789 ed 578 e 4349 fc 0 eee 670 e 2 df 87434 be](https://github.com/3v0k4/yesod-blog/commit/b9ed6789ed578e4349f9fc0eee670e2df87434be)向`Post`添加一个`userId`，并确保它被已验证用户的 id 填充。

## 授权删除

在一个多作者的博客中，只有所有者可以删除帖子。commit[db 722 e 785 cc 09 ad 5642486 df 17 c 770 e 85899648 c](https://github.com/3v0k4/yesod-blog/commit/db722e785cc09ad5642486df17c770e85899648c)负责处理此事。重要的一点是下面的

```
isAuthorized (PostR postId) _ = isOwner postId 
```

Enter fullscreen mode Exit fullscreen mode

## 删除按钮

因为只有所有者可以删除帖子，所以在 UI 中反映这一点是有意义的。commit[2378194354 b 6 E0 e 92 FB 1c 83 C5 feb 97 AAC 8d 219 b](https://github.com/3v0k4/yesod-blog/commit/2378194354b6e0e92fb1c83ac5feb97aac8d219b)正是这么做的:

```
$if userId == (postUserId $ entityVal post)
  <button>Delete
$else
  <p> 
```

Enter fullscreen mode Exit fullscreen mode

## 给我看看作者！

最后要做的是在他们的博客文章旁边显示作者的名字。给定我们的数据库模式(`config/models.persistentmodels` )

```
User
    ident Text
    password Text Maybe
Post
    title Text
    text Textarea
    userId UserId 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在`user`和`post`之间执行一个连接。

不幸的是，Yesod 的默认数据库库 Persistent 不支持类型安全方式的连接。事实上，唯一的办法就是使用 [`rawSql`](https://hackage.haskell.org/package/persistent-2.10.0/docs/Database-Persist-Sql.html#v:rawSql) 。

幸运的是，我们可以轻松地添加 [Esqueleto](http://hackage.haskell.org/package/esqueleto) ，它构建在持久化之上，能够执行类型安全的连接:[78 ef 59 c6e 6718 dbce 83 ea 2802 CB 70335 bb 4c ca 33](https://github.com/3v0k4/yesod-blog/commit/78ef59c6e6718dbce83ea2802cb70335bb4cca33)

## 截图还是没发生！

这里我们可以看到，删除按钮只显示给帖子的所有者，作者姓名与标题和文本一起显示:

[![](img/e2c7f36b658e481495fc15f3d3360227.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wU9e2Rv7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6d43ik5trz55a7qkdyt7.png)

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！