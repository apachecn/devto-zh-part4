# PureScript 中的解析器组合子(第 1/2 部分)

> 原文：<https://dev.to/riccardoodone/a-parser-combinator-in-purescript-part-1-52bb>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-06-24-a-parser-combinator-in-purescript-part-1.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

让我们从定义`Parser`类型开始。其思想是，解析器是一个获取字符串并试图从中“解析”出一些东西的函数。

每当解析失败时，函数返回`Nothing`。否则，将产生一组结果:中间值(解析值)和字符串的剩余部分。

这和状态单子背后的直觉真的很像。[上一个帖子](https://dev.to/riccardoodone/a-state-monad-in-purescript-p1k)都是这么回事！

`Parser`类型定义如下:

```
newtype Parser a = Parser (String -> Maybe (Tuple a String)) 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要一种方法来运行一个解析器，给定一个要解析的字符串:

```
runParser :: forall a. Parser a -> String -> Maybe (Tuple a String)
runParser (Parser g) s = g s 
```

Enter fullscreen mode Exit fullscreen mode

为了允许解析器的组合，我们将实例定义为 Monad:

```
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
```

Enter fullscreen mode Exit fullscreen mode

## 实现解析器

我们能创建的最简单的解析器是一个总是失败的解析器:

```
fail :: forall a. Parser a
fail = Parser (\_ -> Nothing) 
```

Enter fullscreen mode Exit fullscreen mode

第二个是使用`Char` :
的解析器

```
anyChar :: Parser Char
anyChar = Parser (\s -> case toChars s :: List Char of
                          Nil       -> Nothing
                          Cons x xs -> Just $ Tuple x $ fromChars xs) 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，在字符串为空的情况下，它失败，否则它产生中间结果(即字符串的第一个字符)和字符串的剩余部分的元组。

示例:

```
main :: Effect Unit
main = do
  logShow $ runParser anyChar "string"
  -- (Just (Tuple 's' "tring")) 
```

Enter fullscreen mode Exit fullscreen mode

第三个解析器使用满足谓词的`Char`。这就是我们开始使用 Monad 实例来访问中间值并能够失败的地方:

```
sat :: (Char -> Boolean) -> Parser Char
sat pred = do
    c <- anyChar
    if pred c then pure c else fail 
```

Enter fullscreen mode Exit fullscreen mode

有了工具箱中的`sat`，我们可以引入更多的解析器:

```
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
```

Enter fullscreen mode Exit fullscreen mode

示例

```
main :: Effect Unit
main = do
  logShow $ runParser (char 's') "string"
  -- (Just (Tuple 's' "tring"))

  logShow $ runParser (char 'Z') "string"
  -- Nothing

  logShow $ runParser digit "3tring"
  -- (Just (Tuple '3' "tring")) 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用递归调用并创建更复杂的解析器。例如一个`string`解析器:

```
string :: String -> Parser String
string s =
  map fromChars $ case toChars s :: List Char of
       Nil       -> pure Nil
       Cons x xs -> do
          _ <- char x
          _ <- string $ fromChars xs
          pure $ Cons x xs 
```

Enter fullscreen mode Exit fullscreen mode

这个解析器只是不断尝试从传递的字符串`s`中解析下一个字符。中间结果(即`_ <- char x`)总是被丢弃。那是因为要么

*   解析器通过成功解析每个字符到达字符串`s`的末尾。这意味着中间值正好是`s`(即递归的`Cons x xs`)
*   解析器无法解析字符串`s`中的任何字符，并关闭到`Nothing`的电路

示例

```
main :: Effect Unit
main = do
  logShow $ runParser (string "stri") "string"
  -- (Just (Tuple "stri" "ng"))

  logShow $ runParser (string "ZZZ") "string"
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
import Data.String.Yarn
import Data.List
import Data.Char.Unicode
import Data.String.CodeUnits as CU

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
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！