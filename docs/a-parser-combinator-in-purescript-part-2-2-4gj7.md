# PureScript 中的解析器组合子(第 2 部分)

> 原文：<https://dev.to/riccardoodone/a-parser-combinator-in-purescript-part-2-2-4gj7>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-07-01-a-parser-combinator-in-purescript-part-2.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

在[之前的文章](https://dev.to/riccardoodone/a-parser-combinator-in-purescript-part-1-52bb)中，我们已经创建了解析器组合子库的基本构件。现在是时候添加一些更花哨的东西了。

## 选择与失败

到目前为止，每个解析器要么解析成功，要么解析失败。我们希望有一个能够在放弃和失败之前尝试不同解析器的解析器。

幸运的是，数学已经为我们做了肮脏的工作。事实上，我们可以使用几个类型类: [`Alt`](https://pursuit.purescript.org/packages/purescript-control/4.1.0/docs/Control.Alt#t:Alt) 和 [`Plus`](https://pursuit.purescript.org/packages/purescript-control/4.1.0/docs/Control.Plus#t:Plus) 。

捕捉在选项之间做出选择的直觉。它只有一个成员`alt`或者中缀`<|>`:T3

```
instance altParser :: (Functor Parser) => Alt Parser where
    -- alt :: forall a. f a -> f a -> f a
    alt p1 p2 = Parser (\s -> case runParser p1 s of
                                Just (Tuple v s') -> Just $ Tuple v s'
                                Nothing           -> runParser p2 s) 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，它尝试`p1`并在失败的情况下退回到`p2`。例如

```
main :: Effect Unit
main = do
  logShow $ runParser (char 'Z' <|> char 's') "string"
  -- (Just (Tuple 's' "tring")) 
```

Enter fullscreen mode Exit fullscreen mode

`Plus`代表故障:

```
instance plusParser :: (Alt Parser) => Plus Parser where
    -- empty :: forall a. f a
    empty = Parser (\_ -> Nothing) 
```

Enter fullscreen mode Exit fullscreen mode

在上一篇文章中，我们表示了`fail`解析器中的失败。原来我们可以用`empty`来代替

```
main :: Effect Unit
main = do
  -- `:: Parser Unit` is needed to make the compiler happy
  logShow $ runParser (empty :: Parser Unit) "string"
  -- Nothing 
```

Enter fullscreen mode Exit fullscreen mode

将它们放在一起

```
main :: Effect Unit
main = do
  logShow $ runParser (empty <|> anyChar) "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser (anyChar <|> empty) "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser (char 'Z' <|> char 's') "string"
  -- (Just (Tuple 's' "tring")) 
```

Enter fullscreen mode Exit fullscreen mode

## 多次尝试解析

如果一个解析器能够成功使用多次，那就太棒了。假设我们想要匹配任意数量的空格。然后我们可以写

```
spaces :: Parser Unit
spaces = do
  c <- anyChar
  if c == ' '
    then spaces
    else Parser (\s -> Just (Tuple unit $ fromChars [c] <> s)) 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，我们解析任何字符，如果它是一个我们递归调用的空格`spaces`。否则，我们通过将字符放回字符串中来查找。

或者类似地

```
spaces' :: Parser Unit
spaces' = (char ' ' >>= \_ -> spaces') <|> pure unit 
```

Enter fullscreen mode Exit fullscreen mode

我们要么成功解析一个空间并递归调用`spaces'`,要么成功退出。

那些作品

```
main :: Effect Unit
main = do
  logShow $ runParser spaces "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces "string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces' "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces' "string"
  -- (Just (Tuple Unit "string")) 
```

Enter fullscreen mode Exit fullscreen mode

然而，它们看起来很丑。但是你猜怎么着？数学又一次支持了我们。事实上，我们可以用 [`Data.List.many`](https://pursuit.purescript.org/packages/purescript-lists/5.4.0/docs/Data.List.Lazy#v:many) 。

要使用`many`，我们需要满足几个约束。其实签名是

```
many
  :: forall f a. Alternative f
  => Lazy (f (List a))
  => f a
  -> f (List a) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们用`Parser`代替`f`，那么我们得到

```
many
  :: forall a. Alternative Parser
  => Lazy (Parser (List a))
  => Parser a
  -> Parser (List a) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们需要以下实例:`Alternative Parser`和`Lazy (Parser (List a))`。

[`Alternative`](https://pursuit.purescript.org/packages/purescript-control/4.1.0/docs/Control.Alternative#t:Alternative) 很简单，因为它没有成员来实现。特别是，对于要成为`Alternative`的类型，它只需要同时是`Applicative`和`Plus`。既然`Parser`实现了那些，我们可以只写

```
instance alternativeParser :: (Applicative Parser, Plus Parser) => Alternative Parser 
```

Enter fullscreen mode Exit fullscreen mode

试图解析无限次数需要懒惰，就像我们需要懒惰来拥有一个无限列表一样(查看 [`many`](https://github.com/purescript/purescript-lists/blob/6629e0c05f2ee4b47a9b2fbcdbe6619ff17e8e28/src/Data/List.purs#L167-L167) 和 [`some`](https://github.com/purescript/purescript-lists/blob/6629e0c05f2ee4b47a9b2fbcdbe6619ff17e8e28/src/Data/List.purs#L155) 的相互递归定义以深入了解)。

在 Haskell 中这不成问题，因为默认情况下这种语言是懒惰的。在 PureScript 中我们需要使用 [`Lazy`](https://pursuit.purescript.org/packages/purescript-control/4.1.0/docs/Control.Lazy#t:Lazy) :

```
instance lazyParser :: Lazy (Parser (List a)) where
    -- defer :: (Unit -> l) -> l
    defer g = Parser (\s -> runParser (g unit) s) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着现在我们可以写

```
spaces0 :: Parser Unit
spaces0 = do
  _ <- many $ char ' '
  pure unit 
```

Enter fullscreen mode Exit fullscreen mode

我们丢弃空格(`_ <- many $ char ' '`)，因为我们对空格列表不感兴趣，我们只想消耗它们:

```
main :: Effect Unit
main = do
  logShow $ runParser spaces0 "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces0 "string"
  -- (Just (Tuple Unit "string")) 
```

Enter fullscreen mode Exit fullscreen mode

最好的部分是，现在我们也可以使用`some`来拥有一个至少需要一个匹配才能成功的解析器:

```
spaces1 :: Parser Unit
spaces1 = do
  _ <- some $ char ' '
  pure unit

main :: Effect Unit
main = do
  logShow $ runParser spaces0 "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces0 "string"
  -- Nothing 
```

Enter fullscreen mode Exit fullscreen mode

## `int`解析器

只是为了好玩，让我们把所有东西放在一个解析器中，解析`Int` s。

在顶部，我们将`int`定义为

```
int :: Parser Int
int =
  neg <|> nat 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，int 要么是负数，要么是自然数。`nat`的实现如下

```
nat :: Parser Int
nat = do
  xs <- some digit
  case fromString $ fromChars xs of
    Just n -> pure n
    Nothing -> empty 
```

Enter fullscreen mode Exit fullscreen mode

并且`neg`如下

```
neg :: Parser Int
neg = do
  _ <- char '-'
  negate <$> nat 
```

Enter fullscreen mode Exit fullscreen mode

这是

```
main :: Effect Unit
main = do
  logShow $ runParser int "123 string"
  -- (Just (Tuple 123 " string"))

  logShow $ runParser int "-123 string"
  -- (Just (Tuple -123 " string"))

  logShow $ runParser int "string"
  -- Nothing 
```

Enter fullscreen mode Exit fullscreen mode

## 整码

```
module Main where

import Prelude
import Effect (Effect)
import Effect.Console (logShow)
import Data.Maybe
import Data.Tuple
import Data.String.Yarn hiding (fromString)
import Data.List
import Data.Char.Unicode
import Data.String.CodeUnits
import Control.Alt
import Control.Plus
import Control.Alternative
import Control.Lazy (class Lazy)
import Data.Int

newtype Parser a = Parser (String -> Maybe (Tuple a String))

runParser :: forall a. Parser a -> String -> Maybe (Tuple a String)
runParser (Parser g) s = g s

instance functorParser :: Functor Parser where
    -- map :: forall a b. (a -> b) -> f a -> f b
    map g f = Parser (\s -> case runParser f s of
                              Just (Tuple v s') -> Just $ Tuple (g v) s'
                              Nothing           -> Nothing)

instance applyParser :: (Functor Parser) => Apply Parser where
    -- apply :: forall a b. f (a -> b) -> f a -> f b
    apply fg f = Parser (\s -> case runParser fg s of
                                 Just (Tuple g s') ->
                                   case runParser f s' of
                                     Just (Tuple v s'') -> Just $ Tuple (g v) s''
                                     Nothing            -> Nothing
                                 Nothing           -> Nothing)

instance applicativeParser :: (Apply Parser) => Applicative Parser where
    -- pure :: forall a. a -> f a
    pure x = Parser (\s -> Just $ Tuple x s)

instance bindParser :: (Apply Parser) => Bind Parser where
    -- bind :: forall a b. m a -> (a -> m b) -> m b
    bind m g = Parser (\s -> case runParser m s of
                               Just (Tuple v s') -> runParser (g v) s'
                               Nothing           -> Nothing)

instance monadParser :: (Bind Parser) => Monad Parser

instance altParser :: (Functor Parser) => Alt Parser where
    -- alt :: forall a. f a -> f a -> f a
    alt p1 p2 = Parser (\s -> case runParser p1 s of
                                Just (Tuple v s') -> Just $ Tuple v s'
                                Nothing           -> runParser p2 s)

instance plusParser :: (Alt Parser) => Plus Parser where
    -- empty :: forall a. f a
    empty = Parser (\_ -> Nothing)

instance alternativeParser :: (Applicative Parser, Plus Parser) => Alternative Parser

instance lazyParser :: Lazy (Parser (List a)) where
    -- defer :: (Unit -> l) -> l
    defer g = Parser (\s -> runParser (g unit) s)

fail :: forall a. Parser a
fail = Parser (\_ -> Nothing)

anyChar :: Parser Char
anyChar = Parser (\s -> case toChars s :: List Char of
                          Nil       -> Nothing
                          Cons x xs -> Just $ Tuple x $ fromChars xs)

sat :: (Char -> Boolean) -> Parser Char
sat pred = do
    c <- anyChar
    if pred c then pure c else fail

digit :: Parser Char
digit = sat isDigit

lower :: Parser Char
lower = sat isLower

upper :: Parser Char
upper = sat isUpper

letter :: Parser Char
letter = sat isAlpha

alphanum :: Parser Char
alphanum = sat isAlphaNum

char :: Char -> Parser Char
char c = sat ((==) c)

string :: String -> Parser String
string s =
  map fromChars $ case toChars s :: List Char of
       Nil       -> pure Nil
       Cons x xs -> do
          _ <- char x
          _ <- string $ fromChars xs
          pure $ Cons x xs

spaces :: Parser Unit
spaces = do
  c <- anyChar
  if c == ' '
    then spaces
    else Parser (\s -> Just (Tuple unit $ fromChars [c] <> s))

spaces' :: Parser Unit
spaces' = (char ' ' >>= \_ -> spaces') <|> pure unit

spaces0 :: Parser Unit
spaces0 = do
  _ <- many $ char ' '
  pure unit

spaces1 :: Parser Unit
spaces1 = do
  _ <- some $ char ' '
  pure unit

int :: Parser Int
int =
  neg <|> nat

nat :: Parser Int
nat = do
  xs <- some digit
  case fromString $ fromChars xs of
    Just n -> pure n
    Nothing -> empty

neg :: Parser Int
neg = do
  _ <- char '-'
  negate <$> nat

main :: Effect Unit
main = do
  logShow $ runParser anyChar "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser (char 's') "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser (char 'Z') "string"
  -- Nothing

  logShow $ runParser digit "3tring"
  -- (Just (Tuple '3' "tring"))

  logShow $ runParser (string "stri") "string"
  -- (Just (Tuple "stri" "ng"))

  logShow $ runParser (string "ZZZ") "string"
  -- Nothing

  logShow $ runParser (char 'Z' <|> char 's') "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser (empty :: Parser Unit) "string"
  -- Nothing

  logShow $ runParser (empty <|> anyChar) "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser (anyChar <|> empty) "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser spaces "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces "string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces' "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces' "string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces0 "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces0 "string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces1 "   string"
  -- (Just (Tuple Unit "string"))

  logShow $ runParser spaces1 "string"
  -- Nothing

  logShow $ runParser int "123 string"
  -- (Just (Tuple 123 " string"))

  logShow $ runParser int "-123 string"
  -- (Just (Tuple -123 " string"))

  logShow $ runParser int "string"
  -- Nothing 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

特别感谢汤姆编写了[《幻想曲、鳗鱼和规格 10:替代、添加和选择》](http://www.tomharding.me/2017/04/24/fantas-eel-and-specification-10/)和 [Typeclassopedia](https://wiki.haskell.org/Typeclassopedia) 。这两个例子清楚地解释了`Alternative`背后的直觉。此外，非常感谢来自 [fpchat](https://fpchat-invite.herokuapp.com/) 的@ monoid 音乐家和@kadblas，他们帮助我理解了`Lazy`。

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！