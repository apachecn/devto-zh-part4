# 在 Haskell 中重构火星车 Kata

> 原文：<https://dev.to/riccardoodone/refactoring-the-mars-rover-kata-in-haskell-3n3p>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-09-23-refactoring-the-mars-rover-kata-in-haskell.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

上周，我收到了一些友好的批评，因为我已经通过推迟我们的类型驱动开发的 kata 的重构步骤结束了[帖子](https://dev.to/riccardoodone/mars-rover-kata-in-haskell-16bl):

> 我今天没有更多的时间了。我们将在以后的帖子中进行重构！

最好尽快解决！让我们重构(真的)。

但首先，这是我们上周学到的代码:

```
{-# LANGUAGE OverloadedStrings #-}

module Main where

import Data.Text (Text)
import qualified Data.Text as T (lines, words, unpack)
import Text.Read (readMaybe)

type Coord =
    (Int, Int)

type CoordDir =
    (Coord, Dir)

data Turn
    = L
    | R
    deriving (Show, Read)

data Dir
    = N
    | E
    | S
    | W
    deriving (Show, Read)

data Cmd
    = Turn Turn
    | Move
    deriving (Show)

readMaybeT :: Read a => Text -> Maybe a
readMaybeT = readMaybe . T.unpack

parsePlateauMax :: Text -> Maybe Coord
parsePlateauMax txt =
    case T.words txt of
        [x, y] -> (,) <$> readMaybeT x <*> readMaybeT y
        _      -> Nothing

parseStartingCoordDir :: Text -> Maybe CoordDir
parseStartingCoordDir txt = do
    (x, y, d) <- case T.words txt of
        [x, y, d] -> (,,) <$> readMaybeT x <*> readMaybeT y <*> readMaybeT d
        _      -> Nothing
    Just ((x, y), d)

parseCommands :: Text -> Maybe [Cmd]
parseCommands txt = toCmd `traverse` T.unpack txt
    where
        toCmd :: Char -> Maybe Cmd
        toCmd 'L' = Just (Turn L)
        toCmd 'R' = Just (Turn R)
        toCmd 'M' = Just Move
        toCmd _   = Nothing

runCommands :: [Cmd] -> Coord -> CoordDir -> CoordDir
runCommands []       _   coordDir             = coordDir
runCommands (c:cmds) max coordDir = runCommands cmds max $ newCoordDir c
    where
        newCoordDir (Turn L) = turnL coordDir
        newCoordDir (Turn R) = turnR coordDir
        newCoordDir Move     = move coordDir

        turnL (c, N) = (c, W)
        turnL (c, E) = (c, N)
        turnL (c, S) = (c, E)
        turnL (c, W) = (c, S)

        turnR (c, N) = (c, E)
        turnR (c, E) = (c, S)
        turnR (c, S) = (c, W)
        turnR (c, W) = (c, N)

        move ((x, y), N) = (wrap max (x, y+1), N)
        move ((x, y), E) = (wrap max (x+1, y), E)
        move ((x, y), S) = (wrap max (x, y-1), S)
        move ((x, y), W) = (wrap max (x-1, y), W)

        wrap (maxX, maxY) (x, y) = (wrap' maxX x, wrap' maxY y)

        wrap' max x | x < 0       = max + 1 + x
        wrap' max x | x > max     = x `rem` max
        wrap' max x | otherwise   = x

run :: Text -> Maybe CoordDir
run txt = do
    (max, coordDir, cmds) <- go $ T.lines txt
    Just $ runCommands cmds max coordDir
    where
        go [f, s, t] = (,,) <$> parsePlateauMax f <*> parseStartingCoordDir s <*> parseCommands t
        go _         = Nothing

main :: IO ()
main = do
    let commands = "5 5\n1 2 N\nLMLMLMLMM"
    print $ parsePlateauMax "5 5"
    print $ parseStartingCoordDir "1 2 N"
    print $ parseCommands "LMLMLMLMM"
    maybe (print "ERR!") print $ run commands 
```

Enter fullscreen mode Exit fullscreen mode

## CyclicEnum

几周前，我们[引入了](https://dev.to/riccardoodone/fun-with-typeclasses-ap7)`CyclicEnum`类型类。

让我们在我们的`turnL`和`turnR`函数中使用它:

```
 data Dir
     = N
     | E
     | S
     | W
-    deriving (Show, Read) +    deriving (Show, Read, Eq, Enum, Bounded, CyclicEnum) 
...

-        turnL (c, N) = (c, W)
-        turnL (c, E) = (c, N)
-        turnL (c, S) = (c, E)
-        turnL (c, W) = (c, S) +        turnL (c, d) = (c, csucc d) 
-        turnR (c, N) = (c, E)
-        turnR (c, E) = (c, S)
-        turnR (c, S) = (c, W)
-        turnR (c, W) = (c, N) +        turnR (c, d) = (c, cpred d) 
```

Enter fullscreen mode Exit fullscreen mode

代码更加整洁，但最重要的是，如果我们添加了更多的`Dir`像`NE`(即东北)，我们就不必更新`turnL`和`turnR`。

由于元组是函子，我们可以进一步重构:

```
-        turnL (c, d) = (c, csucc d) +        turnL = fmap csucc 
-        turnR (c, d) = (c, cpred d)
+        turnR = fmap cpred 
```

Enter fullscreen mode Exit fullscreen mode

以及内联`turnL`和`turnR` :

```
-        newCoordDir (Turn L) = turnL coordDir
-        newCoordDir (Turn R) = turnR coordDir +        newCoordDir (Turn L) = fmap csucc coordDir
+        newCoordDir (Turn R) = fmap cpred coordDir
         newCoordDir Move     = move coordDir

-        turnL = fmap csucc
-
-        turnR = fmap cpred 
```

Enter fullscreen mode Exit fullscreen mode

## 解析

为了使解析更加可靠，我们可以利用 [Parsec](http://hackage.haskell.org/package/parsec) 。

我们首先删除旧代码:

```
-readMaybeT :: Read a => Text -> Maybe a
-readMaybeT = readMaybe . T.unpack
-
-parsePlateauMax :: Text -> Maybe Coord
-parsePlateauMax txt =
-    case T.words txt of
-        [x, y] -> (,) <$> readMaybeT x <*> readMaybeT y
-        _      -> Nothing
-
-parseStartingCoordDir :: Text -> Maybe CoordDir
-parseStartingCoordDir txt = do
-    (x, y, d) <- case T.words txt of
-        [x, y, d] -> (,,) <$> readMaybeT x <*> readMaybeT y <*> readMaybeT d
-        _      -> Nothing
-    Just ((x, y), d)
-
-parseCommands :: Text -> Maybe [Cmd]
-parseCommands txt = toCmd `traverse` T.unpack txt
-    where
-        toCmd :: Char -> Maybe Cmd
-        toCmd 'L' = Just (Turn L)
-        toCmd 'R' = Just (Turn R)
-        toCmd 'M' = Just Move
-        toCmd _   = Nothing 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们添加了 Parsec 解析器:

```
+toCmd :: Char -> Maybe Cmd
+toCmd 'L' = Just (Turn L)
+toCmd 'R' = Just (Turn R)
+toCmd 'M' = Just Move
+toCmd _   = Nothing
+
+toDir :: Char -> Maybe Dir
+toDir c = readMaybe [c]
+
+dirParser = do
+    dir <- toDir <$> letter
+    maybe (parserFail "not a valid dir") pure dir
+
+cmdParser = do
+    cmd <- toCmd <$> letter
+    maybe (parserFail "not a valid cmd") pure cmd
+
+parser = do
+    min <- read <$> many1 digit
+    space
+    max <- read <$> many1 digit
+    _ <- newline
+    x <- read <$> many1 digit
+    space
+    y <- read <$> many1 digit
+    space
+    d <- dirParser
+    _ <- newline
+    cs <- many1 cmdParser
+    pure ((min, max), ((x, y), d), cs) 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们插入新的解析器:

```
+eitherToMaybe :: Either a b -> Maybe b
+eitherToMaybe (Right x) = Just x
+eitherToMaybe (Left _)  = Nothing 
 run :: Text -> Maybe CoordDir
 run txt = do
-    (max, coordDir, cmds) <- go $ T.lines txt +    (max, coordDir, cmds) <- eitherToMaybe $ parse parser "" txt
     Just $ runCommands cmds max coordDir
-    where
-        go [f, s, t] = (,,) <$> parsePlateauMax f <*> parseStartingCoordDir s <*> parseCommands t
-        go _         = Nothing 
```

Enter fullscreen mode Exit fullscreen mode

我们之前的解析器返回了一个`Maybe`，但是 Parsec 的`parse`返回了`Either`。因此，我们利用`eitherToMaybe`将新的解析器插入到旧的逻辑中。

由于`Either`的`Left`案例提供了一些很好的信息，以防解析失败，所以我们保留它:

```
-eitherToMaybe :: Either a b -> Maybe b
-eitherToMaybe (Right x) = Just x
-eitherToMaybe (Left _)  = Nothing
-
-run :: Text -> Maybe CoordDir +run :: Text -> Either ParseError CoordDir
 run txt = do
-    (max, coordDir, cmds) <- eitherToMaybe $ parse parser "" txt
-    Just $ runCommands cmds max coordDir +    (max, coordDir, cmds) <- parse parser "" txt
+    Right $ runCommands cmds max coordDir 
```

Enter fullscreen mode Exit fullscreen mode

## 有约束力

我们的部分代码负责在坐标到达网格的极端时对其进行包装:

```
move ((x, y), N) = (wrap max (x, y+1), N)
move ((x, y), E) = (wrap max (x+1, y), E)
move ((x, y), S) = (wrap max (x, y-1), S)
move ((x, y), W) = (wrap max (x-1, y), W)

wrap (maxX, maxY) (x, y) = (wrap' maxX x, wrap' maxY y)

wrap' max x | x < 0       = max + 1 + x
wrap' max x | x > max     = x `rem` max
wrap' max x | otherwise   = x 
```

Enter fullscreen mode Exit fullscreen mode

让我们抽象出那个逻辑。

我们首先用负责包装的`update`函数定义一个`BoundedInt`:

```
+data BoundedInt =
+    BoundedInt Int Int Int
+    deriving (Read, Show)
+
+update :: (Int -> Int) -> BoundedInt -> BoundedInt
+update g (BoundedInt min max val) =
+    BoundedInt min max $ wrap (g val)
+    where
+        wrap x | x < 0     = max + 1 + x `rem` max
+        wrap x | x > max   = x `rem` max
+        wrap x | otherwise = x 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们更新剩余的代码

```
 type Coord =
-    (Int, Int) +    (BoundedInt, BoundedInt) 
...

 parser = do
-    min <- read <$> many1 digit +    maxX <- read <$> many1 digit
     space
-    max <- read <$> many1 digit +    maxY <- read <$> many1 digit
     _ <- newline
     x <- read <$> many1 digit
     space
     y <- read <$> many1 digit
     space
     d <- dirParser
     _ <- newline
     cs <- many1 cmdParser
-    pure ((min, max), ((x, y), d), cs) +    pure (((BoundedInt 0 maxX x, BoundedInt 0 maxY y), d), cs) 
...

-        move ((x, y), N) = (wrap max (x, y+1), N)
-        move ((x, y), E) = (wrap max (x+1, y), E)
-        move ((x, y), S) = (wrap max (x, y-1), S)
-        move ((x, y), W) = (wrap max (x-1, y), W)
-
-        wrap (maxX, maxY) (x, y) = (wrap' maxX x, wrap' maxY y)
-
-        wrap' max x | x < 0       = max + 1 + x
-        wrap' max x | x > max     = x `rem` max
-        wrap' max x | otherwise   = x +        move ((x, y), N) = ((x, update (+ 1) y), N)
+        move ((x, y), E) = ((update (subtract 1) x, y), E)
+        move ((x, y), S) = ((x, update (subtract 1) y), S)
+        move ((x, y), W) = ((update (+ 1) x, y), W) 
```

Enter fullscreen mode Exit fullscreen mode

## 提取模块

现在应该很清楚我们的解决方案依赖于特定的类型:`CoordDir`、`Cmd`、`Turn`。是时候把它们放在自己的模块中了。

我们首先将`CoordDir`提升为数据类型:

```
-type CoordDir =
-    (Coord, Dir) +data CoordDir =
+    CoordDir Coord Dir
+    deriving (Show) 
...

 parser = do
     maxX <- read <$> many1 digit
     space
     maxY <- read <$> many1 digit
     _ <- newline
     x <- read <$> many1 digit
     space
     y <- read <$> many1 digit
     space
     d <- dirParser
     _ <- newline
     cs <- many1 cmdParser
-    pure (((BoundedInt 0 maxX x, BoundedInt 0 maxY y), d), cs) +    pure ((CoordDir (BoundedInt 0 maxX x, BoundedInt 0 maxY y) d), cs) 
...

 runCommands :: [Cmd] -> CoordDir -> CoordDir
 runCommands []       coordDir = coordDir
-runCommands (c:cmds) coordDir = runCommands cmds $ newCoordDir c +runCommands (c:cmds) (CoordDir coord dir) = runCommands cmds $ newCoordDir c
     where
-        newCoordDir (Turn L) = fmap csucc coordDir
-        newCoordDir (Turn R) = fmap cpred coordDir
-        newCoordDir Move     = move coordDir
-
-        move ((x, y), N) = ((x, update (+ 1) y), N)
-        move ((x, y), E) = ((update (subtract 1) x, y), E)
-        move ((x, y), S) = ((x, update (subtract 1) y), S)
-        move ((x, y), W) = ((update (+ 1) x, y), W) +        newCoordDir (Turn L) = CoordDir coord $ csucc dir
+        newCoordDir (Turn R) = CoordDir coord $ csucc dir
+        newCoordDir Move     = move coord dir
+
+        move (x, y) N = CoordDir (x, update (+ 1) y) N
+        move (x, y) E = CoordDir (update (subtract 1) x, y) E
+        move (x, y) S = CoordDir (x, update (subtract 1) y) S
+        move (x, y) W = CoordDir (update (+ 1) x, y) W 
```

Enter fullscreen mode Exit fullscreen mode

注意`move`函数是如何更新`Coord`元组的第一个或第二个元素的。让我们重构它，使用元组的双功能实例:

```
-        move (x, y) N = CoordDir (x, update (+ 1) y) N
-        move (x, y) E = CoordDir (update (subtract 1) x, y) E
-        move (x, y) S = CoordDir (x, update (subtract 1) y) S
-        move (x, y) W = CoordDir (update (+ 1) x, y) W +        move coord N = CoordDir (second (update (+ 1)) coord) N
+        move coord E = CoordDir (first (update (subtract 1)) coord) E
+        move coord S = CoordDir (second (update (subtract 1)) coord) S
+        move coord W = CoordDir (first (update (+ 1)) coord) W 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以提取模块:

```
-- Turn.hs

module Turn where

data Turn
    = L
    | R
    deriving (Show, Read)

-- Cmd.hs

module Cmd (Cmd(..), parser) where

import Turn (Turn(..))
import Text.Parsec (parserFail)
import Text.Parsec.Text (Parser)
import Text.Parsec.Char (letter)

data Cmd
    = Turn Turn
    | Move
    deriving (Show)

toCmd :: Char -> Maybe Cmd
toCmd 'L' = Just (Turn L)
toCmd 'R' = Just (Turn R)
toCmd 'M' = Just Move
toCmd _   = Nothing

parser :: Parser Cmd
parser = do
    cmd <- toCmd <$> letter
    maybe (parserFail "not a valid cmd") pure cmd

-- CoordDir.hs

{-# LANGUAGE DeriveAnyClass #-}

module CoordDir (CoordDir, parser, move, turn) where

import Data.Bifunctor (first, second)
import Text.Read (readMaybe)
import Text.Parsec (parserFail)
import Text.Parsec.Text (Parser)
import Text.Parsec.Char (space, digit, newline, letter)
import Text.Parsec.Combinator (many1)
import Turn (Turn(..))

class (Eq a, Enum a, Bounded a) => CyclicEnum a where
    cpred :: a -> a
    cpred d
        | d == minBound = maxBound
        | otherwise = pred d

    csucc :: a -> a
    csucc d
        | d == maxBound = minBound
        | otherwise = succ d

data BoundedInt =
    BoundedInt Int Int Int
    deriving (Read, Show)

type Coord =
    (BoundedInt, BoundedInt)

data Dir
    = N
    | E
    | S
    | W
    deriving (Show, Read, Eq, Enum, Bounded, CyclicEnum)

data CoordDir =
    CoordDir Coord Dir
    deriving (Show)

toDir :: Char -> Maybe Dir
toDir c = readMaybe [c]

dirParser :: Parser Dir
dirParser = do
    dir <- toDir <$> letter
    maybe (parserFail "not a valid dir") pure dir

parser :: Parser CoordDir
parser = do
    maxX <- read <$> many1 digit
    space
    maxY <- read <$> many1 digit
    _ <- newline
    x <- read <$> many1 digit
    space
    y <- read <$> many1 digit
    space
    d <- dirParser
    pure $ CoordDir (BoundedInt 0 maxX x, BoundedInt 0 maxY y) d

update :: (Int -> Int) -> BoundedInt -> BoundedInt
update g (BoundedInt min max val) =
    BoundedInt min max $ wrap (g val)
    where
        wrap x | x < 0     = max + 1 + x `rem` max
        wrap x | x > max   = x `rem` max
        wrap x | otherwise = x

move :: CoordDir -> CoordDir
move (CoordDir coord N) = CoordDir (second (update (+ 1)) coord) N
move (CoordDir coord E) = CoordDir (first (update (subtract 1)) coord) E
move (CoordDir coord S) = CoordDir (second (update (subtract 1)) coord) S
move (CoordDir coord W) = CoordDir (first (update (+ 1)) coord) W

turn :: Turn -> CoordDir -> CoordDir
turn L (CoordDir coord dir) = CoordDir coord $ cpred dir
turn R (CoordDir coord dir) = CoordDir coord $ csucc dir

-- Main.hs

module Main where

import Data.Text (Text)
import Text.Parsec (parse, ParseError)
import Text.Parsec.Char (newline)
import Text.Parsec.Combinator (many1)
import Text.Parsec.Text (Parser)
import Turn (Turn(..))
import CoordDir (CoordDir)
import qualified CoordDir (turn, move, parser)
import Cmd (Cmd(..))
import qualified Cmd (parser)

parser :: Parser (CoordDir, [Cmd])
parser = do
    coordDir <- CoordDir.parser
    _ <- newline
    cmds <- many1 Cmd.parser
    pure (coordDir, cmds)

runCommands :: CoordDir -> [Cmd] -> CoordDir
runCommands = foldl f
    where
        f coordDir (Turn turn) = CoordDir.turn turn coordDir
        f coordDir Move        = CoordDir.move coordDir

run :: Text -> Either ParseError CoordDir
run txt = do
    (coordDir, cmds) <- parse parser "" txt
    pure $ runCommands coordDir cmds

main :: IO ()
main = do
    let commands = "5 5\n1 2 N\nLMLMLMLMM"
    print $ (parse parser "" ("5 5\n1 2 N\nLM" :: Text) :: Either ParseError (CoordDir, [Cmd]))
    either print print $ run commands 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的一件重要事情是`CoordDir`内部并没有从`CoordDir.hs`中暴露出来。换句话说，`CoordDir`是一种不透明的数据类型。这意味着我们可以在不破坏其他模块的情况下，将`CoordDir`的表示重构为完全不同的类型。

## 收拾碗筷

我们可以重构`runCommands`来代替递归来折叠。事实上，我们可以从
开始

```
runCommands :: [Cmd] -> CoordDir -> CoordDir
runCommands []     coordDir = coordDir
runCommands (x:xs) coordDir = runCommands xs $ newCoordDir x
    where
        newCoordDir (Turn turn) = CoordDir.turn turn coordDir
        newCoordDir Move        = CoordDir.move coordDir 
```

Enter fullscreen mode Exit fullscreen mode

至

```
runCommands :: CoordDir -> [Cmd] -> CoordDir
runCommands = foldl f
    where
        f coordDir (Turn turn) = CoordDir.turn turn coordDir
        f coordDir Move        = CoordDir.move coordDir 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

在重构的过程中，我尝试了很多东西。一些想法悲惨地失败了。例如，我试图通过使用一个`data Bounded2D a b c = Bounded2D a b c`类型来使`BoundedInt`成为一个仿函数。不幸的是，为此实施一个明智的`fmap`被证明是不可能的。我还试图使用一个幻影类型，使不可能更新 x 坐标与 y 偏移，但不喜欢它的样子。

无论如何，我很确定我错过了一些很酷的数学抽象。所以如果你有任何想法，请在评论中告诉我！

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！