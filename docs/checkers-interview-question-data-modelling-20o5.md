# Checkers 面试问题:数据建模

> 原文：<https://dev.to/mortoray/checkers-interview-question-data-modelling-20o5>

我的一个标准[面试问题](https://iio.sh/r/Yxu7)是写一个简化的跳棋游戏。我专注于一些基本操作所需的数据建模。很多考生对这个问题没有准备，失败率比我预想的高很多。我把需求建立在真实世界的活动上，因此没有理由你不能完成它。

让我带你一步一步地回答我在面试中提出的问题。我会提到一些我看到的求职者普遍存在的问题，以及他们在面试中犯的错误。通过跟随获得一些额外的编码实践。

# 向棋盘添加棋子

我先简单介绍一下我在寻找什么。这个问题的第一部分着重于数据建模:我们将如何表示[跳棋游戏](https://en.wikipedia.org/wiki/Draughts)及其棋子。我不是在寻找一个完整的程序，很少关心小语法错误。我将只从视觉上回顾它。

*   这个游戏是在一个 8x8 的格子上玩的
*   每个单元格可以是空的，或者包含一个红色或黑色的块

有鉴于此，我要求受访者创建一个功能来放置作品。

*   创建一个函数，该函数获取棋子列表及其位置，并将它们添加到棋盘上。例如，4.5 处的*红色，或 3.7*处的*黑色。*
*   将一块放在现有的上面是错误的
*   将棋子放在棋盘外面是错误的
*   如果有任何错误，该功能将失败，电路板保持不变

## 板件模型

我一下子就说出了所有这些要点，但困难是从第一点开始的。我希望候选人为棋盘和棋子创建一个合适的数据模型。最合理的解决方案是使用矩阵或多维数组，这取决于您的首选语言支持什么。没有理由不知道这些数据结构。

件代表是个人分歧的地方。首选方法是枚举或另一种有限的数据类型。每当你有有限的选择时，清楚地表达这些选择是最干净的。这里你的枚举将有`Empty`、`Red`和`Black`值。枚举表示每个单元格的状态。

另一个有效的方法是只使用一个`Red`和`Black`枚举，每个板单元有一个可选的类型。枚举本身只针对棋子，单元格状态是复合类型。

然而，我得到的最常见的方法是使用整数值。每个单元的状态由一个整数表示。这种方法有两个明显的问题:

*   电路板可能损坏，因为一个单元可能有一个无意义的数字
*   代码很难读懂，因为这些数字没有任何意义

稳定编程的一个目标是永远不要有无效状态。选择阻止损坏的数据类型会有所帮助。可读性也很重要。通过枚举，你可以在整个代码中使用干净的名字，比如`Empty`。对于整数，我看到了很多[、](https://mortoray.com/2019/02/25/stop-waving-the-wand-of-magic-numbers/)和`0`或`1`的神奇对比。

一些个体使用整数并定义常数，本质上是一种没有特殊语法的枚举。比普通整数好，但是为什么不用枚举呢？如果你的语言没有任何内置的枚举语法，你就别无选择。

一些候选人很难完成这部分。大约一半的候选人需要帮助来代表董事会和棋子。注意，如果他们选择了整数方法，我不会阻止他们，而是稍后指出它的问题。

## 错误检查

纠结于建模的候选人往往会忘记错误检查。大约一半需要一些提示来添加检查。

错误检查的要求很简单，一目了然。不要将棋子放在现有的棋子上，也不要将棋子放在棋盘之外。这给了候选人一个很好的机会来展示他们清晰的编码能力。

看看我得到的这个典型片段。

```
def place_pieces( self, places)
    for place in places:
        if place.position.x < 0 or place.position.x > 7 or place.position.y < 0 or place.position.y > 7:
            raise ValueError( "location not on board" )
        if board[place.position.x][place.position.y] != 0:
            raise ValueError( "position already occupied" )

        board[place.position.x][place.position.y] = place.piece 
```

Enter fullscreen mode Exit fullscreen mode

注意`!= 0`代表使用整数的模型，这只是代码的问题之一。代码有一些神奇的数字，一眼看不出它在做什么。干净的解决方案是为第一部分创建一个助手函数，并为第二部分使用一个枚举。一些候选人在第一次通过时就这样做了。

```
def position_in_board(self, position ):
    return position.x >= 0 and position.x < self.width and
        position.y >= 0 and position.y < self.height

def get_cell(self, position):
    return self.board[position.x][position.y]

def place_pieces( self, places)
    for place in places:
        if not position_in_board( place.position ):
            raise ValueError( "location not on board" )
        if board.get_cell( place.position ) != CellStatus.Empty:
            raise ValueError( "position already occupied" ) 
```

Enter fullscreen mode Exit fullscreen mode

这个读起来好多了。

面试中的努力值得吗？是的，绝对的。如果你习惯于用这种方式写代码，这不应该花费任何额外的时间。这个代码清楚地与面试官交流。我不必问条件逻辑的问题，名字就说明了一切。用这种方式编码的候选人总体表现更好，让面试官的评价更有趣。

## 总误差检查

几乎所有候选人都忽略了这样一个要求，即如果任何项目无效，就不要更改任何内容。例如，如果第三块位于无效位置，则不应修改棋盘。然而，如果你做了上面的典型循环，你已经在这一点上改变了董事会。

当被指出时，大多数候选人会创建第二个循环来首先检查错误。然而，第二个循环完全不起作用。如果输入列表在同一位置包含多个片段，初始循环不会检测到错误。直到你把碎片放好，你才会注意到复制品。

一个好的解决方案是使用克隆板。坚持简单的初始循环，如果一切正常，就把电路板换出来。这是一种常见的方法。

我不会否定任何不理解这个问题的人。这是日常编码中出现的东西，它代表了一种良好的实践，但是如果没有人审查代码，它很容易被忽略。然而，它确实给有经验的候选人一个获得加分的机会。否则我就把它当作一个教学机会。

# 移动一块

在第二部分中，我们将在棋盘上移动一个棋子。