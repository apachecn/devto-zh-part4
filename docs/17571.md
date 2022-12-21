# Java 日常编码问题#005

> 原文：<https://dev.to/awwsmm/java-daily-coding-problem-005-1phg>

[日常编码问题](https://dailycodingproblem.com/)是一个网站，它会每天向你的收件箱发送一个编程挑战。我想向初学者展示如何使用 Java 解决这些问题，所以这将是我的一系列解决方案中的一个。欢迎在评论中把它们挑出来！

### 问题

> `cons(a, b)`构造一个对，`car(pair)`和`cdr(pair)`返回该对的第一个和最后一个元素。比如`car(cons(3, 4))`返回`3`，`cdr(cons(3, 4))`返回`4`。
> 
> 给定`cons`的这个实现:
> 
> ```
> def cons(a, b):
>    def pair(f):
>        return f(a, b)
>    return pair 
> ```
> 
> 执行`car`和`cdr`。

### 策略

*剧透！*不要看下面，除非你想看我的解决方案！

* * *

日常编码问题主要在他们的提示中使用 Python(至少从我目前看到的来看)，所以我们要做的第一件事就是把这个从 Python 翻译成 Java。短语:

> `cons(a, b)`构造一对

...暗示着`cons(a, b)`构造了一个对象，返回该对象的一个实例，让我们称这个对象类为`Pair`。`car()`和`cdr()`，则是将单个`Pair`作为参数并分别返回第一个或第二个值的方法。

Python 可能允许`a`和`b`是任何未指定的类型，但是在 Java 中，我们必须使用泛型或者显式地给`a`和`b`一些类。让我们看看能否用泛型做到这一点。

最后，在`cons(a, b)`内部有一个奇怪的结构。在其中，我们定义了高阶函数`pair`，它将第二个函数`f`作为参数，我们将其应用于`a`和`b`(按此顺序)，而不需要提前知道`f`是什么。注意，`cons(a, b)`并不返回带有任何作为`f`传入的特定参数的`pair(f)`——它返回函数本身`pair`。效果是`cons(a, b)`实际上返回了一个部分定义的函数`f(a, b)`，其中`a`和`b`是已知的，而`f`是未知的。

如果您在 Python shell 中运行这个，您会得到:

```
>>> def cons(a, b):
...     def pair(f):
...         return f(a,b)
...     return pair
... 
>>> cons(1, 2)
<function pair at 0x10edbd050> 
```

Enter fullscreen mode Exit fullscreen mode

注意`cons()`如何返回一个`function`(名为`pair`)。在 Python 中，我们可以将一个已定义的函数或一个 lambda 传递给这个返回值:

```
>>> ret = cons(2, 3)
>>> ret(lambda a, b: a*b)
6
>>> ret(lambda a, b: a+b)
5
>>> def fn(a, b):
...     return a**b
... 
>>> ret(fn)
8 
```

Enter fullscreen mode Exit fullscreen mode

这种行为在 Java 中实现起来会有点棘手。

### 代码

首先，我们看看能否用 Java 定义这个`cons`函数。`cons`是一个接受两个泛型参数并返回的方法...某事:

```
public ??? cons (T a, U b) {
 ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在`cons`中，我们定义了另一个函数`pair`，它将一个 [`function`](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/function/package-summary.html) 作为参数(特别是一个 [`BiFunction`](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/function/BiFunction.html) ，并返回应用于提供给外部`cons`函数的参数的函数:

```
public pair (BiFunction<T,U,R> f) {
  return f(a,b); // a and b defined in cons()
} 
```

Enter fullscreen mode Exit fullscreen mode

不过，这也有一些问题。首先，在 Java 中，你不能像这样在函数中定义函数。相反，我们必须根据功能接口来定义这个内部功能。`pair()`是一个函数，它将另一个函数`f`作为参数，并返回某个值。这使得它成为一个普通的老式 [`Function`](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/util/function/Function.html) :

```
Function<BiFunction<T,U,R>, R> pair = f -> f.apply(a, b); 
```

Enter fullscreen mode Exit fullscreen mode

内部函数的类型签名是`<T,U,R>`，因为我们对函数`f`一无所知。我们只知道它接受两个相同或不同类型的参数(`T`和`U`)，它返回的值可能是其中一种类型，也可能是完全不同的第三种类型(`R`)。但是当我们定义`f`并将其传递给`pair`时，`f`会返回一个`R`类型的值，然后这个值就变成了`pair`的返回值(和类型)。所以`pair`的返回类型也应该是`R`。

将此合并到`cons`中得到:

```
jshell> public class DCP005<T,U,R> {
   ...>   public Function<BiFunction<T,U,R>,R> cons(T a, U b) {
   ...>     Function<BiFunction<T,U,R>,R> pair = f -> f.apply(a,b);
   ...>     return pair;
   ...>   }
   ...> } 
```

Enter fullscreen mode Exit fullscreen mode

...其中`cons`的返回类型就是`pair`的类型，因为那就是`cons`返回的。

注意这是在`jshell`之内。我们可以显式地(非常冗长地)声明所有的类型，比如:

```
jshell> Function<BiFunction<Integer,Integer,Integer>,Integer> exa = (new DCP005<Integer, Integer, Integer>()).cons(2, 3)
exa ==> DCP005$$Lambda$24/85777802@4d41cee 
```

Enter fullscreen mode Exit fullscreen mode

...或者，如果我们乐意忽略警告，只需:

```
jshell> Function exb = (new DCP005()).cons(2, 3)
|  Warning:
|  unchecked call to cons(T,U) as a member of the raw type DCP005
|  Function exb = (new DCP005()).cons(2, 3);
|                 ^-----------------------^
exb ==> DCP005$$Lambda$24/85777802@2833cc44 
```

Enter fullscreen mode Exit fullscreen mode

要应用该方法，我们可以做:

```
jshell> exa.apply((x,y) -> x+y)
$28 ==> 5

jshell> exa.apply((x,y) -> x*y)
$29 ==> 6 
```

Enter fullscreen mode Exit fullscreen mode

...使用`exb`有点笨拙，但是它很有效:

```
jshell> exb.apply((BiFunction)((x,y) -> (Integer)x+(Integer)y))
|  Warning:
|  unchecked call to apply(T) as a member of the raw type java.util.function.Function
|  exb.apply((BiFunction)((x,y) -> (Integer)x+(Integer)y))
|  ^-----------------------------------------------------^
$3 ==> 5

jshell> exb.apply((BiFunction)((x,y) -> (Integer)x*(Integer)y))
|  Warning:
|  unchecked call to apply(T) as a member of the raw type java.util.function.Function
|  exb.apply((BiFunction)((x,y) -> (Integer)x*(Integer)y))
|  ^-----------------------------------------------------^
$4 ==> 6 
```

Enter fullscreen mode Exit fullscreen mode

所以我们需要在某个地方声明*，不管我们是用`cons()`创建`Function`还是用`apply()`调用它。现在，唯一有点尴尬的是我们如何调用`cons`作为`DCP005`对象的成员。我们可以通过将类和方法都声明为`static`来消除这一点，但是我们不能在`jshell`中这样做。相反，让我们创建一个只有一个源文件的 Maven 项目:* 

```
package DCP;

import java.util.function.BiFunction;
import java.util.function.Function;

public class App {

  public static <T,U,R> Function<BiFunction<T,U,R>,R> cons(T a, U b) {
    Function<BiFunction<T,U,R>,R> pair = f -> f.apply(a,b);
    return pair;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

编译这个并在`jshell`中加载包使得这个*变得更加*更好更容易:

```
$ mvn package
...

$ jshell --class-path target/005-1.0-SNAPSHOT.jar
...

jshell> import static DCP.App.*

jshell> cons(2, 3).apply((x,y) -> x+y)
$4 ==> 5

jshell> cons(2, 3).apply((x,y) -> x*y)
$5 ==> 6 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了和 Python 几乎一样的语法！(我认为*的语法也更直观。)这样定义和使用时，Java 会推断传入的对象的类型:* 

```
jshell> cons(2, "3").apply((x,y) -> x)
$9 ==> 2

jshell> cons(2, "3").apply((x,y) -> x + y)
$10 ==> "23" 
```

Enter fullscreen mode Exit fullscreen mode

困难的部分已经解决了，让我们来处理简单的部分:实际解决提示。为此，我们只需要两个函数，它们接受来自`cons()`的返回值，并返回该对中的左成员或右成员。大概是:

```
 public static <T,U> T car(Function<BiFunction<T,U,T>,T> cons) {
    return cons.apply((a,b) -> a);
  }

  public static <T,U> U cdr(Function<BiFunction<T,U,U>,U> cons) {
    return cons.apply((a,b) -> b);
  } 
```

Enter fullscreen mode Exit fullscreen mode

这里的泛型比较复杂，但是`car`基本上是说，提供给它的函数必须接受两个类型为`T`和`U`的对象，并返回一个类型为`T`的对象。`cdr`说提供给它的函数必须接受两个类型为`T`和`U`的对象，并返回一个类型为`U`的对象。

对程序员来说肯定更冗长，但更类型安全。用户的详细程度(在`jshell`中)正好等于 Python 版本(假设我们已经在 Python 中定义了`car`和`cdr`):

```
>>> car(cons(2, "3"))
2

>>> cdr(cons(2, "3"))
'3' 
```

Enter fullscreen mode Exit fullscreen mode

```
jshell> car(cons(2, "3"))
$2 ==> 2

jshell> cdr(cons(2, "3"))
$3 ==> "3" 
```

Enter fullscreen mode Exit fullscreen mode

唯一的区别是我们需要`import`我们用 Java 定义的包。

### 讨论

嗯，那是一次旅行！实际问题非常简单。如果我只用 Python，几分钟就能解决。但我想用 Java 解决这些编码问题，这意味着有时我需要跨语言翻译代码。通常情况下还不算太糟，但是今天涉及到泛型类型时就有点棘手了。在解决这个提示时，我确实学到了一些东西，我希望你也是！

* * *

我的日常编码问题解决方案的所有代码都可以在[github.com/awwsmm/daily](https://github.com/awwsmm/daily)获得。

建议？请在评论中告诉我。