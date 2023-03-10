# 用 Yesod-JSON API 在 Haskell 中构建博客

> 原文：<https://dev.to/riccardoodone/building-a-blog-in-haskell-with-yesod-json-api-40d1>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-08-19-building-a-blog-in-haskell-with-yesod%E2%80%93returning-JSON-API.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

这是一个关于 [Yesod](https://www.yesodweb.com/) 的系列:一个 Haskell web 框架，遵循类似于 [Rails](https://rubyonrails.org/) 的哲学。事实上，它非常固执己见，并且提供了许多现成的功能。

一本关于 Yesod 的好书可以在网上免费获得:[用 Haskell 和 Yesod 开发 web 应用](https://www.yesodweb.com/book)。这就是为什么这个系列将是对来自[回购](https://github.com/3v0k4/yesod-blog)的提交的评论，我们将使用它来开发一个超级简单的博客。

换句话说，这不是学习如何使用 Yesod 的好材料。然而，它有望给出该框架如何工作的概述。

## 重回商界

上周的帖子以一个苦涩的音符开始:

> 这篇文章的计划是将整个博客转换成一个 API。不幸的是，编译器夹在中间。

众所周知，Haskell 编译器有时很难令人满意。然而，当程序最终进行类型检查时，这是多么令人高兴啊！这次我们搞定了。所以让我们深入研究一下。

## 认证

上周的帖子有点骗人。事实上，由于没有考虑身份验证，用户必须遵循以下步骤来获取 JSON 格式的帖子列表:

*   在浏览器上访问`/api/posts`
*   应用程序会显示登录表单
*   提交登录表单
*   再次在浏览器上访问`/api/posts`

这是因为用户没有任何方法来验证`/api/posts`请求。因此，他们必须通过登录创建一个会话(cookie)。

提交[CD 78427 e 82 babef 42 f 170 BF 7 b 3 E4 ff 423d 88 a 729](https://github.com/3v0k4/yesod-blog/commit/cd78427e82babef42f170bf7b3e4ff423d88a729)通过修补`maybeAuthId` :
修复了该问题

```
 maybeAuthId = do
        request <- waiRequest
        let mHeader = lookup "X-User-Id" (Network.Wai.requestHeaders request)
            bsToText = T.pack . BSC8.unpack
        case bsToText <$> mHeader of
          Just v ->
            return $ fromPathPiece v
          Nothing ->
            defaultMaybeAuthId 
```

Enter fullscreen mode Exit fullscreen mode

[`maybeAuthId`](http://hackage.haskell.org/package/yesod-auth-1.6.7/docs/Yesod-Auth.html#v:maybeAuthId) 默认

> 如果用户已通过身份验证，则检索用户凭据。
> 
> 默认情况下，这会调用 defaultMaybeAuthId 从会话中获取用户 Id。

医生继续说

> 这可以被覆盖，以允许通过其他方式进行身份验证，例如检查请求头中的特殊标记。这对于创建通过浏览器以外的方式访问的 API 特别有用。

这就是为什么上面打了补丁的代码会在`X-User-Id`头中查找 id，并在没有找到时委托默认行为。

## 登记

到目前为止，我们一直使用 [`Yesod.Auth.Dummy`](http://hackage.haskell.org/package/yesod-auth-1.6.7/docs/Yesod-Auth-Dummy.html) 进行认证和注册。事实上，`Yesod.Auth.Dummy`呈现了一个带有一个文本字段的登录表单。如果它是用现有用户的用户名提交的，那么这个用户就是登录用户。否则，它首先向数据库添加一条新记录，然后创建会话。

很遗憾，`Yesod.Auth.Dummy`不支持通过 JSON 请求注册。所以，我们得自己打补丁。

提交[78e 13 f 807 f 5674 aa 0a 84 e 2633d 7967 fa 02 b 755 cf](https://github.com/3v0k4/yesod-blog/commit/78e13f807f5674aa0a84e2633d7967fa02b755cf)只需从`Yesod.Auth.Dummy`复制/粘贴`authDummy`代码。真正的改变是在提交[CD 78427 e 82 babef 42 f 170 bf7 B3 E4 ff 423d 88 a 729](https://github.com/3v0k4/yesod-blog/commit/cd78427e82babef42f170bf7b3e4ff423d88a729)中完成的。

首先，我们引入一个解析器

```
parser :: Value -> Parser Text
parser = withObject "ident" (\obj -> do
                ident <- obj .: "ident"
                return ident) 
```

Enter fullscreen mode Exit fullscreen mode

它能够像下面的
一样从 JSON 中提取`ident`

```
{  "ident":  "MY USERNAME"  } 
```

Enter fullscreen mode Exit fullscreen mode

其次，我们给`authDummy`插件的`dispatch`函数打补丁。那是负责处理给`/auth/page/dummy`的邮件请求的地方。到目前为止，它只在提交 html 表单时有效。为了让它能够处理 JSON 请求，我们需要做一些修改:

```
 authDummy :: YesodAuth m => AuthPlugin m
 authDummy =
     AuthPlugin "dummy" dispatch login
   where
     dispatch "POST" [] = do
-        ident <- runInputPost $ ireq textField "ident"
-        setCredsRedirect $ Creds "dummy" ident [] +        result <- runInputPostResult $ ireq textField "ident"
+        case result of
+          FormSuccess ident ->
+            setCredsRedirect $ Creds "dummy" ident []
+          _ -> do
+            (result :: Result Value) <- parseCheckJsonBody
+            case result of
+              Success val -> do
+                let mIdent = parseEither parser val
+                case mIdent of
+                  Right ident ->
+                    setCredsRedirect $ Creds "dummy" ident []
+                  Left err ->
+                    invalidArgs [T.pack err]
+              Error err ->
+                invalidArgs [T.pack err]
     dispatch _ _ = notFound 
```

Enter fullscreen mode Exit fullscreen mode

更详细地说，我们首先尝试用`runInputPostResult`从 POST 参数(即表单提交)中提取`ident`。如果这成功了(即`FormSuccess`)，我们就保持原来的行为不变。

如果失败，我们尝试用`parseCheckJsonBody`获取 JSON 体。如果失败，我们返回`invalidArgs`(即 HTTP 400)。如果成功，我们尝试从 JSON 主体中提取`ident`文本。如果成功，我们做与 POST 参数成功时相同的事情。

## 创建新帖子

事实证明，前一节中使用的许多概念也适用于创建新帖子。我们在提交中这样做[4922 c8 db 2706 FD 0999 ef 478373 b 82326 f 5851 b4d](https://github.com/3v0k4/yesod-blog/commit/4922c8db2706fd0999ef478373b82326f5851b4d):

```
postApiPostsR :: Handler Value
postApiPostsR = do
  (result :: Result Value) <- parseCheckJsonBody
  case result of
    Success val -> do
      let mPost = parseEither postParser val
      case mPost of
        Right post -> do
          postId <- runDB $ insert post
          return $ object [ "post" .= post, "id" .= postId ]
        Left err ->
          invalidArgs [pack err]
    Error err ->
      invalidArgs [pack err]

postParser :: Value -> Parser Post
postParser = withObject "Post" (\obj -> do
                title <- obj .: "title"
                text <- obj .: "text"
                userId <- obj .: "userId"
                return $ Post title text userId) 
```

Enter fullscreen mode Exit fullscreen mode

## 删除帖子

这是最简单的部分，看看 commit[166 aa 28741 Fe 532 AE 664308 a2 af4 b 28638 b6d 560](https://github.com/3v0k4/yesod-blog/commit/166aa28741fe532ae664308a2af4b28638b6d560)有多小！

唯一值得一提的是
中的`return Null`

```
deleteApiPostR :: PostId -> Handler Value
deleteApiPostR postId = do
  _ <- runDB $ delete postId
  return Null 
```

Enter fullscreen mode Exit fullscreen mode

这只是返回空 JSON 主体的一个快捷方式。

## 冰壶

注册:

```
curl -XPOST -d '{"ident":"super cool username"}' -H "Content-Type: application/json" -H "Accept: application/json" http://localhost:3000/auth/page/dummy

# {"message":"Login Successful"} 
```

Enter fullscreen mode Exit fullscreen mode

未认证的请求:

```
curl -H "Accept: application/json" http://localhost:3000/api/posts

# {"authentication_url":"http://localhost:3000/auth/login","message":"Not logged in"} 
```

Enter fullscreen mode Exit fullscreen mode

帖子列表:

```
curl -H "X-User-Id: 1" -H "Accept: application/json" http://localhost:3000/api/posts

# {"posts":[{"text":"Luigi","user": {"username":"luigi","id":3},"id":5,"title":"I am"},{"text":"333","user":{"username":"mario","id":2},"id":4,"title":"333"},{"text":"text","user":{"username":"mario","id":2},"id":3,"title":"title"},{"text":"text","user"{"username":"riccardo","id":1},"id":2,"title":"title"},{"text":"1","user":{"username":"riccardo","id":1},"id":1,"title":"1"}]} 
```

Enter fullscreen mode Exit fullscreen mode

创建缺少参数的新帖子:

```
curl -XPOST -d '{"title":"some title","text":"some text"}' -H "X-User-Id: 1" -H "Content-Type: application/json" -H "Accept: application/json" http://localhost:3000/api/posts

# {"message":"Invalid Arguments","errors":["Error in $: key \"userId\" not present"]} 
```

Enter fullscreen mode Exit fullscreen mode

创建新帖子:

```
curl -XPOST -d '{"title":"some title","text":"some text","userId": 1}' -H "X-User-Id: 1" -H "Content-Type: application/json" -H "Accept: application/json" http://localhost:3000/api/posts

# {"post":{"text":"some text","userId":1,"title":"some title"},"id":6} 
```

Enter fullscreen mode Exit fullscreen mode

删除非所有者的帖子:

```
curl -XDELETE -H "X-User-Id: 2" -H "Accept: application/json" http://localhost:3000/api/posts/6

# {"message":"Permission Denied. only the author can delete their post"} 
```

Enter fullscreen mode Exit fullscreen mode

删除一个帖子:

```
curl -XDELETE -H "X-User-Id: 1" -H "Accept: application/json" http://localhost:3000/api/posts/6

# null 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

这里我们有我们的 JSON API。这是一场艰苦的斗争。但是当它最终编译完成时，那种感觉真好！！！

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！