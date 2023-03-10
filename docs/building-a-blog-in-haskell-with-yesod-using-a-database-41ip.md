# 用 Yesod 在 Haskell 中创建博客——使用数据库

> 原文：<https://dev.to/riccardoodone/building-a-blog-in-haskell-with-yesod-using-a-database-41ip>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-07-22-building-a-blog-in-haskell-with-yesod%E2%80%93using-a-database.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

这是一个关于 [Yesod](https://www.yesodweb.com/) 的系列:一个 Haskell web 框架，遵循类似于 [Rails](https://rubyonrails.org/) 的哲学。事实上，它非常固执己见，并且提供了许多现成的功能。

一本关于 Yesod 的好书可以在网上免费获得:[用 Haskell 和 Yesod 开发 web 应用](https://www.yesodweb.com/book)。这就是为什么这个系列将是对来自[回购](https://github.com/3v0k4/yesod-blog)的提交的评论，我们将使用它来开发一个超级简单的博客。

换句话说，这不是学习如何使用 Yesod 的好材料。然而，它有望给出该框架如何工作的概述。

* * *

## 与 SQLite3 交互

在开始之前，让我们回顾一些与 SQLite3 交互的命令。这是我们正在使用的数据库，因为它是脚手架 Yesod 应用程序的默认数据库。

*   “打开”一个数据库:`SQLite3 MY_DATABASE.sqlite3`
*   列出所有表格:`.tables`
*   检查表格的模式`.schema MY_TABLE`

在这篇文章的后面，创建一个`user`或者`post` :
会很有用

```
INSERT INTO user (ident, password) VALUES ("admin", "admin");
INSERT INTO post (title, text) VALUES ("title", "text"); 
```

Enter fullscreen mode Exit fullscreen mode

## 仅在数据库中登录用户

commit[b 90 b5 cc 29 b 053 ed 900 A8 b 395 b 097688264388 ebf](https://github.com/3v0k4/yesod-blog/commit/b90b5cc29b053ed900a8b395b097688264388ebf)在用户提交登录表单时引入一个条件。特别是，它检查数据库中是否存在具有提交的用户名和密码的用户。如果是这种情况，用户将被重定向到文章页面。否则，将重新呈现登录表单。

在这种情况下，我们重用了搭建的 Yesod 应用程序提供给我们的持久实体:

```
User
    ident Text
    password Text Maybe
    UniqueUser ident 
```

Enter fullscreen mode Exit fullscreen mode

我们真应该把`ident`改成`username`，把`password`改成`Text`，而不是`Maybe Text`。因为其他搭建的代码依赖于该定义，并且我们可能希望在将来重用它，所以我们保留了它。

提交的重要部分是

```
user <- runDB $ selectList [ UserIdent ==. username login, UserPassword ==. Just (password login) ] [] 
```

Enter fullscreen mode Exit fullscreen mode

[`selectList`](https://hackage.haskell.org/package/persistent-2.10.0/docs/Database-Persist-Class.html#v:selectList) 就是出自执着。这个类型看起来很吓人，但是在本例中，它只是返回一个用户列表，这些用户与登录表单中的`username`和`password`相匹配。

上面的实现既不有用也不安全。事实上，没有为用户创建任何会话，并且假设密码以明文形式保存在数据库中。我们将在稍后的帖子中修复会话部分。

## 使帖子成为一个实体

提交[525368882062 eeeb 1717 e 7112 c 74 b 25214 fdf 736](https://github.com/3v0k4/yesod-blog/commit/525368882062eeeb1717e7112c74b25214fdf736)使`Post`数据类型成为持久实体。通过这样做，我们可以免费生成:

*   刚刚删除的`Post`数据类型
*   我们将需要与帖子和数据库交互的其他零碎内容

## 来自数据库的帖子

commit[7a 0735 fc 2 Fe 8 ce 3420773 CD 2 BD 8 a 41 DDE 6d 74 fa 3](https://github.com/3v0k4/yesod-blog/commit/7a0735fc2fe8ce3420773cd2bd8a41dde6d74fa3)删除硬编码的帖子，支持数据库中的帖子。

重要的部分是

```
allPosts :: [Entity Post] <- runDB $ selectList [] [] 
```

Enter fullscreen mode Exit fullscreen mode

需要 type 注释来让 Persistent 知道针对哪个表运行`selectList`。

## 帖子排序

提交[716817 e7b 54310 FD 1b 588 E4 a 77 db 5d 199 e 084383](https://github.com/3v0k4/yesod-blog/commit/716817e7b54310fd1b588e4a77db5d199e084383)确保帖子按照`Desc`结束 id 顺序排序:

```
allPosts :: [Entity Post] <- runDB $ selectList [] [ Desc PostId ] 
```

Enter fullscreen mode Exit fullscreen mode

## 在数据库中创建帖子

提交[1 EC 890d 790 f 08027 fc 5 e 58 BFA 8 D3 a 354d 86 ab 4 e 0](https://github.com/3v0k4/yesod-blog/commit/1ec890d790f08027fc5e58bfa8d3a354d86ab4e0)将新的 post 表单连接到数据库。换句话说，只要表单提交成功，数据库中就会添加一条新记录。

## 删除帖子按钮

提交[4 FEA 7c 6460 f 73615d 615 ba 03 e 46 e 157 C4 e 949424](https://github.com/3v0k4/yesod-blog/commit/4fea7c6460f73615d615ba03e46e157c4e949424)为每篇文章添加一个删除按钮。

HTML `form`只允许使用`GET`或`POST`作为 HTTP 动作。在这种情况下，我们需要一个`DELETE`来跟随 REST。我们可以用
来模拟

```
<form method=post action=@{PostR $ entityKey post}?_method=DELETE> 
```

Enter fullscreen mode Exit fullscreen mode

关键部分是`?_method=DELETE`。有了这个 url 参数，表单将被路由到`deletePostR`而不是`postPostR`。这多亏了[网络。wai . middleware . method override](https://stackoverflow.com/questions/22902419/yesod-put-and-delete-using-hidden-method-parameter/22903897#answer-22903897)。

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！