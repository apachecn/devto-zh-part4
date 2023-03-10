# 对于 Scala 中的表达式

> 原文：<https://dev.to/avalander/for-expression-in-scala-2g92>

Scala 中的 For 表达式可能看起来类似于 Java 或其他命令式语言中的 for 循环，但它们更接近于 Haskell 或 Python 中的列表理解。一个主要的区别是 Java 中的 for 循环通常会有副作用，即它会在循环之外变异一些东西。Scala 中的 for 表达式反而返回一个`Sequence`。

让我们尝试一个简单的问题来理解 for 表达式是如何有用的。假设我们想找出`1`和一个给定的数`n`之间的所有素数。

对于命令式 for 循环，我们可以这样做:

```
const primesTo = n => {
  const primes = []
  for (let i = 1; i <= n; i++) {
    if (isPrime(i)) primes.push(i)
  }
  return primes
} 
```

我们有一个索引，它会一直递增，直到达到值`n`，对于每次迭代，我们都会检查这个数字是否是质数，如果是质数，我们会将它推送到一个数组中。注意，我们实际上是在变异一个在 for 循环范围之外的值，数组`primes`。

在函数式编程中，我们可以生成一个在`1`和`n`之间的数字序列，然后过滤这个序列以保留质数。

```
def primesTo (n: Int): Seq[Int] =
  (1 to n) filter isPrime 
```

这与本主题无关，但这里是我在 Scala 中对`isPrime`的简单实现。

```
def isPrime (n: Int): Boolean =
  (2 until n) forall (n % _ != 0) 
```

Scala 中的 for 表达式接受一个或多个生成器，还可以选择过滤器。发生器采用`x <- <collection>`的形式，滤波器采用`if <condition>`的形式。所以让我们用 for 表达式重写我们的`primesTo`函数。

```
def primesTo (n: Int): Seq[Int] =
  for (i <- (1 to n) if isPrime(i)) yield i 
```

到目前为止差别不大。有人甚至会说它比上一个例子更长。然而，当我们进入嵌套序列领域时，for 表达式可以使代码更具可读性。

让我们试试另一个问题。如果我们想找到满足[勾股定理](https://en.wikipedia.org/wiki/Pythagorean_theorem)的三个小于`n`的整数的所有组合呢？

对于命令式 for 循环，我们可以这样做:

```
const pythagorean = n => {
  const result = []
  for (let a = 1; a < n; a++) {
    for (let b = 1; b < a; b++) {
      for (let c = 1; c < b; c++) {
        if (a * a === b * b + c * c) {
          result.push([ a, b, c ])
        }
      }
    }
  }
  return result
} 
```

如果我们试图在 Scala 中生成一个序列并过滤它，我们可以做类似的事情:

```
def pythagorean (n: Int): Seq[(Int, Int, Int)] =
  (1 until n) flatMap (a =>
    (1 until a) flatMap (b =>
      (1 until b) map (c => (a, b, c))
    )
  ) filter (v => v._1 * v._1 == v._2 * v._2 + v._3 * v._3) 
```

这并不完全可怕，但在这一点上，它并不是最可读的代码。

在 for 表达式中，我们可以使用多个生成器，所以我们不需要像前面例子中那样多的缩进层次。

```
def pythagorean (n: Int): Seq[(Int, Int, Int)] =
  for {
    a <- (1 until n)
    b <- (1 until a)
    c <- (1 until b)
    if a * a == b * b + c * c
  } yield (a, b, c) 
```

注意，在这个例子中，我们在`{}`而不是`()`之间添加了发生器和滤波器。这两者的区别在于，使用括号我们需要在不同的生成器之间添加分号，而使用花括号我们可以跳过它们，这在我们用新行分隔生成器时很方便。

总之，for 表达式提供了一种可读性更好的语法来处理嵌套序列。