# 用 Yesod 在 Haskell 中创建博客——返回 JSON

> 原文：<https://dev.to/riccardoodone/building-a-blog-in-haskell-with-yesod-returning-json-2fl5>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-08-12-building-a-blog-in-haskell-with-yesod%E2%80%93returning-JSON.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

这是一个关于 [Yesod](https://www.yesodweb.com/) 的系列:一个 Haskell web 框架，遵循类似于 [Rails](https://rubyonrails.org/) 的哲学。事实上，它非常固执己见，并且提供了许多现成的功能。

一本关于 Yesod 的好书可以在网上免费获得:[用 Haskell 和 Yesod 开发 web 应用](https://www.yesodweb.com/book)。这就是为什么这个系列将是对来自[回购](https://github.com/3v0k4/yesod-blog)的提交的评论，我们将使用它来开发一个超级简单的博客。

换句话说，这不是学习如何使用 Yesod 的好材料。然而，它有望给出该框架如何工作的概述。

## JSON 政权！

这篇文章的计划是将整个博客转换成一个 API。不幸的是，编译器夹在中间。因此，这只是实现这一目标的第一步。

特别是，在这里我们将看到如何以 JSON 而不是 HTML 的形式返回文章列表。

提交[53c 06240 c8 b 41438 f 35475284588 e 67950 ff 8800](https://github.com/3v0k4/yesod-blog/commit/53c06240c8b41438f35475284588e67950ff8800)是最初的尝试。

通过将`json`添加到`Post`和`User`模型中，我们可以免费获得一个`ToJSON`实例。然后，我们可以用
在处理程序中使用它

```
return $ object [ "posts" .= allPosts ] 
```

Enter fullscreen mode Exit fullscreen mode

注意`allPosts`是一个元组列表。尤其是`(post, user)`。这就是为什么最终的 JSON 看起来像下面这样:

```
{  "posts":[  [  {  "text":"Luigi",  "userId":3,  "id":5,  "title":"I am"  },  {  "password":null,  "ident":"luigi",  "id":3  }  ],  [  {  "text":"333",  "userId":2,  "id":4,  "title":"333"  },  {  "password":null,  "ident":"mario",  "id":2  }  ],  [  {  "text":"text",  "userId":2,  "id":3,  "title":"title"  },  {  "password":null,  "ident":"mario",  "id":2  }  ],  [  {  "text":"text",  "userId":1,  "id":2,  "title":"title"  },  {  "password":null,  "ident":"riccardo",  "id":1  }  ],  [  {  "text":"1",  "userId":1,  "id":1,  "title":"1"  },  {  "password":null,  "ident":"riccardo",  "id":1  }  ]  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 自定义 JSON

免费生成`ToJSON`实例很酷。不幸的是，这意味着我们无法控制 JSON 的内容。

提交[70e 71484 DD 979 Fe 43 ADF 6295 F4 f 6110 e 974 a 3214](https://github.com/3v0k4/yesod-blog/commit/70e71484dd979fe43adf6295f4f6110e974a3214)通过将`(post, user)`元组包装到一个新的数据类型中，并使用它自己的`ToJSON`实例:
来修复这个问题

```
instance ToJSON PostData where
  toJSON (PostData (postEntity, userEntity)) =
    let
      post = entityVal postEntity
      postId = entityKey postEntity
      user = entityVal userEntity
      userId = entityKey userEntity
    in
    object
      [ "id" .= postId
      , "title" .= postTitle post
      , "text" .= postText post
      , "user" .= object
        [ "id" .= userId
        , "username" .= userIdent user
        ]
      ] 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们的 JSON 现在看起来像这样

```
{  "posts":[  {  "text":"Luigi",  "user":{  "username":"luigi",  "id":3  },  "id":5,  "title":"I am"  },  {  "text":"333",  "user":{  "username":"mario",  "id":2  },  "id":4,  "title":"333"  },  {  "text":"text",  "user":{  "username":"mario",  "id":2  },  "id":3,  "title":"title"  },  {  "text":"text",  "user":{  "username":"riccardo",  "id":1  },  "id":2,  "title":"title"  },  {  "text":"1",  "user":{  "username":"riccardo",  "id":1  },  "id":1,  "title":"1"  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！