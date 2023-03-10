# 将木卫一和任何一个合并成一个单子

> 原文：<https://dev.to/riccardoodone/merging-io-and-either-into-one-monad-mdc>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-09-02-merging-io-and-either-into-one-monad.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

消化新概念的一个好方法是从其他人那里复制想法，并试图让他们编译。这正是我们今天要做的。

如果你想阅读(更好的)原著，请查看[“单子变形金刚的温柔介绍”](https://two-wrongs.com/a-gentle-introduction-to-monad-transformers)。

## 问题

让我们写一个从邮件中提取域名的函数:

```
getDomain :: Text -> Either LoginError Text
getDomain email =
  case T.splitOn "@" email of
    [_, domain] -> Right domain
    _           -> Left InvalidEmail 
```

Enter fullscreen mode Exit fullscreen mode

然后，假设我们想向用户索要他们的电子邮件，并使用域作为身份验证的令牌:

```
getToken :: IO (Either LoginError Text)
getToken = do
  T.putStrLn "Enter email address:"
  email <- T.getLine
  pure $ getDomain email 
```

Enter fullscreen mode Exit fullscreen mode

除了令牌，用户还需要输入密码。用户数据库如下:

```
users :: Map Text Text
users = Map.fromList
  [ ("example.com", "qwerty123")
  , ("localhost", "password")
  ] 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的认证系统，三种可能的错误是可能的:

```
data LoginError
  = InvalidEmail
  | NoSuchUser
  | WrongPassword
  deriving (Show) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们可以把所有的放在一起:

```
userLogin :: IO (Either LoginError Text)
userLogin = do
  token <- getToken
  case token of
    Right domain ->
      case Map.lookup domain users of
        Just password -> do
          T.putStrLn "Enter password:"
          pw <- T.getLine
          if pw == password then
            pure $ Right domain
          else
            pure $ Left WrongPassword
        Nothing ->
          pure $ Left NoSuchUser
    left    ->
      pure $ left 
```

Enter fullscreen mode Exit fullscreen mode

我们完了。

不，这是个笑话。那段代码真的很难看。我们需要做点什么。

清理`userLogin`有一个秘密:缩进来自于我们处理两个不同单子的事实(即`IO`和`Either`)。

让我们看看将两者合二为一是否能解决我们的问题。

## 解

我们首先将两个单子包装成`EitherIO` :

```
data EitherIO e a = EitherIO {
  runEitherIO :: IO (Either e a)
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们为`Functor`、`Applicative`和`Monad` :
定义实例

```
instance Functor (EitherIO e) where
  -- fmap :: Functor f => (a -> b) -> f a -> f b
  -- fmap g f = EitherIO $ fmap (fmap g) (runEitherIO f)
  fmap g = EitherIO . fmap (fmap g) . runEitherIO

-- THIS IS NOT LAWFUL. DO NOT USE FOR SERIOUS STUFF!
instance Applicative (EitherIO e) where
  -- pure :: a -> f a
  pure = EitherIO . pure . Right
  -- (<*>) :: f (a -> b) -> f a -> f b
  fg <*> f = EitherIO $ liftA2 (<*>) (runEitherIO fg) (runEitherIO f)

instance Monad (EitherIO e) where
  -- (>>=) :: forall a b. m a -> (a -> m b) -> m b
  m >>= mg = EitherIO $ (runEitherIO m >>= either (pure . Left) (runEitherIO . mg)) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用新单子
来定义`getToken`

```
getToken' :: EitherIO LoginError Text
getToken' = do
  EitherIO $ fmap Right (T.putStrLn "Enter email address:")
  email <- EitherIO $ fmap Right T.getLine
  EitherIO $ pure $ getDomain email 
```

Enter fullscreen mode Exit fullscreen mode

更好的是，我们可以创建几个助手来使它更干净

```
liftEither :: Either e a -> EitherIO e a
liftEither = EitherIO . pure

liftIO :: IO a -> EitherIO e a
liftIO = EitherIO . fmap Right

getToken'' :: EitherIO LoginError Text
getToken'' = do
  liftIO $ T.putStrLn "Enter email address:"
  email <- liftIO T.getLine
  liftEither $ getDomain email 
```

Enter fullscreen mode Exit fullscreen mode

现在，`userLogin`可以改写为:

```
userLogin' :: EitherIO LoginError Text
userLogin' = do
  domain <- getToken''
  case Map.lookup domain users of
    Just password -> do
      liftIO $ T.putStrLn "Enter password:"
      pw <- liftIO T.getLine
      if pw == password then
        liftEither $ Right domain
      else
        liftEither $ Left WrongPassword
    Nothing ->
      liftEither $ Left NoSuchUser 
```

Enter fullscreen mode Exit fullscreen mode

我们移除了一层嵌套。这是因为使用`EitherIO`的单子实例，我们用`<- getToken''`而不是之前的`Either _ domain`来“提取”域。但是我们可以做得更好:

```
userLogin'' :: EitherIO LoginError Text
userLogin'' = do
  domain <- getToken''
  password <- maybe (liftEither $ Left NoSuchUser) pure $ Map.lookup domain users
  liftIO $ T.putStrLn "Enter password:"
  pw <- liftIO T.getLine
  if pw == password then
    liftEither $ Right domain
  else
    liftEither $ Left WrongPassword 
```

Enter fullscreen mode Exit fullscreen mode

所有这些就绪后，我们可以使用:
运行登录

```
main :: IO ()
main = do
  result <- runEitherIO getToken''
  print result 
```

Enter fullscreen mode Exit fullscreen mode

## 但是等等，还有！

我们可以将`userLogin`重构为:

```
throwE :: e -> EitherIO e a
throwE = liftEither . Left

userLogin''' :: EitherIO LoginError Text
userLogin''' = do
  domain <- getToken''
  password <- maybe (throwE NoSuchUser) pure $ Map.lookup domain users
  liftIO $ T.putStrLn "Enter password:"
  pw <- liftIO T.getLine
  if pw == password then
    liftEither $ Right domain
  else
    throwE WrongPassword 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们能扔，我们也能接住:

```
catchE :: EitherIO e a -> (e -> EitherIO e a) -> EitherIO e a
catchE throwing handler = EitherIO $ do
  result <- runEitherIO throwing
  case result of
    Left  e -> runEitherIO $ handler e
    success -> pure success 
```

Enter fullscreen mode Exit fullscreen mode

并有一个处理程序，允许用户在出现`WrongPassword`错误时重试登录:

```
wrongPasswordHandler :: LoginError -> EitherIO LoginError Text
wrongPasswordHandler WrongPassword = do
  liftIO $ T.putStrLn "Wrong password, one more chance."
  userLogin'''
wrongPasswordHandler e = throwE e 
```

Enter fullscreen mode Exit fullscreen mode

有了这个我们就可以:

```
main :: IO ()
main = do
  result <- runEitherIO $ userLogin''' `catchE` wrongPasswordHandler
  print result 
```

Enter fullscreen mode Exit fullscreen mode

## 整码

```
{-# LANGUAGE OverloadedStrings #-}

module Main where

import Data.Text as T
import Data.Text.IO as T
import Data.Map as Map
import Data.Either (either)
import Data.Maybe (maybe)
import Control.Applicative (liftA2)

data LoginError
  = InvalidEmail
  | NoSuchUser
  | WrongPassword
  deriving (Show)

getDomain :: Text -> Either LoginError Text
getDomain email =
  case T.splitOn "@" email of
    [_, domain] -> Right domain
    _           -> Left InvalidEmail

getToken :: IO (Either LoginError Text)
getToken = do
  T.putStrLn "Enter email address:"
  email <- T.getLine
  pure $ getDomain email

users :: Map Text Text
users = Map.fromList
  [ ("example.com", "qwerty123")
  , ("localhost", "password")
  ]

userLogin :: IO (Either LoginError Text)
userLogin = do
  token <- getToken
  case token of
    Right domain ->
      case Map.lookup domain users of
        Just password -> do
          T.putStrLn "Enter password:"
          pw <- T.getLine
          if pw == password then
            pure $ Right domain
          else
            pure $ Left WrongPassword
        Nothing ->
          pure $ Left NoSuchUser
    left    ->
      pure $ left

data EitherIO e a = EitherIO {
  runEitherIO :: IO (Either e a)
}

instance Functor (EitherIO e) where
  -- fmap :: Functor f => (a -> b) -> f a -> f b
  -- fmap g f = EitherIO $ fmap (fmap g) (runEitherIO f)
  fmap g = EitherIO . fmap (fmap g) . runEitherIO

instance Applicative (EitherIO e) where
  -- pure :: a -> f a
  pure = EitherIO . pure . Right
  -- (<*>) :: f (a -> b) -> f a -> f b
  fg <*> f = EitherIO $ liftA2 (<*>) (runEitherIO fg) (runEitherIO f)

instance Monad (EitherIO e) where
  -- (>>=) :: forall a b. m a -> (a -> m b) -> m b
  m >>= mg = EitherIO $ (runEitherIO m >>= either (pure . Left) (runEitherIO . mg))

getToken' :: EitherIO LoginError Text
getToken' = do
  EitherIO $ fmap Right (T.putStrLn "Enter email address:")
  email <- EitherIO $ fmap Right T.getLine
  EitherIO $ pure $ getDomain email

liftEither :: Either e a -> EitherIO e a
liftEither = EitherIO . pure

liftIO :: IO a -> EitherIO e a
liftIO = EitherIO . fmap Right

getToken'' :: EitherIO LoginError Text
getToken'' = do
  liftIO $ T.putStrLn "Enter email address:"
  email <- liftIO T.getLine
  liftEither $ getDomain email

userLogin' :: EitherIO LoginError Text
userLogin' = do
  domain <- getToken''
  case Map.lookup domain users of
    Just password -> do
      liftIO $ T.putStrLn "Enter password:"
      pw <- liftIO T.getLine
      if pw == password then
        liftEither $ Right domain
      else
        liftEither $ Left WrongPassword
    Nothing ->
      liftEither $ Left NoSuchUser

userLogin'' :: EitherIO LoginError Text
userLogin'' = do
  domain <- getToken''
  password <- maybe (liftEither $ Left NoSuchUser) pure $ Map.lookup domain users
  liftIO $ T.putStrLn "Enter password:"
  pw <- liftIO T.getLine
  if pw == password then
    liftEither $ Right domain
  else
    liftEither $ Left WrongPassword

throwE :: e -> EitherIO e a
throwE = liftEither . Left

userLogin''' :: EitherIO LoginError Text
userLogin''' = do
  domain <- getToken''
  password <- maybe (throwE NoSuchUser) pure $ Map.lookup domain users
  liftIO $ T.putStrLn "Enter password:"
  pw <- liftIO T.getLine
  if pw == password then
    liftEither $ Right domain
  else
    throwE WrongPassword

catchE :: EitherIO e a -> (e -> EitherIO e a) -> EitherIO e a
catchE throwing handler = EitherIO $ do
  result <- runEitherIO throwing
  case result of
    Left  e -> runEitherIO $ handler e
    success -> pure success

wrongPasswordHandler :: LoginError -> EitherIO LoginError Text
wrongPasswordHandler WrongPassword = do
  liftIO $ T.putStrLn "Wrong password, one more chance."
  userLogin'''
wrongPasswordHandler e = throwE e

main :: IO ()
main = do
  print $ getDomain "a"
  print $ getDomain "a@b"
  t <- getToken
  print t
  result <- userLogin
  print result
  t' <- runEitherIO getToken''
  print t'
  t'' <- runEitherIO $ userLogin''' `catchE` wrongPasswordHandler
  print t'' 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！