# 不要看这个单子教程

> 原文：<https://dev.to/codenoodle/don-t-read-this-monad-tutorial-2cfc>

[![alt text](img/851b2ea9ef19a9b40eb8be34fe6ed195.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j40mEvV0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Db4szC7W0AAKvHh.jpg:large)

为了打破每个单子教程都声称比其他教程更好的循环，我真的不认为这个教程会让你比任何其他教程更好地理解单子。那是因为阅读教程只能让你到此为止。相反，你应该写你自己的并打算发表。挖掘所有的细节，亲自尝试所有的东西，回答你的读者必然会有的所有问题。我在丹·皮波尼的想法中找到了灵感，他认为我可以自己发明单子，而 T2 的布兰特·约尔吉断言单子实际上不是墨西哥卷饼。

下面是我在 2018 年 9 月写的帮助我理解单子的教程，做了一些小的修改。

## 丑陋的问题

在 scala 中，我们真的喜欢像这样把方法调用串在一起。易读易调试。

```
List(1, 2, 3, 4, 5)
  .map(_ + 10)
  .filter(_ % 2 == 0)
  .take(2) 
```

Enter fullscreen mode Exit fullscreen mode

我们正在开发的假想应用程序实际上需要一个列表，其中这些函数返回列表结果和每个函数的调试字符串，而不是一个常规列表。

```
object DebugList {
  //variable argument syntax so it works like List.apply
  def apply[A](a: A*):DebugList[A] = DebugList(List(a: _*))
}

case class DebugList[A](l: List[A]){
  def map[B](f: A => B): (DebugList[B], String) =
    (DebugList(l map f), "mapped")

  def filter(f: A => Boolean): (DebugList[A], String) =
    (DebugList(l filter f), "filtered")

  def take(n: Int): (DebugList[A], String) =
    (DebugList(l take n), s"took$n")
} 
```

Enter fullscreen mode Exit fullscreen mode

DebugList 是这样使用的...太难看了。

```
val (dl1, str1) = DebugList(1, 2, 3, 4, 5).map(_ + 10)
val (dl2, str2) = dl1.filter(_ % 2 == 0)
val (dl3, str3) = dl2.take(2) 
```

Enter fullscreen mode Exit fullscreen mode

但这是我们的应用程序所需要的，所以我们要努力让它工作。

问题是我们必须在元组上做所有这些笨拙的匹配。另外，如果我们想按顺序查看我们的调试字符串，我们需要做一些类似

`s"$str1 $str2 $str3"`

的事情来读取、打印或写入它们。因为元组是问题所在，所以让我们试着把元组放在另一个类中，这样我们就可以编写像`flatMap`这样的函数来为我们做大量的工作。

### 平面图和地图

当我们为这个 case 类填充`flatMap`和`map`的定义时，我们必须确保产生的调试对象有一个字符串，该字符串包含来自这个调试对象的内容和来自 f.
的结果

```
case class Debug[A](a: A, s: String) {
  def flatMap[B](f: A => Debug[B]): Debug[B] = {
    val Debug(b, s1) = f(a)
    Debug(b, s + s1)
  }

  def map[B](f: A => B): Debug[B] =
    Debug(f(a), s)
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将需要一个伴随对象中的构造函数，它给我们一种将常规的旧的`A`对象转换成`Debug[A]`对象的方法。

### 一条道里面

当我们填充单元函数时，空字符串是一个合理的选择，如果还没有空字符串的话。

```
case object Debug {
  def apply[A](x: A): Debug[A] =
    unit(x)

  def unit[A](x: A): Debug[A] =
    Debug(x, "")
} 
```

Enter fullscreen mode Exit fullscreen mode

### 定制列表

既然我们已经重构了元组，我们可以使用返回我们新重构的调试类型而不是元组的函数来创建一个更好的 DebugList。

```
object BetterDebugList {
  //variable argument syntax so it works like List.apply
  def apply[A](a: A*): BetterDebugList[A] = BetterDebugList(List(a: _*))
}

case class BetterDebugList[A](l: List[A]){
  def map[B](f: A => B): Debug[BetterDebugList[B]] =
    Debug(BetterDebugList(l map f), "mapped")

  def filter(f: A => Boolean): Debug[BetterDebugList[A]] =
    Debug(BetterDebugList(l filter f), "filtered")

  def take(n: Int): Debug[BetterDebugList[A]] =
    Debug(BetterDebugList(l take n), s"took $n")
} 
```

Enter fullscreen mode Exit fullscreen mode

betterdebugliest 现在可以这样使用:

```
val debug = Debug(BetterDebugList(1, 2, 3, 4, 5))
  .flatMap(_.map(_ + 10))
  .flatMap(_.filter(_ % 2 == 0))
  .flatMap(_.take(2)) 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。这看起来与我们最初使用`List`的方式非常相似。不再有元组匹配！

为了隐藏那些显式的`flatMap`调用，我们可以使用它们来理解，因为它们更漂亮，但做的是完全相同的事情。

```
val debug = for {
  w <- Debug(BetterDebugList(1, 2, 3, 4, 5))
  x <- w.map(_ + 10)
  y <- x.filter(_ % 2 == 0)
  z <- y.take(2)
} yield z 
```

Enter fullscreen mode Exit fullscreen mode

如果你对给每个中间状态取名字感到烦恼，为了理解起见，让你把它们都叫成同一个东西。这几乎让我们的不可变代码看起来有点像是可变的。

```
val debug = for {
  x <- Debug(BetterDebugList(1, 2, 3, 4, 5))
  x <- x.map(_ + 10)
  x <- x.filter(_ % 2 == 0)
  x <- x.take(2)
} yield x 
```

Enter fullscreen mode Exit fullscreen mode

现在您可以像这样得到调试字符串:

```
println(debug.s) 
```

Enter fullscreen mode Exit fullscreen mode

## 惊喜！你做了单子。

就像许多其他函数式编程工具一样，monad 采用了合理有用的代码，否则使用起来可能会非常笨拙，并且使它看起来更加自然。请注意，我们如何使用同一个`Debug`类来创建我们想要的任何其他类的可调试版本。

在 Scala 中，我们一直使用单子，因为它们太自然了。`List`和`Option`都是单子，我们在几乎每个 Scala 初学者教程中都可以看到。

[![alt text](img/8e86d593a95cb4e862ffaccd54ac5b59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f6qdWRuY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://pbs.twimg.com/media/Dn20jIIWsAERqkm%3Fformat%3Djpg%26name%3Dsmall) 
图片来源:[我的推特](https://twitter.com/codenoodle/status/1044189951372464128?s=20)

## 好吧但是单子是什么？

一个单子需要...

1 -平面图(有时称为绑定)

2 -单元(通常在 Scala 中用 apply 实现)

3 -遵循三个单子定律

这里有一些你已经熟悉的单子的例子。它们都使用了`apply`方法，而不是一个名为“unit”的函数，并且它们都有`flatMap`。

```
List(1)                    == List.unit(1)
List(1,2,3)                == List.unit(1,2,3)
Option(5)                  == Option.unit(5)
Try(throw new Exception()) == Try.unit(throw new Exception()) 
```

Enter fullscreen mode Exit fullscreen mode

为了成为单子，它也必须遵循三个单子定律。这些法则只是确保我们能够以我们期望的方式重构代码，并得到可预测的结果。

### 单子法则

*f 和 g 是函数

m 是单子的一个实例，也称为“单子作用”*

1 -右侧标识

```
unit(z).flatMap(f) == f(z) 
```

Enter fullscreen mode Exit fullscreen mode

2 -左侧标识

```
m.flatMap(unit) == m 
```

Enter fullscreen mode Exit fullscreen mode

3 -关联性

```
m.flatMap(f).flatMap(g) == m.flatMap(x => f(x).flatMap(g)) 
```

Enter fullscreen mode Exit fullscreen mode

让我们用单子看看这些法律定义的例子。想象一下，如果这些陈述不总是正确的，那么使用 List 会有多奇怪:

1 -右侧标识

```
List(2).flatMap(x => List(x * 5)) == List(2 * 5) 
```

Enter fullscreen mode Exit fullscreen mode

2 -左侧标识

```
List(2).flatMap(List(_)) == List(2) 
```

Enter fullscreen mode Exit fullscreen mode

3 -关联性

```
List(2).flatMap(w => List(w, w)).flatMap(y => List(y * 2)) == 
List(2).flatMap(x => List(x, x).flatMap(z => List(z * 2))) 
```

Enter fullscreen mode Exit fullscreen mode

## 打破单子定律

FMCounter 类统计`flatMap`被调用的次数。它看起来像一个单子，但它打破了三大定律中的一些。

下面是它的定义。让我们找出它违反了哪些法律。

```
case object FMCounter {
  def unit[A](a: A): FMCounter[A] =
    FMCounter(a, 0)

  def append(str: String, end: String): FMCounter[String] =
    unit(str + end)
}

case class FMCounter[A](a: A, counter: Int) {
  def flatMap[B](f: A => FMCounter[B]): FMCounter[B] = {
    val FMCounter(b, k) = f(a)
    FMCounter(b, counter + k + 1)
  }

  def map[B](f: A => B): FMCounter[B] =
    FMCounter(f(a), counter)
} 
```

Enter fullscreen mode Exit fullscreen mode

FMCounter 破坏了正确的标识。这里有一个反例:

```
FMCounter.unit("My").flatMap(x => FMCounter.unit(x + "Counter")) = FMCounter(MyCounter,1)
FMCounter.unit("My" + "Counter") = FMCounter(MyCounter,0) 
// not the same! 
```

Enter fullscreen mode Exit fullscreen mode

FMCounter 破坏左标识。这里有一个反例:

```
FMCounter.unit("MyCounter").flatMap(FMCounter.unit) = FMCounter(MyCounter,1)
FMCounter.unit("MyCounter") = FMCounter(MyCounter,0) 
// not the same! 
```

Enter fullscreen mode Exit fullscreen mode

但是 FMCounter 其实是联想的。如果你看一下结合性的定义，你在每一边调用 flatMap 相同的次数，这是它通过的一个很好的指示。

但是如果你在寻找更正式的东西，这里有一个非常规的证明，使用了 scala 式的语法。如果这不是你的难题，请随意过去。

```
let {FM} be the set of all monadic actions of type FMCounter
let f : A => FMCounter[B]
let g : B => FMCounter[C]

Theorem:  ∀ x ∈ {FM} x.flatMap(f).flatMap(g) == x.flatMap(a => f(a).flatMap(g))
          x                               = FMCounter[A](a: A, i:         Int)
          f(a)                            = FMCounter[B](b: B, k0:        Int)
          x.flatMap(f)                    = FMCounter[B](b: B, i+k0+1:    Int)
          g(b)                            = FMCounter[C](c: C, k1:        Int)
          x.flatMap(f).flatMap(g)         = FMCounter[C](c: C, k0+k1+2:   Int)

          h: A => FMCounter[C]            = (a: A) => f(a).flatMap(g)        
          h                               = (a: A) => {
                                                f(a) = FMCounter[B](b: B, k0:      Int)
                                                g(b) = FMCounter[C](b: C, k1:      Int)
                                                       FMCounter[C](b: C, k0+k1+1: Int)
                                            } 

          h(a)                            = FMCounter[C](b: C, k0+k1+1: Int)
          x.flatMap(a => f(a).flatMap(g)) = x.flatMap(a => h(a))
          x.flatMap(a => h(a))            = FMCounter[C](b: C, k0+k1+2: Int)

          substitution: FMCounter[C](c: C, k0+k1+2: Int) == FMCounter[C](c: C, k0+k1+2: Int)
          TRUE 
```

Enter fullscreen mode Exit fullscreen mode

因为 FMCounter 计算了`flatMap`被调用的次数，它打破了要求表达式具有不同数量的`flatMap`的属性。

因为它违反了两条定律，所以有多种方法可以正确地编写相同的代码，从而导致不同的`flatMap`计数。这意味着这可能不是我们想要的解决方案。而且它不是一个单子。

## 结论

如果你面临一个特殊的问题，比如把返回元组的函数串在一起，你真的可以自己发明单子。单子只是一种工具，可以让笨拙的解决方案看起来更自然。我们已经一直在使用单子，所以理解它们为什么如此擅长自己的工作是值得的。