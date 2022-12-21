# Haskell 作为训练轮

> 原文：<https://dev.to/deciduously/haskell-as-training-wheels-1igl>

我不怎么写 Haskell。事实上，我并不真的写*任何* Haskell。我一生的总输出不到 1000 行。不过，每次我坐下来写 Haskell 时，我都会想起为什么我这么喜欢它。

函数式编程可能是一种棘手的范式，但我不认为它具有根本性的挑战。相反，这与你已经建立起来的本能相抵触。因此，当试图用更熟悉的语言进行函数式编程时，很容易作弊。JavaScript 就是一个很好的例子。现代 JS 对于函数式编程来说是一种很好的语言，但是可以说，没有什么能让你保持正轨。你可能正拄着强制性的拐杖，甚至没有意识到你正在这样做。当然，你可能到处都在使用`reduce`或像`flatMap`这样的新奇东西，但是语言本身并不关心你的代码听起来如何，它会做你要求的几乎任何事情，并且如果你为了让事情对自己更容易而违反规则，它也不会抱怨。

哈斯克尔强迫你呆在盒子里。它严苛的编译器意味着，除非你做对了，否则你的代码不会运行。起初这感觉像是一个限制，但是通过强迫你从功能上解决问题...良好的...强迫你从功能上解决问题。

促使这一点的是昨天的开发者挑战:

[![thepracticaldev](img/9beda539d204a28e09d7b574a6cb63a3.png)](/thepracticaldev) [## 每日挑战#8 -拼字计算器

### 开发人员至员工 7 月 5 日 192 分钟读取

#challenge](/thepracticaldev/daily-challenge-8-scrabble-word-calculator-41f6)

我昨天坐了很长时间的火车，觉得编码挑战是打发时间的好方法。在阅读了问题规范之后，我立刻有了如何解决这个问题的大纲。在过去的几周里，我花了大部分时间写 C++或者 Rust，所以我的思维大纲是非常必要的。您可以迭代地遍历字符串，根据出现的标记增加分数，然后根据任何额外的奖励调整分数。我相当肯定，这种本能的解决方案稍加按摩就会奏效。

但是，这有什么意思呢？

所以，当然，我决定拿出我那满是灰尘的旧 Haskell 编译器，看看我是否还知道如何驱动它。

我发现对“函数式编程”最有用的一句话总结是，不要告诉计算机如何计算结果，你只需告诉计算机结果*是什么*。这可能说起来容易做起来难，需要你重新框定你对问题的思考方式。

在这个特定的问题中，我们被给定一个字符串，并且必须根据拼字游戏规则返回该字符串的得分。有一些曲线球——`*`字符用于双倍或三倍得分，`^`表示空白，因此前一个字母不应得分，单词可以附加一个乘数，如`hello(d)`或`hello(t)`，以表示最终结果应该是两倍或三倍。

框定这一点的方法是思考拼字游戏分数实际上是什么。我们想要一个可以给任何单词打分的等式，而不是一次一个分数，比如说，一个接一个地添加字母，然后检查是否需要调整。这看起来像下面这样:

```
rawScore * wordMultiplier + sevenLetterBonus 
```

Enter fullscreen mode Exit fullscreen mode

这个等式适用于任何输入——我们可以只将乘数默认为 1，将奖金默认为 0，这样大多数不需要这些的单词就会得到`rawScore * 1 + 0`，这显然相当于`rawScore`。

所以，这就是答案*是*。我们只需要操作传入的字符串，这样当我们到达时，这些值中的每一个都被正确地填充了。最简单的部分就是`sevenLetterBonus`。如果生字正好是 7 个字母，我们加 50 分。我们的输入字符串可能有额外的位，如星号或乘数后缀，所以只需去掉这些就可以得到实际的单词:

```
stripMarkers :: String -> String
stripMarkers = filter (\c -> c /= '*' && c /= '^') $ takeWhile (/= '(') 
```

Enter fullscreen mode Exit fullscreen mode

很好。同样，这个函数只是描述了最终的结果——它是从最初的单词到一个`(`字符，标记字符被过滤掉了。相当具有宣示性。然后我们可以建立奖金:

```
sevenLetterBonus = if (length $ stripMarkers w) == 7 then 50 else 0 
```

Enter fullscreen mode Exit fullscreen mode

太好了，这现在可以在任何输入上工作了。乘数也很简单。一些输入会有一个后缀，如果有，检查哪个。如果不是，乘数为 1:

```
wordMultiplier =
    let
        suffix = dropWhile (/= '(') w
    in
        if length suffix > 0 then
            case suffix !! 1 of
                't' -> 3
                'd' -> 2
                _   -> 1
        else 1 
```

Enter fullscreen mode Exit fullscreen mode

它只查看字符串中一个`(`字符之后的任何部分，并相应地采取行动。这也已经处理了我们扔给它的任何字符串——大多数会击中那个`else`块，因为没有`(`出现并被赋予 1，这不会改变原始分数。

对我来说，功能上最棘手的部分是处理星号。我的直觉告诉我用迭代循环来解决这个问题，但是 Haskell 不会让我得逞的。如果是的话，我很可能会选择简单的方法。但是，当然，我不能。

给一列字母打分很简单——你只需用数字值替换每个字母，然后对列表求和:

```
sum $ map (\c -> scores ! c) $ word 
```

Enter fullscreen mode Exit fullscreen mode

映射函数只是在从字符到整型的映射中执行查找。为了实现这一点，我们需要一个字符串，其中只包含要评分的字母。为了让这个小片段能够处理任何输入，应该对输入进行预处理，使其只包含字母。我们想要省略的任何字母都可以，嗯，省略，要多次计数的字母就可以出现多次。我不知道我是否像一个哈斯凯勒那样干净优雅地处理了这个问题，但这确实有用

```
expandMarkers :: String -> String
expandMarkers [] = []
expandMarkers (c:[]) = [c]
expandMarkers (c:rest) =
    case head rest of
        '*' ->
            if (head $ tail rest) == '*' then
                [c] ++ [c] ++ [c] ++ (expandMarkers $ drop 2 rest) else
                [c] ++ [c] ++ (expandMarkers $ tail rest)
        '^' -> expandMarkers $ tail rest
        _ -> [c] ++ expandMarkers rest 
```

Enter fullscreen mode Exit fullscreen mode

它递归地使用字符串。在每个字母上，它向前看一个字母，然后根据找到的字母继续这个过程。星号将被移除，并替换为我们所在字母的副本，除非下一个的*也是星号，在这种情况下，它将替换这两个星号，而克拉将导致我们所在的字符不出现在结果中。这个函数将`he*ll^o**`转换为`heelooo`——准备好通过简单的字符到整数的替换进行评分。*

完整的代码只是把所有这些放在一起:

```
import Data.Map (Map, (!))
import qualified Data.Map as Map

scores :: Map Char Int
scores = Map.fromList pairs
    where
        pairs = [
            ('a', 1),
            ('b', 3),
            ('c', 3),
            ('d', 2),
            ('e', 1),
            ('f', 4),
            ('g', 2),
            ('h', 4),
            ('i', 1),
            ('j', 8),
            ('k', 5),
            ('l', 1),
            ('m', 3),
            ('n', 1),
            ('o', 1),
            ('p', 3),
            ('q', 10),
            ('r', 1),
            ('s', 1),
            ('t', 1),
            ('u', 1),
            ('v', 4),
            ('w', 4),
            ('x', 8),
            ('y', 4),
            ('z', 10)]

scoreWord :: String -> Int
scoreWord w =
    let
        sevenLetterBonus = if (length $ stripMarkers w) == 7 then 50 else 0
        wordMultiplier =
            let
                suffix = dropWhile (/= '(') w
            in
                if length suffix > 0 then
                    case suffix !! 1 of
                        't' -> 3
                        'd' -> 2
                        _ -> 1
                else 1
        -- 
        preparedWord = expandMarkers $ takeWhile (/= '(') w
        rawScore = sum $ map (\c -> scores ! c) $ preparedWord
    in
        rawScore * wordMultiplier + sevenLetterBonus

-- transform doubles, triples, carats
-- if we hit an asterisk, replace it with the previous letter
-- if we hit a carat, drop the previous letter
expandMarkers :: String -> String
expandMarkers [] = []
expandMarkers (c:[]) = [c]
expandMarkers (c:rest) =
    case head rest of
        '*' ->
            if (head $ tail rest) == '*' then
                [c] ++ [c] ++ [c] ++ (expandMarkers $ drop 2 rest) else
                [c] ++ [c] ++ (expandMarkers $ tail rest)
        '^' -> expandMarkers $ tail rest
        _ -> [c] ++ expandMarkers rest

-- remove suffix and all markers for deciding on the 7-letter bonus
stripMarkers :: String -> String
stripMarkers w = filter (\c -> c /= '*' && c /= '^') $ takeWhile (/= '(') w 
```

Enter fullscreen mode Exit fullscreen mode

这种解决方案将每一个输入预处理成可以很容易地以同样的方式评分的东西，与我在阅读规范时在脑海中编写的代码几乎没有相似之处。这很酷，我认为在任何语言中这都是一个不错的解决方案。

这就是哈斯克尔值得的原因。为了实现它，你不能依靠直觉。你必须用不同的方式来解决问题，直到它解决了才有效。我并不幻想这是一个好的、惯用的 Haskell，但是它确实是有用的 Haskell，这意味着我已经想出了一个有效的解决方案，现在我可以把它带到一个更熟悉的环境中并加以实现。如果我坐下来用 JavaScript 写这个，如果没有更多的思考和自律，我就不会到达同样的地方，因为我会先写下我是如何思考的，并剥夺自己以新的方式看待问题的体验。现在，下一次我处理类似的问题时，我的工具箱已经扩大了，我的第一直觉实际上可能更像这样。谢谢哈斯克尔。

照片由 Michal Vrba 在 Unsplash 上拍摄