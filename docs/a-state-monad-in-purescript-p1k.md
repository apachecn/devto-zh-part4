# PureScript 中的状态单子

> 原文：<https://dev.to/riccardoodone/a-state-monad-in-purescript-p1k>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-06-17-a-state-monad-in-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

函数式程序员喜欢纯函数。不幸的是，有些东西天生就是有状态的。然而，这并不意味着我们不能用纯函数式语言来处理它们。这只是意味着它们更自然地使用可变状态建模。或者至少，这是面向对象代码的背景能让我们想到的。

容易用可变状态建模的一个东西是堆栈。这是维基百科对它的描述

> 在计算机科学中，堆栈是作为元素集合的抽象数据类型，有两个主要操作:
> 
> *   push，它向集合中添加一个元素，而
> *   pop，删除最近添加但尚未删除的元素。

这是它在代码中的样子:

```
type Stack = List Int

push :: Int -> Stack -> Stack
push x st = x : st

pop :: Stack -> Tuple (Maybe Int) Stack
pop xs = Tuple (head xs) (drop 1 xs)

main :: Effect Unit
main = do
  let stack           = 3 : 2 : 1 : Nil
      stack4          = push 4 stack
      Tuple m4 stack3 = pop stack4
      Tuple m3 stack2 = pop stack3
  logShow stack
  -- (3 : 2 : 1 : Nil)
  logShow stack4
  -- (4 : 3 : 2 : 1 : Nil)
  logShow m4
  -- (Just 4)
  logShow stack3
  -- (3 : 2 : 1 : Nil)
  logShow m3
  -- (Just 3)
  logShow stack2
  -- (2 : 1 : Nil) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们重写`push`，把它和`pop`
放在一起

```
push :: Int -> Stack -> Tuple Unit Stack
push x st = Tuple unit (x : st)

pop :: Stack -> Tuple (Maybe Int) Stack
pop xs = Tuple (head xs) (drop 1 xs) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以注意到一个对称性。特别是，我们可以把`push`和`pop`想象成产生一组结果的操作:一个中间值和堆栈的新状态。

当`pop` ping 时，中间值是弹出值，堆栈的新状态是没有弹出值的旧堆栈。

当`push` ing 时，中间值什么都不是(我们使用`unit`来建模)，并且堆栈的新状态是添加了推入值的旧堆栈。

这种产生中间值和新状态的函数的直觉被状态单子捕获。来实现一个吧！

## 实现 Stat3 单子

在这一节中，我们将实现一个状态单子。我们会用`4` s 和`3` s 来代替名字中的`a` s 和`e` s(比如`Stat3` vs `State`)。因为我们将使用与来自[控件的`State`单子相同的 api。Monad.State](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.State#t:State) ，这将避免名称冲突。

我们从定义`Stat3` :
开始

```
newtype Stat3 s v = Stat3 (s -> Tuple v s) 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，一个从当前状态到中间值和新状态的元组的函数包装器。我们使用了两个类型变量来控制状态的类型(`s`)和中间值的类型(`v`)。

那看起来真的很像`pop` ( `Stack -> Tuple (Maybe Int) Stack`)和`push` ( `... -> Stack -> Tuple Unit Stack`)的类型。

然后我们定义一个函数来“展开”`s -> Tuple v s`，并以初始状态`s` :
运行它

```
runStat3 :: forall s v. Stat3 s v -> s -> Tuple v s
runStat3 (Stat3 g) s = g s 
```

Enter fullscreen mode Exit fullscreen mode

为了能够编写声明性代码并匹配 [`State`](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.State#t:State) 的 api，我们实现了所有需要的类型类，直到`Monad`。

特别是，我们在`Stat3 s`而不是`Stat3`上实现 typeclasses。那是因为他们处理的是`* -> *`类型的构造函数，而不是`* -> * -> *`。

```
instance functorStat3 :: Functor (Stat3 s) where
    -- map :: forall a b. (a -> b) -> f a -> f b
    map g f = Stat3 (\s -> let Tuple v s' = runStat3 f s in Tuple (g v) s')

instance applyStat3 :: Functor (Stat3 s) => Apply (Stat3 s) where
    -- apply :: forall a b. f (a -> b) -> f a -> f b
    apply fg f = Stat3 (\s -> let Tuple g s'  = runStat3 fg s
                                  Tuple v s'' = runStat3 f s' in Tuple (g v) s'')

instance applicativeStat3 :: Apply (Stat3 s) => Applicative (Stat3 s) where
    -- pure :: forall a. a -> f a
    pure v = Stat3 (\s -> Tuple v s)

instance bindStat3 :: Apply (Stat3 s) => Bind (Stat3 s) where
    -- bind :: forall a b. m a -> (a -> m b) -> m b
    bind m g = Stat3 (\s -> let Tuple v s' = runStat3 m s in runStat3 (g v) s') 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以写

```
pushStat3 :: Int -> Stat3 (List Int) Unit
pushStat3 x = Stat3 (\s -> Tuple unit (x : s))

popStat3 :: Stat3 (List Int) (Maybe Int)
popStat3 = Stat3 (\s -> Tuple (head s) (drop 1 s))

m4nip :: Stat3 (List Int) Unit
m4nip = do
    pushStat3 4
    _ <- popStat3
    _ <- popStat3
    pure unit

main :: Effect Unit
main = do
  logShow $ runStat3 m4nip (3 : 2 : 1 : Nil)
  -- (Tuple unit (2 : 1 : Nil)) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以对`pushStat3`和`popStat3`进行如下改进:

```
g3t :: forall s. Stat3 s s
g3t = Stat3 (\s -> Tuple s s)

m0dify_ :: forall s. (s -> s) -> Stat3 s Unit
m0dify_ g = do
    s <- g3t
    Stat3 (\s -> Tuple unit (g s))

popStat3 :: Stat3 (List Int) (Maybe Int)
popStat3 = do
    xs <- g3t
    m0dify_ $ drop 1
    pure $ head xs

pushStat3 :: Int -> Stat3 (List Int) Unit
pushStat3 x = m0dify_ (\s -> x : s) 
```

Enter fullscreen mode Exit fullscreen mode

## 使用实态单子

使用 [`State`单子](https://pursuit.purescript.org/packages/purescript-transformers/4.2.0/docs/Control.Monad.State#t:State)就像用`e` s 替换`3` s，用`a` s:
替换`4` s 一样简单

```
pushState :: Int -> State (List Int) Unit
pushState x = modify_ (\s -> x : s)

popState :: State (List Int) (Maybe Int)
popState = do
    xs <- get
    modify_ $ drop 1
    pure $ head xs

manip :: State (List Int) Unit
manip = do
    pushState 4
    _ <- popState
    _ <- popState
    pure unit

main :: Effect Unit
main = do
  logShow $ runState manip (3 : 2 : 1 : Nil)
  -- (Tuple unit (2 : 1 : Nil)) 
```

Enter fullscreen mode Exit fullscreen mode

## 整码

```
module Main where

import Prelude
import Effect (Effect)
import Effect.Console (logShow)
import Data.List
import Data.Tuple
import Data.Maybe
import Control.Monad.State

type Stack = List Int

push :: Int -> Stack -> Stack
push x st = x : st

pop :: Stack -> Tuple (Maybe Int) Stack
pop xs = Tuple (head xs) (drop 1 xs)

newtype Stat3 s v = Stat3 (s -> Tuple v s)

runStat3 :: forall s v. Stat3 s v -> s -> Tuple v s
runStat3 (Stat3 g) s = g s

instance functorStat3 :: Functor (Stat3 s) where
    -- map :: forall a b. (a -> b) -> f a -> f b
    map g f = Stat3 (\s -> let Tuple v s' = runStat3 f s in Tuple (g v) s')

instance applyStat3 :: Functor (Stat3 s) => Apply (Stat3 s) where
    -- apply :: forall a b. f (a -> b) -> f a -> f b
    apply fg f = Stat3 (\s -> let Tuple g s'  = runStat3 fg s
                                  Tuple v s'' = runStat3 f s' in Tuple (g v) s'')

instance applicativeStat3 :: Apply (Stat3 s) => Applicative (Stat3 s) where
    -- pure :: forall a. a -> f a
    pure v = Stat3 (\s -> Tuple v s)

instance bindStat3 :: Apply (Stat3 s) => Bind (Stat3 s) where
    -- bind :: forall a b. m a -> (a -> m b) -> m b
    bind m g = Stat3 (\s -> let Tuple v s' = runStat3 m s in runStat3 (g v) s')

pushSt4t3 :: Int -> Stat3 (List Int) Unit
pushSt4t3 x = Stat3 (\s -> Tuple unit (x : s))

popSt4t3 :: Stat3 (List Int) (Maybe Int)
popSt4t3 = Stat3 (\s -> Tuple (head s) (drop 1 s))

g3t :: forall s. Stat3 s s
g3t = Stat3 (\s -> Tuple s s)

m0dify_ :: forall s. (s -> s) -> Stat3 s Unit
m0dify_ g = do
    s <- g3t
    Stat3 (\s -> Tuple unit (g s))

popStat3 :: Stat3 (List Int) (Maybe Int)
popStat3 = do
    xs <- g3t
    m0dify_ $ drop 1
    pure $ head xs

pushStat3 :: Int -> Stat3 (List Int) Unit
pushStat3 x = m0dify_ (\s -> x : s)

m4nip :: Stat3 (List Int) Unit
m4nip = do
    pushStat3 4
    _ <- popStat3
    _ <- popStat3
    pure unit

pushState :: Int -> State (List Int) Unit
pushState x = modify_ (\s -> x : s)

popState :: State (List Int) (Maybe Int)
popState = do
    xs <- get
    modify_ $ drop 1
    pure $ head xs

manip :: State (List Int) Unit
manip = do
    pushState 4
    _ <- popState
    _ <- popState
    pure unit

main :: Effect Unit
main = do
  let stack           = 3 : 2 : 1 : Nil
      stack4          = push 4 stack
      Tuple m4 stack3 = pop stack4
      Tuple m3 stack2 = pop stack3
    logShow stack
  -- (3 : 2 : 1 : Nil)
  logShow stack4
  -- (4 : 3 : 2 : 1 : Nil)
  logShow m4
  -- (Just 4)
  logShow stack3
  -- (3 : 2 : 1 : Nil)
  logShow m3
  -- (Just 3)
  logShow stack2
  -- (2 : 1 : Nil)
  logShow $ runStat3 manip (3 : 2 : 1 : Nil)
  -- (2 : 1 : Nil)
  logShow $ runState manip' (3 : 2 : 1 : Nil)
  -- (2 : 1 : Nil) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！