# 用 Haskell 写一个 Tictactoe 游戏

> 原文：<https://dev.to/nt591/writing-a-tictactoe-game-in-haskell-545e>

* * *

代码可以在[这里](https://github.com/nt591/haskell-playground/blob/master/random/tictactoe.hs)找到

* * *

当我与 OCaml 模块系统的一些误解作斗争时，我决定暂时搁置我的解释器来尝试一些 Haskell。这是一种我一直关注的语言，也是我学习的目标。Haskell 的简洁符号(事实证明`sum . take 10 $ (*) <$> [2,4,6] <*> [1,2,3,4,5]`是完全合法的，尽管令人生畏的代码行)与新术语(cue monad 教程)的结合导致了一些延迟。但是在决定提交之后，以及 OCaml 对 FP 稍微友好一点的加入，我觉得我有能力弄脏自己的手。在看到为 Github 的[语义](https://github.com/github/semantic)项目编写的代码后，我感到特别受鼓舞。在某种程度上，我想做一些有价值的 Haskell 代码，但是没有比开始更好的开始了。

在阅读了大量的[Learn You A Haskell For Great Good](http://learnyouahaskell.com)和观看了一些 Youtube 视频之后，我对能够编写一个小型 Tictactoe CLI 游戏感到相当满意。我选择了[栈](https://docs.haskellstack.org/en/stable/README/)作为我的构建工具。

像 OCaml 一样，Haskell 的类型系统鼓励尽早进行领域建模。我决定让我的棋盘包含 9 个元素，每个元素可以是 X，O 或者空。由于 Haskell 没有空类型，我决定创建一个`Move`和一个`Cell`

```
import System.Environment
import Data.List

data Move = X | O
data Cell = Occupied Move | Empty 
```

我们使用`System.Environment`是因为我们需要一些 IO 行为，使用`Data.List`是为了一些未来的功能。

我本可以把`Cell`做成一个 Maybe 类型，但是选择了一种更具描述性的方式来表达一个单元格。这样，我可以跟踪游戏的移动，也可以看到单元格中有什么。我还需要一种方法来渲染这块板。因为我使用自定义类型，所以我需要创建 [Show typeclass](https://www.haskell.org/tutorial/stdclasses.html) 的实例。

```
instance Show Move where
  show X = "X"
  show O = "O"

instance Show Cell where
  show (Occupied X)     = "X"
  show (Occupied O)    = "O"
  show Empty            = " " 
```

我半肯定我可以在我的`Move`类型上使用`deriving (Show)`,但那将是为了以后的重构。今天的主要目标只是写代码。我的下一个计划是获得一些棋盘渲染代码。我需要一个函数，简单地把我的板，我的`[Cell]`输出一些漂亮的东西。

```
renderRow :: [Cell] -> String
renderRow row = intercalate " | " $ fmap show row

dividingLine :: String
dividingLine = "----------"

renderBoard :: [Cell] -> IO ()
renderBoard board = do
  putStrLn $ renderRow firstRow
  putStrLn dividingLine
  putStrLn $ renderRow secondRow
  putStrLn dividingLine
  putStrLn $ renderRow thirdRow
  where firstRow  = take 3 board
        secondRow = drop 3 . take 6 $ board
        thirdRow  = drop 6 board 
```

获取单元格列表并返回由管道连接的可读版本。只是一些列表切片来呈现不超过 3 行的 3 个元素。因为我正在写控制台，所以我需要返回一个`IO ()`， [IO 单子](http://learnyouahaskell.com/input-and-output)。不用太多赘述，I/O 被认为是一种副作用，因此 Haskell 强迫您将其包装在一个单子中。

如果我用一个空元素列表调用`renderBoard`，我会得到一个非常漂亮的

```
 |   |
----------
  |   |
----------
  |   | 
```

我的下一个目标是一些分配的想法。我需要能够接受一个`Move`和一个`[Cell]`并返回一个更新的电路板。这里有几个规则

1)所选单元格必须在界限内。
2)选择的单元格必须是自由的。

鉴于此，我决定简单地创建一个输入字符串映射来列出索引。好看吗？没有。但是在这种情况下它工作得很好。

```
getBoardIndex :: String -> Maybe Int
getBoardIndex "A1" = Just 0
getBoardIndex "A2" = Just 1
getBoardIndex "A3" = Just 2
getBoardIndex "B1" = Just 3
getBoardIndex "B2" = Just 4
getBoardIndex "B3" = Just 5
getBoardIndex "C1" = Just 6
getBoardIndex "C2" = Just 7
getBoardIndex "C3" = Just 8
getBoardIndex _    = Nothing 
```

Haskell 中的模式匹配比 OCaml 更简洁，因为我不需要 match 语句。我只是为每一种可能性创建函数，类似于 Elixir 的匹配。你还会看到我正在返回一个`Maybe Int` -我选择这个是因为我不仅关心一个 board 索引是否真实，还关心它是否是免费的。两个 if 语句，所以我可以使用一元绑定，或者`>>=`操作符。供参考:

```
 (>>=)            :: m a -> (a -> m b) -> m b 
```

这里说的是“给我一个关于某个`a`的单子，给我一个函数，把某个`a`变成一个关于`b`的单子，我会返回一个关于`b`的单子。如果我有一个来自`getBoardIndex`的`Maybe Int`,并且我的函数“该单元格可以自由分配吗”接受一个`Int`并返回一个`Maybe`,那么我可以使用这个绑定。

```
data CellTransform = Success [Cell] | Fail String [Cell]

verifyIsFree ::  [Cell] -> Int -> Maybe Int
verifyIsFree board ix = if board !! ix == Empty then Just ix else Nothing

assignCell :: String -> Move -> [Cell] -> CellTransform
assignCell location move board =
  case getBoardIndex location >>= verifyIsFree board of
    Nothing -> Fail "Invalid move" board
    Just i -> Success ((take i board) ++ [Occupied move] ++ (drop (i+1) board)) 
```

你会看到这种新的`CellTransform`类型。我添加了一个新类型，只是为了携带错误信息和一个未修改的板，如果板被占用的话。所以我的`verifyIsFree`获取一个板子和索引，如果板子在那个元素上是空闲的，那么返回一个可能的索引，否则不返回任何东西。因为我正在对一个自定义数据类型进行相等性检查，所以我需要确保`Cell`也是 [Eq typeclass](http://hackage.haskell.org/package/base-4.12.0.0/docs/Data-Eq.html)
的一个实例

```
instance Eq Cell where
  Occupied X == Occupied X = True
  Occupied O == Occupied O = True
  Empty == Empty           = True
  _ == _                   = False 
```

这只是为一个`Cell`的所有可能状态设置了我的等式操作符。

最后，我真正的游戏。我需要我的游戏来

1)要求输入
2)尝试分配单元格
3a)如果单元格无效，告诉用户并让他们重新选择
3b)如果单元格有效，检查赢家
4a)如果有赢家，提醒他们并结束游戏
4b)如果没有赢家，移交给下一个玩家

让我们把这个编码出来

```
playRound :: Move  -> [Cell] -> IO ()
playRound move board = do
  putStrLn $ (show move) ++ " 's turn."
  putStrLn $ "Pick a cell from A1 to C3."
  renderBoard board
  putStr "\nInput: "
  cell <- getLine
  case assignCell cell move board of
    Fail err board -> do
      putStrLn err
      playRound move board
    Success newBoard -> do
      if isThereAWinner move newBoard then do
        putStrLn $ ("Winner! " ++ (show move) ++ " has won!")
        renderBoard newBoard
        return ()
      else playRound (nextMove move) newBoard 
```

因为我们再次使用 I/O，我们需要返回一个 IO 单子。这也给了我们一些稍微强制性阅读代码的好处。

你会看到一些假函数- `isThereAWinner`和`nextMove move`。我们可以把这些编码出来。

```
nextMove :: Move -> Move
nextMove X = O
nextMove O = X

isThereAWinner :: Move -> [Cell] -> Bool
isThereAWinner move board =
  or [
    -- check top row
    board !! 0 == (Occupied move) && board !! 1 == (Occupied move) && board !! 2 == (Occupied move),
    -- check middle row
    board !! 3 == (Occupied move) && board !! 4 == (Occupied move) && board !! 5 == (Occupied move),
    -- check bottom row
    board !! 6 == (Occupied move) && board !! 7 == (Occupied move) && board !! 8 == (Occupied move),
    -- check left column
    board !! 0 == (Occupied move) && board !! 3 == (Occupied move) && board !! 6 == (Occupied move),
    -- check middle column
    board !! 1 == (Occupied move) && board !! 4 == (Occupied move) && board !! 7 == (Occupied move),
    -- check right column
    board !! 2 == (Occupied move) && board !! 5 == (Occupied move) && board !! 8 == (Occupied move),
    -- check top left -> bottom right
    board !! 0 == (Occupied move) && board !! 4 == (Occupied move) && board !! 8 == (Occupied move),
    -- check bottom left -> top right
    board !! 6 == (Occupied move) && board !! 4 == (Occupied move) && board !! 2 == (Occupied move)
  ] 
```

这是我最不喜欢的函数。它可以通过评论来阅读，但绝对不会令人愉快。我无法想象把这个改成 5x5 的井字棋盘什么的。但是一旦我们有了这个，我们就可以创建一个`main`函数。

```
main :: IO ()
main = do
  putStrLn $ "The game is beginning."
  let newBoard = replicate 9 Empty
  playRound X newBoard 
```

而且我们可以用`stack ghc tictactoe.hs`造我们的`tictactoe.hs`，跑`./tictactoe`去玩！

这是一个有趣的实验。我试图避免过多地钻研一元操作符、状态一元或高级 Haskell 技术。我主要关注的是键入代码，并试着熟悉语法。这个编译器非常有用，但是不像 Elm 的编译器那样显式。因为我的职业目标是得到一份编写后端 ML 系列代码(Scala、OCaml、Haskell 等)的工作，所以我会继续练习。我喜欢任何项目的想法。我可能会尝试为一个更大、更丰富的项目编写一个 Lisp 解释器。