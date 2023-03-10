# 哈斯克尔之旅[第二部分]

> 原文：<https://dev.to/pmalhaire/an-haskell-trip-part-2-kii>

# haskell 之旅[第 2 部分]:让我们来看看 Haskell 语法

*   [简介](#introduction)
*   [让我们进入哈斯克尔的世界](#lets-enter-the-haskell-world)
*   [满足 haskell 语法](#meet-the-haskell-syntax)
    *   [遇见`::`利用`:type`T3】](#meet--using-type)
    *   [遇见`->`(箭头)利用`:type`](#meet---arrow-using-type)
    *   [遇到`=>`双箭头使用`:info`T3】](#meet-the--double-arrow-using-info)
    *   [`$`【T2 案】](#the-case-of-)
*   [【插曲】口齿不清的味道](#interlude-the-smell-of-lisp)
*   [结论](#conclusion)
*   [附录:参考文献](#appendix--references)

## 简介

既然[高层设计](//design.md)已经足够清晰可以使用了。下面介绍一下`haskell`。这个项目是我第一次接触这种奇妙的语言，也是我用全新的视角介绍这种语言的机会。

## 让我们进入哈斯克尔的世界

先喘口气，介绍一下`Haskell`。有时当我参加会议时，我觉得`Haskell`是程序员的标签。如果你知道`Haskell`，你一定很聪明。良好的...也许这就是为什么你在看我的帖子。聪明与否`Haskell`是`nerd`的乐趣。

当你学习新的语言时，从一种语言到另一种语言进行类比是很实用的。我们中的许多人学习了`Html`、`Javascript`、`PHP`、`Cpp`或其他顺序/组合，包括`python`、`lua`、`golang`、`ruby`或许多其他语言。
学习`Haskell`并不是从你之前学习的语言跳转而来(除非你已经学习了其他函数式语言)。这是一个新世界。每一条新的`Haskell`线都会让你学到一些东西:一个全新的计算机编程视角！

介绍并不容易，因为这是一个新世界的跳跃。我将采取一种相当不寻常的方式，如果你想要“古典”的方式，你会在[参考文献](#appendix--references)中看到有趣的书。这里的目标是介绍我们创建 askBarrel 所需的概念。

## 满足 haskell 语法

Haskell 是一个丰富的宇宙，有许多入口。

我的有点奇怪，我发现`Haskell`的语法非常漂亮。

在学习`haskell`的时候有一点可以让你放松，那就是`=>`、`::`、`->`、`$`、`<$`等符号。它基于许多有用的概念。一旦你有了这个概念，这个符号就会自然而然地出现。你会问自己，我想做，我知道有一个字形。

这个项目的目标是制作一个`REPL`解析器。我们的主要对象是`strings`(一个字符列表`[Char]`)，所以我们将关注它。

一个有用的[链接](https://github.com/takenobu-hs/haskell-symbol-search-cheatsheet)帮助你搜索符号。

### 遇见`::`利用`:type`

让我们用 ghci 写一个字符串`test`。

```
ghci
λ> "test"
"test" 
```

Enter fullscreen mode Exit fullscreen mode

让我们在`ghci`中使用`:type`(或者它的简称`:t`)，毕竟`Haskell`是关于类型的。

```
ghci
λ> :t "test"
"test" :: [Char] 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里遇到了`::`符号。双冒号或类型签名。
它告诉编译器我们的`string`的类型是`[Char]``Char`的列表。

`[Char]`是默认的字符串类型，但是我们可以使用`::`强制使用另一种类型`String`。

```
ghci
λ> "test"::String
"test"
λ> :t "test"::String
"test"::String :: String 
```

Enter fullscreen mode Exit fullscreen mode

### 遇见`->`(箭头)利用`:type`

一个好几个更有用。

让我们连接两个字符串。

```
ghci
λ> "first" ++ "second"
"firstsecond" 
```

Enter fullscreen mode Exit fullscreen mode

我们再来看看类型。

```
ghci
λ> :t ++
<interactive>:1:1: error: parse error on input ‘++’ 
```

Enter fullscreen mode Exit fullscreen mode

哎呀，看来`++`没有类型。事实上它确实有一个，但是`++`是由特殊字符`+`组成的，为了得到它的类型，我们需要使用括号。

```
ghci
λ> :t (++)
(++) :: [a] -> [a] -> [a]
λ> 
```

Enter fullscreen mode Exit fullscreen mode

这个奇怪但美丽的`[a] -> [a] -> [a]`解释了`++`是类型`[a]`返回类型`[a]`的两个变量的函数。你可能想知道为什么我们有几个`->`,这是因为`haskell`中的任何函数都可以被看作是由一个变量的函数组成的结构，乍一看这似乎很奇怪，但这是使`haskell`强大的原因之一。

让我们把`++`分成几部分，加上括号。

```
ghci
λ> ("first" ++) "second"
"firstsecond"
λ> :t ("first" ++)
("first" ++) :: [Char] -> [Char] 
```

Enter fullscreen mode Exit fullscreen mode

所以`("first" ++)`式签名(`::`右侧)是`[Char] -> [Char]`。

从类型为`[Char]`的参数返回(`->`)一个类型为`[Char]`的值。

让我们回到`(++)`。

```
ghci
λ> :t (++)
(++) :: [a] -> [a] -> [a]
λ> 
```

Enter fullscreen mode Exit fullscreen mode

`(++)`型签名`[a] -> [a] -> [a]`告诉我们:

*   如果我给`++`一个参数，比如在`("first" ++)`中，它将返回一个取`[a]`并返回`[a]`的函数。
*   如果我给`++`两个参数，比如在`"first" ++ "second"`中，它将返回一个类型为`[a]`的实例。

这种操作将非常有用，因为它允许仅在需要结果时链接多个函数进行计算。自 [`Haskell Curry`](https://en.wikipedia.org/wiki/Haskell_Curry) 发明此物后，就称之为 [`Currying`](https://wiki.haskell.org/Currying) 并将其命名为`Haskell`。

它像预期的那样工作。我高贵的同事让我意识到这并不是本能。与此同时，我们正在看一个`Haskell`的美女。一种方法是将`signature`剪成如下类型。

```
-- to use the function entirely give it 2 parameters
-- | parameters     | result |
--   [a] ->   [a]  ->    [a]
λ> "first-" ++ "-second"
"first--second"
-- to use partially the function give it only one parameters
-- | parameter   |      result |
--    [a]       ->  [a] -> [a]
-- here we will have a function you'll need one more `[a]` to get a result such as ("first-" ++)
λ> f=("first-" ++)
-- ("first-" ++) it used as a brick once
λ> f "test"
"first-test"
-- ("first-" ++) it used as a brick twice
λ> f ( f "test")
"first-first-test" 
```

Enter fullscreen mode Exit fullscreen mode

[https://dev.to/pmalhaire/an-haskell-trip-part-2-kii](https://dev.to/pmalhaire/an-haskell-trip-part-2-kii)

### 遇到`=>`双箭头使用`:info`

ghci 中另一个非常有用的功能是`info`,它给出了关于类型、变量或函数的所有信息。

```
λ> :t 1
1 :: Num p => p 
```

Enter fullscreen mode Exit fullscreen mode

这里我们的类型签名(`::`)作为额外的`=>`。
这给类型`p`添加了一个约束。`p`必须是属于`Num`实例的任何类型。

让我们用`:info`来了解更多。

```
ghci
λ> :info Num
class Num a where
  (+) :: a -> a -> a
  (-) :: a -> a -> a
  (*) :: a -> a -> a
  negate :: a -> a
  abs :: a -> a
  signum :: a -> a
  fromInteger :: Integer -> a
  {-# MINIMAL (+), (*), abs, signum, fromInteger, (negate | (-)) #-}
    -- Defined in ‘GHC.Num’
instance Num Word -- Defined in ‘GHC.Num’
instance Num Integer -- Defined in ‘GHC.Num’
instance Num Int -- Defined in ‘GHC.Num’
instance Num Float -- Defined in ‘GHC.Float’
instance Num Double -- Defined in ‘GHC.Float’
λ> 
```

Enter fullscreen mode Exit fullscreen mode

为了有效，类型 p 必须实现在类`Num`中定义的 7 个函数。

注意:这里的`class`不是面向对象编程。

在我们的`ghci`上下文中，Num 的可用实例是

```
instance Num Word -- Defined in ‘GHC.Num’
instance Num Integer -- Defined in ‘GHC.Num’
instance Num Int -- Defined in ‘GHC.Num’
instance Num Float -- Defined in ‘GHC.Float’
instance Num Double -- Defined in ‘GHC.Float’ 
```

Enter fullscreen mode Exit fullscreen mode

如果我们愿意，我们可以实现 https://dev.to/pmalhaire/an-haskell-trip-part-2-kiimore。这是`haskell`的另一个妙处，我们在这里用变量`a`告诉契约`Num`，任何匹配契约的实例都可以使用`Word`、`Integer`等。

```
ghci
λ> :t 1.1
1.1 :: Fractional p => p
λ> :t 1
1 :: Num p => p
λ> 1.1 +1
2.1 
```

Enter fullscreen mode Exit fullscreen mode

### `$`的情况

考虑下面的情况，我们想给字符串添加一个头和一个尾。

```
ghci
λ> "head-" ++ "content" ++ "-tail"
"head-content-tail" 
```

Enter fullscreen mode Exit fullscreen mode

做一些类似`headtail "content"`的事情会更有用。

让我们重复使用我们以前做过的事情。

`"head-"`将首先被添加，因此我们很好

```
ghci
λ> "head-" ++ "content" 
```

Enter fullscreen mode Exit fullscreen mode

`"-tail"`在后必须加上。
我们将创建一个在其后添加`"-tail"`的函数。

```
ghci
λ> (++ "-tail") "content"
"content-tail" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以像做 T1 一样做 T0

```
ghci
λ> (++ "-tail") ( "head-" ++ "content" )
"head-content-tail" 
```

Enter fullscreen mode Exit fullscreen mode

这里显然需要括号，如果我们省略，那么我们得到了

```
ghci
λ> (++ "-tail") "head-" ++ "content"
"head--tailcontent" 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至可以使用 head 的部分函数做得更好。

```
ghci
λ> ("head-" ++) ((++ "-tail") "content")
"head-content-tail" 
```

Enter fullscreen mode Exit fullscreen mode

它很好，但是有很多插入语。

我来介绍一下`$`。少用括号是一个运算符，它用括号括住后面的内容。

```
ghci
λ> ("head-" ++) $ (++ "-tail") "content"
"head-content-tail" 
```

Enter fullscreen mode Exit fullscreen mode

让我们创建一个函数。

```
ghci
λ> headtail a = ("head-" ++) $ (++ "-tail") a
λ> headtail "content"
"head-content-tail" 
```

Enter fullscreen mode Exit fullscreen mode

通常函数参数对于变量被写成`a`(然后是`b`)，对于函数被写成`f`(然后是`g`)。
这里的`a`就是`headtail`的论点。

这里我们没有对`a`的类型进行任何限制。因为它被`(++ "-tail")`使用，所以它必须符合它的类型。

```
ghci
λ> :t (++ "-tail")
(++ "-tail") :: [Char] -> [Char] 
```

Enter fullscreen mode Exit fullscreen mode

`a`是隐式的一个`[Char]`。

让我们检查一下。

```
ghci
λ> headtail a = ("head-" ++) $ (++ "-tail") a
λ> :t headtail
headtail :: [Char] -> [Char] 
```

Enter fullscreen mode Exit fullscreen mode

## 【插曲】口齿不清的味道

Haskell 让我想起了我在 Péru 天主教大学的 Tupia 教授的 IA 课程中学习 lisp 的时候。

让我们在 ghci 中查看一下这个[链接](https://www.haskell.org/platform/)来安装 haskell。

```
ghci
λ> ( 3 + 4 ) / ( 4 + 6 )
0.7 
```

Enter fullscreen mode Exit fullscreen mode

这个简单的计算使用了`infix`操作符。这是我们大多数人的本能。

那些都是等价的:

```
( 3 + 4 ) / ( 4 + 6 )
(/) ( 3 + 4 ) ( 4 + 6 )
(/) ( (+) 3 4 ) ( (+) 4 6 ) 
```

Enter fullscreen mode Exit fullscreen mode

我们来看看 lisp 版本:

```
clisp
[1]> (/ (+ 3 4) (+ 4 6))
7/10 
```

Enter fullscreen mode Exit fullscreen mode

这是`lisp`和`haskell`社区联系在一起的众多原因之一。

我没有注意到`++`是一个`infix function`的事实。为了理解它，试着移动`++`一边和另一边`(++ "string")`和`("string" ++)`。

更多细节见[中缀函数](https://wuciawe.github.io/functional%20programming/haskell/2016/07/03/infix-functions-in-haskell.html)。

## 结论

https://dev.to/pmalhaire/an-haskell-trip-part-2-kii
[我们遇到了`Haskell`语法，目标是为`REPL`客户端操作字符串。在第 3 部分(尚未出版)中，我们将解决被称为`Monad`的神秘生物`M word`。](https://dev.to/pmalhaire/an-haskell-trip-part-2-kii)

## 附录:参考文献

*   [学你一个 Haskell](http://learnyouahaskell.com/)
*   [Haskell 符号搜索备忘单](https://github.com/takenobu-hs/haskell-symbol-search-cheatsheet)
*   [中缀函数](https://wuciawe.github.io/functional%20programming/haskell/2016/07/03/infix-functions-in-haskell.html)。