# 为什么我不喜欢 Scala 的“要么”

> 原文：<https://dev.to/buildingdragons/why-i-don-t-like-scala-s-either-5elf>

### 问题

假设我们被指派开发一个验证用户名的功能。出于示例的目的，该函数只检查所提供的`String`值是否在 4 到 10 个字符之间。

函数的第一稿可能是这样的:

```
def validateUsername(value: String): Boolean = {
  val length = value match {
    case s: String => s.length
    case _         => 0
  }

  length >= 4 && length <= 10
} 
```

这个实现为所有有效的用户名返回`true`，但是有两个不幸的缺点:

1.  函数返回一个`Boolean`的事实并不鼓励以纯函数的方式使用它。
2.  如果用户名无效，调用者不会得到任何关于原因的信息。

第一个问题可以通过稍微修改实现来轻松解决:

```
def validateUsername(value: String): Option[String] = value match {
  case s: String if (s.length >= 4 && s.length <= 10) => Some(value)
  case _                                              => None
} 
```

为了能够弥补第二个缺点，我们需要更详细地阐述。

### 这就是为什么`Option`不是一个好的选择...

虽然 [`Option`](https://www.scala-lang.org/api/current/scala/Option.html) 允许在函数样式中使用`validateUsername`，但它并不提供验证失败的原因。

我们需要的是返回经过验证的用户名的方法，或者指出验证失败原因的错误。幸运的是，Scala 为这个问题提供了几种解决方案——让我们仔细看看其中的一些。

### ...而且`Either`都不是。

通过[`scala.util.Either`](https://www.scala-lang.org/api/current/scala/util/Either.html)*Scala 标准库*提供了一个包装两种(不同)类型的容器。

让我们修改我们的例子，使它使用`Either`作为返回类型:

```
def validateUsername(value: String): Either[String, String] = {
  val min = 4    // minimum legnth for username
  val max = 10   // maximum lenght for username

  val length = value match {
    case s: String => s.length
    case _         => 0
  }

  if (length >= min && length <= max) {
    Right(value)
  } else if (length < min) {
    Left("'value' is too short.")
  } else {
    Left("'value' is too long.")
  }
} 
```

现在，让我们在一个简单的例子中使用`validateUsername`:

```
validateUsername("user") match {
  case Right(u) => println(s"A valid username: '$u'");
  case Left(r)  => println(s"Invalid username; reason: '$r'")
} 
```

这看起来并不太糟糕，但正如标题所示:*我非常不喜欢 Scala 的`Either`* ，我将向你解释为什么。

为了充分理解我不喜欢的地方，让我们简单地看一下它的实现。

与`Option`非常相似，`Either`是一个*密封的抽象类*，有两个具体的实现(`Left`和`Right`)，但是重要的区别是采用两个类型参数而不是一个。我对派生类的实现没有太多问题，而是对它们的命名有太多问题。

对我来说，`Left`和`Right`没有太多的语义。当然，这些名字指的是各自类型参数在声明中的位置，但仅此而已。一个人不需要患有诵读困难症就能感到困惑。哪种类型表示函数返回时没有错误？是`Left`因为它被首先声明还是`Right`因为它的拼写像“右”？

如果结果类型能够明确指出函数返回的是好还是有错，那该有多好？让我们看看 Scala 标准库还能提供什么来反映我们的需求。

### 让我们`Try`点别的

[`scala.util.Try`](https://www.scala-lang.org/api/current/scala/util/Try.html) 是 Scala 2.10 中引入的，旨在执行容易出现异常的操作，而无需关心显式的异常处理。

```
import scala.util.{Try, Success, Failure}

Try(1 / 0) match {
  case Success(s) => println(s"Result: $s")
  case Failure(x) => println(s"Exception: $x")
} 
```

`Try`在异常情况下工作正常，但是我们能把它用作我们的`validateUsername`函数的返回类型吗？我们可以，但这会使实现有些笨拙，不够简洁:

##### 1。如果验证失败，则引发异常

```
import scala.util.{Try, Success, Failure}

def validateUsername(value: String): Try[String] = {
  val min = 4 // minimum legnth for username
  val max = 10 // maximum lenght for username

  val length = value match {
    case s: String => s.length
    case _         => 0
  }

  if (length < min) {
    Failure(new Exception("'value' is too short."))
  } else if (length > max) {
    Failure(new Exception("'value' is too long."))
  } else {
    Success(value)
  }
}

validateUsername("username") match {
  case Success(username) => println(s"Valid username: $username")
  case Failure(ex)       => println(s"Invalid: '${ex.getMessage}'.")
} 
```

这里，如果用户名验证失败，我们会故意抛出一个异常。这不是一个好主意，因为异常应该只在异常情况下抛出，而不应该是函数调用的预期和有效的“结果”。

##### 2。`Try`作为结果类型

```
import scala.util.{Try, Success, Failure}

def validateUsername(value: String): Try[String] = {
  val min = 4 // minimum legnth for username
  val max = 10 // maximum lenght for username

  val length = value match {
    case s: String => s.length
    case _         => 0
  }

  if (length < min) {
    Failure(new Exception("'value' is too short."))
  } else if (length > max) {
    Failure(new Exception("'value' is too long."))
  } else {
    Success(value)
  }
}

validateUsername("username") match {
  case Success(username) => println(s"Valid username: $username")
  case Failure(ex)       => println(s"Invalid: '${ex.getMessage}'.")
} 
```

在第二个例子中，我们不抛出而是创建异常实例，因为`Failure`需要一个异常对象。只有在极少数情况下，创建新的异常实例而不立即抛出它们才是可接受的，如果我们遇到创建了异常实例但没有抛出的情况，我们应该警惕。

到目前为止我们学到了什么？

1.  `Option`不起作用，因为它只包装了一种类型，而这种类型应该代表一个有效的结果。
2.  `Either`可以包装两种类型，但是其具体实现的命名`Left`和`Right`缺乏语义。
3.  在验证失败的情况下抛出异常或者使用`Try`作为结果类型也不是一个好主意。

这是否意味着我们运气不好？至于 Scala 2.13，标准库没有提供合适的类型来解决我们的问题。如果我们停在这里，我会建议使用`Either`，因为它是这里最小的邪恶。

### 一种锈状的`Result`型

但是等等，我不会就此止步。Scala 是一个不断发展的项目，所以我提出了一个类似 Rust 的`Result`类型:

```
sealed abstract class Result[+A, +B]

final case class Success[+A, +B](success: A) extends Result[A, B]
final case class Failure[+A, +B](failure: B) extends Result[A, B] 
```

这个简单的、类似存根的实现已经使我们的例子更加简洁:

```
def validateUsername(value: String): Result[String, String] = {
  val min = 4 // minimum legnth for username
  val max = 10 // maximum lenght for username

  val length = value match {
    case s: String => s.length
    case _         => 0
  }

  if (length >= min && length <= max) {
    Success(value)
  } else if (length < min) {
    Failure("'value' is too short.")
  } else {
    Failure("'value' is too long.")
  }
}

validateUsername("usr") match {
  case Success(u) => println(s"A valid username: '$u'");
  case Failure(r) => println(s"Invalid username; reason: '$r'")
} 
```

如果我们想以更复杂的方式使用`Result`，我们可以添加一个`map`函数:

```
sealed abstract class Result[+A, +B] {
  def map[AA](f: A => AA): Option[AA] = this match {
    case Success(a) => Some(f(a))
    case Failure(_) => None
  }
} 
```

这将允许我们只关注快乐的道路:

```
validateUsername("username").map(x => x).foreach(createUserAccount(_)); 
```

在我看来，Scala 中的`Result`类型有很大的潜力，可以使错误处理更简洁、更容易理解。