# 我从阅读 Elm 的语法文档中学到了 5 件值得注意的事情

> 原文：<https://dev.to/dwayne/5-notable-things-i-learned-from-reading-elm-s-syntax-documentation-6>

## 5。切换评论的巧妙技巧

```
{--}
add x y = x + y
--} 
```

Enter fullscreen mode Exit fullscreen mode

只需在第一行删除或插入`}`来注释或取消注释`add`函数。

## 4。多行字符串

它们对于保存 JSON 或其他有引号的内容很有用。

```
quotes : String
quotes =
  """
  {
    "quotes": [
      { "content": "A language that doesn't affect the way you think about programming is not worth knowing.", "author": "Alan J. Perlis" },
      { "content": "You don't understand anything until you learn it more than one way.", "author": "Marvin Minsky" }
    ]
  }
  """ 
```

Enter fullscreen mode Exit fullscreen mode

## 3。字段访问功能

```
alan = { name = "Alan Turing", age = 41 }
alonzo = { name = "Alonzo Church", age = 92 }

.name alan == "Alan Turing"

-- > .name
-- <function> : { a | name : String } -> String

.age alonzo == 92

-- > .age
-- <function> : { a | age : Int } -> Int 
```

Enter fullscreen mode Exit fullscreen mode

注意`.name`和`.age`将可扩展记录作为参数。`.name`接受任何带有`name`字段的记录，`.age`接受任何带有`age`字段的记录。

## 2。可扩展记录定义

```
type alias Named a =
  { a | name : String }

type alias Positioned a =
  { a | x : Float, y : Float }

alan : Named { age: Int }
alan =
  { name = "Alan Turing"
  , age = 41
  }

origin : Named (Positioned {})
origin =
  { name = "origin"
  , x = 0
  , y = 0
  }

getName : Named a -> String
getName { name } =
  name

getPos : Positioned a -> (Float, Float)
getPos { x, y } =
  (x, y)

names : List String
names =
  [ getName alan
  , getName origin
  ] 
```

Enter fullscreen mode Exit fullscreen mode

它允许你编写小的正交函数来处理各种各样的记录。您可以获得动态类型语言的许多自由，而类型检查器可以保护您的安全。

## 1。type classes-类型类别

当埃文发布[可扩展记录](https://elm-lang.org/news/0.7)时，他说了一些有趣的话:

> 多态可扩展记录提供了 OCaml 和 SML 中一流模块的一些功能。
> 
> ...
> 
> 这些记录也使得创建一个更加劳动密集型的 typeclasses 版本成为可能。
> 
> ...
> 
> 我之所以迟迟不向 Elm 添加 typeclasses，部分原因是因为记录、一级模块和 typeclasses 做了很多相同的事情([记录和模块](http://research.microsoft.com/pubs/65409/scopedlabels.pdf)以及模块和 typeclasses)。已经有一两个提议要求[统一一级模块和类型类](http://www.mpi-sws.org/~dreyer/papers/mtc/main-long.pdf)。我想确保 Elm 得到了所有这些特性中最好的一个，所以我做了大量的研究来确保我不会在这里做出错误的选择。

```
type alias Eq a =
  { eq : a -> a -> Bool
  , neq : a -> a -> Bool
  }

type alias Comparison a =
  { compare : a -> a -> Order
  , lt : a -> a -> Bool
  , lte : a -> a -> Bool
  , gt : a -> a -> Bool
  , gte : a -> a -> Bool
  , min : a -> a -> a
  , max : a -> a -> a
  } 
```

Enter fullscreen mode Exit fullscreen mode

尼基塔·沃尔科夫[重新发现了](https://github.com/nikita-volkov/typeclasses)这个想法，并在他的文章中强调了[的局限性](https://github.com/nikita-volkov/typeclasses/tree/9eac16c6bb85c037b6eac889043b11e526d46aa9#why-no-functors-monads-and-alike)。

## 参考文献

*   [榆树语法](https://elm-lang.org/docs/syntax)
*   [记录](https://elm-lang.org/docs/records)
*   [榆树 0.7](https://elm-lang.org/news/0.7)