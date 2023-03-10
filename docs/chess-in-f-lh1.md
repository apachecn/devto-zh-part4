# F#中的象棋

> 原文：<https://dev.to/simonreynolds/chess-in-f-lh1>

最近，我决定实现一个象棋引擎，作为探索在 F#中构建规则引擎的一种方式

GitHub 链接:[https://github.com/simon-reynolds/FsChess](https://github.com/simon-reynolds/FsChess)

F#是我选择的语言，因为它有很多优点。例如，由于有区别的联合，定义片段变得简单多了。

```
type Colour = | White | Black

type Rank =
    | Pawn
    | Rook
    | Knight
    | Bishop
    | Queen
    | King

type Piece = { Player : Colour; Rank : Rank } 
```

Enter fullscreen mode Exit fullscreen mode

有时候，想弄清楚如何在程序中概念化一个真实世界的对象是一件很困难的事情。我最初尝试将棋盘定义为一个二维数组，反映了现实生活中的结构。

但是这导致了试图将数组索引映射到预期的普通文件符号的问题。例如，国际象棋棋盘的左下方格是 A，1。需要不断地将数组索引`[0,0]`来回转换为预期的 A，1，这是一个额外的概念性开销。

相反，我们可以定义`Row`和`Column`类型，并简单地将`Square`表示为它们的元组...

```
type Column = | A | B | C | D | E | F | G | H
type Row = | One | Two | Three | Four | Five | Six | Seven | Eight

type Square = (Column * Row) 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们可以很容易地将一个`Board`定义为一个`Map`类型，列出棋盘上的方格和可能占据它们的`Piece` s，而不是摆弄数组索引

```
type Board = Map<Square, Piece option> 
```

Enter fullscreen mode Exit fullscreen mode

突然，查询棋盘以查看特定方格上的棋子变得容易多了。我们不必映射索引，`Square`受到`Column`和`Row`的值的约束。这意味着它不能包含非法值，因此不可能有一个`IndexOutOfRangeException`。

我们可以使用 F#的另一个优势，模式匹配，以安全的方式查询棋盘，并向任何`NullReferenceException`挥手告别。

```
let square = (A, One)
let maybePiece = board.[square]

let message =
    match maybePiece with
    | None -> "No piece here"
    | Some piece -> sprintf "There's a %A %A here" piece.Player piece.Rank 
```

Enter fullscreen mode Exit fullscreen mode

在游戏开始时，上面的`message`会是

> 这里有一只白车

## 运动规则

下一步是定义描述棋子如何移动的规则。如果你从未玩过国际象棋，运动规则是:

卒只能向前移动一格，除非他们必须斜着向前移动来捕捉。第一步他们可以移动两格。一个棋子有两个特殊的移动方式，*提升*和*顺便说一下*，当我实现它们时，我会在另一篇博文中描述。

一辆车可以直线前进任意数量的空地。这涉及到国王可以采取的一项名为“阉割”的行动。

一个骑士可以移动成 L 形，水平两格垂直一格，或者水平一格垂直两格。它是唯一一个可以跳过阻挡它的其他棋子的棋子。

一个主教可以在任意方向的对角线上移动任意数量的空间。

王后结合了车和象的移动规则，可以直线或对角线移动任意数量的方格。

国王可以向任何方向移动一格，除了在*阉割*移动期间，这将与棋子的特殊移动一起描述。

现在，我们需要将所有这些翻译成代码...

* * *

### 代码中的运动规则

当我们指定一个移动时，我们要做的第一件事是检查移动是否有效。

一个移动可能无效的原因有很多，我们需要一种方法来表示这一点。F# 4.1 推出了`Result<'T,'TError>`型，这将做得很好。

一个`Result`要么是`'T`的一个`Ok`要么是`'TError`的一个`Error`，在面向铁路的编程中，我们可以用它来链接结果。Scott Wlaschin 的精彩网站 [F#提供了关于这个概念的精彩介绍，以获取乐趣和利润](https://fsharpforfunandprofit.com/rop/)

与其重复所有代码，不如在 GitHub repo 上找到这个项目的实现，[https://github.com/simon-reynolds/FsChess](https://github.com/simon-reynolds/FsChess)

我们可以使用一个自定义的`ResultBuilder`，这样我们可以一次检查多个标准，并在第一次失败时尽早返回。这允许我们将调用链接在一起，这样`validateMove`函数的最终结果就像

```
let validateMove (gameState : GameState) (move : ProposedMove) =

        result {
            return!
                // Check we have a piece
                validatePieceSelected gameState.Board move
                // Check the piece is our own
                >>= validatePieceIsGood gameState.CurrentPlayer
                // Check we're not trying to capture one of our pieces
                >>= validateNoFriendlyFire gameState.Board gameState.CurrentPlayer
                // Check the piece is allowed move to the square
                >>= validateMoveForPiece gameState.Board gameState.CurrentPlayer
                // Check nothing is blocking it's path
                >>= validateNoCollision gameState.Board
                // If we've gotten this far then the move is valid, mark it as such
                >>= markMoveAsValidated gameState.Board
        } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 还在做什么...

任何好的国际象棋引擎都将支持全方位的移动，目前我仍然必须决定如何实现*推广*、*顺道*和*阉割。*

这个项目的另一个长期目标是提供一个可以对抗的人工智能和各种可以使用的前端，从控制台到网络和电话应用程序的一切。

期待他们的后续职位很快。