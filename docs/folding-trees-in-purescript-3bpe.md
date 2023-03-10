# 在 PureScript 中折叠树

> 原文：<https://dev.to/riccardoodone/folding-trees-in-purescript-3bpe>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-06-10-folding-trees-in-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

假设我们想要在树上执行两个操作:

*   数一数叶子的数量
*   将其转换为列表

在本帖中，我们将采用三种不同的策略来实现这两个目标:

*   递归函数
*   使用可折叠的 typeclass
*   使用状态单子

## 树型

```
data Tree a
    = Leaf a
    | Node (Tree a) (Tree a)

instance showTree :: Show a => Show (Tree a) where
    show (Leaf x)   = "(Leaf " <> show x <> ")"
    show (Node l r) = "(Node " <> show l <> " " <> show r <> ")"

exampleTree :: Tree Char
exampleTree =
    Node
      (Node (Leaf 'a') (Leaf 'b'))
      (Leaf 'c')

main :: Effect Unit
main = do
  logShow exampleTree
  -- (Node (Node (Leaf 'a') (Leaf 'b')) (Leaf 'c')) 
```

Enter fullscreen mode Exit fullscreen mode

## 递归函数

PureScript 是一种纯函数式编程语言，而`Tree a`是一种递归类型:递归函数是一种完美的选择。

```
countTreeRec :: forall a. Tree a -> Int
countTreeRec tree =
    go 0 tree
    where
          go i (Leaf _)   = i + 1
          go i (Node l r) = go i l + go i r

toListRec :: forall a. Tree a -> List a
toListRec tree =
    go Nil tree
    where
          go xs (Leaf x)   = xs <> Cons x Nil
          go xs (Node l r) = go xs l <> go xs r

main :: Effect Unit
main = do
  logShow $ countTreeRec exampleTree
  -- 3
  logShow $ toListRec exampleTree
  -- ('a' : 'b' : 'c' : Nil) 
```

Enter fullscreen mode Exit fullscreen mode

这些函数做它们应该做的事情。但是，他们的外形真的很像。`countTreeRec`和`toListRec`的唯一区别是:

*   传递给`go`函数的初始值(即`0`对`Nil`)
*   `go`(即`i + 1`对`xs <> Cons x Nil`)基础情况下的计算
*   递归案例组合递归调用结果的方式(即`+`对`<>`)

上面描述的正是 Foldable typeclass 所捕获的内容。让我们看看这在代码中是怎样的。

## 使用可折叠的 Typeclass

Foldable typeclass 抓住了将一个结构“折叠”成另一个结构的想法。

```
instance foldableTree :: Foldable Tree where
    -- foldMap :: forall a m. Monoid m => (a -> m) -> f a -> m
    foldMap g (Leaf x)   = g x
    foldMap g (Node l r) = foldMap g l <> foldMap g r

    foldr g = foldrDefault g
    foldl g = foldlDefault g

countTreeFold :: forall a. Tree a -> Int
countTreeFold tree =
    count
    where Additive count = foldMap (\_ -> Additive 1) tree

toListFold :: forall a. Tree a -> List a
toListFold tree =
    foldMap (\x -> Cons x Nil) tree

main :: Effect Unit
main = do
  logShow $ countTreeFold exampleTree
  -- 3
  logShow $ toListFold exampleTree
  -- ('a' : 'b' : 'c' : Nil) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们可以使用`foldr`或`foldl`来获得相同的结果。但是`foldMap`更优雅一点。它的工作方式很简单:

*   它首先通过传递给它的函数运行树的每个元素(即`(\_ -> Additive 1)`对`(\x -> Cons x Nil)`)。该函数必须将树的每个元素转换成幺半群
*   它使用`<>`操作符组合树的所有幺半群。由于`<>`被实现为`Additive`的`+`，而`<>`被实现为`List`的`Cons`，一切都和以前一样。

试着比较一下`countTreeFold`和`countTreeRec`以及`toListFold`和`toListRec`。

## 利用状态单子

这个可折叠的戏法非常酷。但是为什么不过度实现和使用状态单子呢？

```
newtype State s a = State (s -> Tuple a s)

runState :: forall s a. State s a -> s -> Tuple a s
runState (State s) a = s a

instance functorState :: Functor (State s) where
    -- map :: forall a b. (a -> b) -> f a -> f b
    map g f = State (\s -> let Tuple a s' = runState f s in Tuple (g a) s')

instance applyState :: Functor (State s) => Apply (State s) where
    -- apply :: forall a b. f (a -> b) -> f a -> f b
    apply fg f = State (\s -> let Tuple g s'  = runState fg s
                                  Tuple a s'' = runState f s' in Tuple (g a) s'')

instance applicativeState :: Apply (State s) => Applicative (State s) where
    -- pure :: forall a. a -> f a
    pure a = State (\s -> Tuple a s)

instance bindState :: Apply (State s) => Bind (State s) where
    -- bind :: forall a b. m a -> (a -> m b) -> m b
    bind m mg = State (\s -> let Tuple a s' = runState m s in runState (mg a) s')

addOne :: State Int Int
addOne = State (\s -> Tuple s (s+1))

countTreeState :: forall a. Tree a -> State Int (Tree Int)
countTreeState (Leaf _)   = Leaf <$> addOne
countTreeState (Node l r) = Node <$> countTreeState l <*> countTreeState r

appendValue :: forall a. a -> State (List a) a
appendValue x = State (\s -> Tuple x (s <> Cons x Nil))

toListState :: forall a. Tree a -> State (List a) (Tree a)
toListState (Leaf x)   = Leaf <$> appendValue x
toListState (Node l r) = Node <$> toListState l <*> toListState r

main :: Effect Unit
main = do
  logShow $ snd $ runState (countTreeState exampleTree) 0
  -- 3
  logShow $ snd $ runState (toListState exampleTree) Nil
  -- ('a' : 'b' : 'c' : Nil) 
```

Enter fullscreen mode Exit fullscreen mode

我将在以后的文章中介绍`State`，敬请关注！

## 整码

```
module Main where

import Prelude (class Applicative, class Apply, class Bind, class Functor, class Show, Unit, discard, show, ($), (+), (<$>),
 (<*>), (<>))
import Effect (Effect)
import Effect.Console (logShow)
import Data.Foldable
import Data.List (List(..), foldMap)
import Data.Monoid.Additive (Additive(..))
import Data.Tuple (Tuple(..), snd)

data Tree a
    = Leaf a
    | Node (Tree a) (Tree a)

instance showTree :: Show a => Show (Tree a) where
    show (Leaf x)   = "(Leaf " <> show x <> ")"
    show (Node l r) = "(Node " <> show l <> " " <> show r <> ")"

exampleTree :: Tree Char
exampleTree =
    Node
      (Node (Leaf 'a') (Leaf 'b'))
      (Leaf 'c')

countTreeRec :: forall a. Tree a -> Int
countTreeRec tree =
    go 0 tree
    where
          go i (Leaf _)   = i + 1
          go i (Node l r) = go i l + go i r

toListRec :: forall a. Tree a -> List a
toListRec tree =
    go Nil tree
    where
          go xs (Leaf x)   = xs <> Cons x Nil
          go xs (Node l r) = go xs l <> go xs r

instance foldableTree :: Foldable Tree where
    -- foldMap :: forall a m. Monoid m => (a -> m) -> f a -> m
    foldMap g (Leaf x)   = g x
    foldMap g (Node l r) = foldMap g l <> foldMap g r

    foldr g = foldrDefault g
    foldl g = foldlDefault g

countTreeFold :: forall a. Tree a -> Int
countTreeFold tree =
    count
    where Additive count = foldMap (\_ -> Additive 1) tree

toListFold :: forall a. Tree a -> List a
toListFold tree =
    foldMap (\x -> Cons x Nil) tree

newtype State s a = State (s -> Tuple a s)

runState :: forall s a. State s a -> s -> Tuple a s
runState (State s) a = s a

instance functorState :: Functor (State s) where
    -- map :: forall a b. (a -> b) -> f a -> f b
    map g f = State (\s -> let Tuple a s' = runState f s in Tuple (g a) s')

instance applyState :: Functor (State s) => Apply (State s) where
    -- apply :: forall a b. f (a -> b) -> f a -> f b
    apply fg f = State (\s -> let Tuple g s'  = runState fg s
                                  Tuple a s'' = runState f s' in Tuple (g a) s'')

instance applicativeState :: Apply (State s) => Applicative (State s) where
    -- pure :: forall a. a -> f a
    pure a = State (\s -> Tuple a s)

instance bindState :: Apply (State s) => Bind (State s) where
    -- bind :: forall a b. m a -> (a -> m b) -> m b
    bind m mg = State (\s -> let Tuple a s' = runState m s in runState (mg a) s')

addOne :: State Int Int
addOne = State (\s -> Tuple s (s+1))

countTreeState :: forall a. Tree a -> State Int (Tree Int)
countTreeState (Leaf _)   = Leaf <$> addOne
countTreeState (Node l r) = Node <$> countTreeState l <*> countTreeState r

appendValue :: forall a. a -> State (List a) a
appendValue x = State (\s -> Tuple x (s <> Cons x Nil))

toListState :: forall a. Tree a -> State (List a) (Tree a)
toListState (Leaf x)   = Leaf <$> appendValue x
toListState (Node l r) = Node <$> toListState l <*> toListState r

main :: Effect Unit
main = do
  logShow exampleTree
  -- (Node (Node (Leaf 'a') (Leaf 'b')) (Leaf 'c'))
  logShow $ countTreeRec exampleTree
  -- 3
  logShow $ toListRec exampleTree
  -- ('a' : 'b' : 'c' : Nil)
  logShow $ countTreeFold exampleTree
  -- 3
  logShow $ toListFold exampleTree
  -- ('a' : 'b' : 'c' : Nil)
  logShow $ snd $ runState (countTreeState exampleTree) 0
  -- 3
  logShow $ snd $ runState (toListState exampleTree) Nil
  -- ('a' : 'b' : 'c' : Nil) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！