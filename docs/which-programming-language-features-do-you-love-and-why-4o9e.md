# 你喜欢哪种编程语言特性，为什么？

> 原文：<https://dev.to/dwayne/which-programming-language-features-do-you-love-and-why-4o9e>

我最喜欢的几个包括:[一级函数](https://en.wikipedia.org/wiki/First-class_function)、[代数数据类型](https://en.wikipedia.org/wiki/Algebraic_data_type)和[不透明数据类型](https://en.wikipedia.org/wiki/Opaque_data_type)。

**一级功能**

因为所有的[很酷的抽象](https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/book-Z-H-12.html#%_sec_1.3) — `map`、`filter`、`accumulate`、`fold`等等——你可以用它们来构建。

```
(define (map f l)
  (if (null? l)
      '()
      (cons (f (car l)) (map f (cdr l))))) 
```

Enter fullscreen mode Exit fullscreen mode

**代数数据类型**

尤其是[求和类型](https://en.wikipedia.org/wiki/Tagged_union)。我觉得所有语言都应该支持这个特性。它们使得精确建模你的领域变得非常方便。

> 排除无效数据会使您的代码更短、更简单、更可靠。通过确保代码中的可能值集与现实生活中的有效值集完全匹配，许多问题就迎刃而解了。— [类型为集合](https://guide.elm-lang.org/appendix/types_as_sets.html)

```
type RemoteData e a
  = NotAsked
  | Loading
  | Failure e
  | Success a 
```

Enter fullscreen mode Exit fullscreen mode

**不透明数据类型**

数据抽象允许您隐藏实现细节，并且它有利于改变表示。例如，您可以从一个简单的实现开始，如果它被证明对系统的整体性能至关重要，那么就改变为一个更有效的表示。不透明数据类型经常被用来实现[抽象数据类型](https://en.wikipedia.org/wiki/Abstract_data_type)。

```
module Stack
  ( Stack
  , empty, push, pop
  ) where

data Stack a
  = Empty
  | Stk a (Stack a)

empty :: Stack a
empty = Empty

push :: a -> Stack a -> Stack a
push x s = Stk x s

pop :: Stack a -> Stack a
pop Empty = Empty
pop (Stk _ s) = s 
```

Enter fullscreen mode Exit fullscreen mode

你呢？