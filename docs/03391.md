# 类型类的乐趣

> 原文：<https://dev.to/riccardoodone/fun-with-typeclasses-ap7>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-09-09-fun-with-typeclasses.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

我最近开始读[《深度哈斯克尔》](https://www.manning.com/books/haskell-in-depth)。这本书的第一部分涵盖了这门语言的基础知识。在这里，我发现了一个有趣的例子，它使用了两个现有的类型类(即`Enum`、`Bounded`)和两个似乎很有意义的新类型类(即`BoundedEnum`、`CyclicEnum`)。

让我们看看这些东西的作用。首先，我们需要一个类型来玩:

```
data Direction
    = North
    | East
    | South
    | West
    deriving (Show, Enum, Bounded, BoundedEnum, Eq, CyclicEnum) 
```

Enter fullscreen mode Exit fullscreen mode

`Enum`的实例使我们能够枚举类型`Direction`的值。换句话说，我们可以通过使用`succ`和`pred`移动到下一个值或上一个值。不仅如此，通过使`Direction`可枚举，我们可以使用`..`操作符:

```
main :: IO ()
main = do
  print $ succ North
  -- East

  print $ pred East
  -- North

  print [North ..]
  -- [North,East,South,West]

  print [East ..]
  -- [East,South,West]

  print [East .. South]
  -- [East,South] 
```

Enter fullscreen mode Exit fullscreen mode

实例`Bounded`允许我们一般地调用下限和上限`minBound`和`maxBound` :

```
main :: IO ()
main = do
  let
      allDirections :: [Direction]
      allDirections = [minBound .. maxBound]
  print allDirections
  -- [North,East,South,West] 
```

Enter fullscreen mode Exit fullscreen mode

现在，在 Haskell 中,`Enum`可能不是`Bounded`,反之亦然。如果一个类型既是类型又是类型，那么定义一个`BoundedEnum`类型类和实例是有意义的。这样，我们可以抽象出范围`[minBound .. maxBound]` :

```
class (Enum a, Bounded a) => BoundedEnum a where
    range :: [a]
    range = enumFrom minBound

main :: IO ()
main = do
  let
      allDirections' :: [Direction]
      allDirections' = range
  print $ allDirections'
  -- [North,East,South,West] 
```

Enter fullscreen mode Exit fullscreen mode

但是仍然有一个问题。事实上，下面的代码引发了一个运行时异常:

```
main :: IO ()
main = do
  print $ pred North
  -- "tried to take `pred' of first tag in enumeration" 
```

Enter fullscreen mode Exit fullscreen mode

事实上，枚举并不绕回，并且`North`是枚举中的第一个值！我们可以通过定义一个`CyclicEnum`类型类和实例来解决这个问题:

```
class (Eq a, Enum a, Bounded a) => CyclicEnum a where
    cpred :: a -> a
    cpred d
        | d == minBound = maxBound
        | otherwise = pred d

    csucc :: a -> a
    csucc d
        | d == maxBound = minBound
        | otherwise = succ d

main :: IO ()
main = do
  print $ cpred North
  -- West 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！