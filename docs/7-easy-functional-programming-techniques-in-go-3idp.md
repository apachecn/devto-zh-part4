# Go 中 7 个简单的函数式编程技巧

> 原文：<https://dev.to/deepu105/7-easy-functional-programming-techniques-in-go-3idp>

*最初发布于[deepu . tech](https://deepu.tech/functional-programming-in-go/)T3。*

围绕函数式编程(FP)有很多炒作，很多酷孩子都在做，但它不是银弹。像其他编程范例/风格一样，函数式编程也有其优点和缺点，人们可能更喜欢一种范例而不是另一种。如果你是一名 Go 开发人员，并且想尝试函数式编程，不要担心，你不必学习面向函数式编程的语言，比如 Haskell 或 Clojure(甚至 Scala 或 JavaScript，尽管它们不是纯粹的函数式编程语言)，因为 Go 已经涵盖了你，这篇文章就是为你准备的。

如果你正在寻找 Java 中的函数式编程，那么看看这个吧

[![deepu105](img/50afb42a32b9b5d6684585aa01f2857b.png)](/deepu105) [## Java 中的 7 种函数式编程技术

### deepu K Sasidharan 7 月 30 日 1910 分钟阅读

#java #functional #beginners #programming](/deepu105/functional-programming-in-java-a-primer-13nb)

我不会深入所有函数式编程概念的细节，相反，我会把重点放在你可以在 Go 中做的符合函数式编程概念的事情上。我也不会讨论函数式编程的利弊。

* * *

## 什么是函数式编程？

As per Wikipedia,

> 函数式编程是一种编程范式——一种构建计算机程序的结构和元素的风格——将计算视为数学函数的评估，并避免改变状态和可变数据。

因此，在函数式编程中，有两条非常重要的规则

*   **无数据突变**:表示一个数据对象在创建后不应该被改变。
*   **无隐式状态**:应避免隐藏/隐式状态。在函数式编程中，状态并没有被消除，相反，它变得可见和显式

这意味着:

*   **无副作用**:一个功能或操作不应该改变其功能范围之外的任何状态。也就是说，一个函数应该只返回一个值给调用者，而不应该影响任何外部状态。这意味着程序更容易理解。
*   **纯函数唯一**:函数代码是幂等的。函数应该只根据传递的参数返回值，不应该影响(副作用)或依赖全局状态。这样的函数对于相同的参数总是产生相同的结果。

除此之外，下面还有一些函数式编程的概念可以应用到 Go 中，我们将会进一步讨论这些概念。

*   [高阶函数](https://en.wikipedia.org/wiki/Higher-order_function)
*   [关闭](https://en.wikipedia.org/wiki/Closure_(computer_programming))
*   [阿谀奉承](https://en.wikipedia.org/wiki/Currying)
*   [递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))
*   [懒惰评估](https://en.wikipedia.org/wiki/Evaluation_strategy)
*   [参照透明度](https://en.wikipedia.org/wiki/Referential_transparency)

使用函数式编程并不意味着要么全部要么什么都没有，你总是可以在 Go 中使用函数式编程概念来补充面向对象或命令式概念。无论您使用哪种范式或语言，只要有可能，就可以利用函数式编程的好处。这正是我们将要看到的。

* * *

## Go 中的功能编程

Golang 是一种多范式语言，所以让我们看看如何在 Go 中应用上面的一些函数式编程概念。

### 一阶和高阶函数

一等函数(作为一等公民的函数)意味着你可以将函数赋给变量，将一个函数作为参数传递给另一个函数，或者从另一个函数返回一个函数。Go 支持这一点，因此使得闭包、currying 和高阶函数等概念更容易编写。

只有当一个函数接受一个或多个函数作为参数，或者返回另一个函数作为结果时，它才可以被认为是高阶函数。

在围棋中，这很容易做到

```
func main() {
    var list = []string{"Orange", "Apple", "Banana", "Grape"}
    // we are passing the array and a function as arguments to mapForEach method.
    var out = mapForEach(list, func(it string) int {
        return len(it)
    })
    fmt.Println(out) // [6, 5, 6, 5]

}

// The higher-order-function takes an array and a function as arguments
func mapForEach(arr []string, fn func(it string) int) []int {
    var newArray = []int{}
    for _, it := range arr {
        // We are executing the method passed
        newArray = append(newArray, fn(it))
    }
    return newArray
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Go
中闭包和 currying 也是可能的

```
// this is a higher-order-function that returns a function
func add(x int) func(y int) int {
    // A function is returned here as closure
    // variable x is obtained from the outer scope of this method and memorized in the closure
    return func(y int) int {
        return x + y
    }
}

func main() {

    // we are currying the add method to create more variations
    var add10 = add(10)
    var add20 = add(20)
    var add30 = add(30)

    fmt.Println(add10(5)) // 15
    fmt.Println(add20(5)) // 25
    fmt.Println(add30(5)) // 35
} 
```

Enter fullscreen mode Exit fullscreen mode

Go 标准库中也有很多内置的高阶函数。还有一些函数式风格库，如 [this](https://github.com/wesovilabs/koazee) 和 [this](https://github.com/yanatan16/itertools) 在 Go 中提供类似 map-reduce 的函数方法。

### 纯函数

正如我们已经看到的，一个纯函数应该只根据传递的参数返回值，而不应该影响或依赖全局状态。在围棋中很容易做到这一点。

这相当简单，就拿下面这个纯函数来说吧。对于给定的输入，它总是返回相同的输出，并且它的行为是高度可预测的。如果需要，我们可以安全地缓存该方法。

```
func sum(a, b int) int {
    return a + b
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在这个函数中添加额外的一行，行为将变得不可预测，因为它现在具有影响外部状态的副作用。

```
var holder = map[string]int{}

func sum(a, b int) int {
    c := a + b
    holder[fmt.Sprintf("%d+%d", a, b)] = c
    return c
} 
```

Enter fullscreen mode Exit fullscreen mode

所以尽量保持你的函数简单明了。

### 递归

函数式编程更喜欢递归而不是循环。让我们看一个计算一个数的阶乘的例子。

在传统的迭代方法中:

```
func factorial(num int) int {
    result := 1
    for ; num > 0; num-- {
        result *= num
    }
    return result
}

func main() {
    fmt.Println(factorial(20)) // 2432902008176640000
} 
```

Enter fullscreen mode Exit fullscreen mode

使用下面的递归也可以做到这一点，递归在函数式编程中很受欢迎。

```
func factorial(num int) int {
    if num == 0 {
        return 1
    }
    return num * factorial(num-1)
}
func main() {
    fmt.Println(factorial(20)) // 2432902008176640000
} 
```

Enter fullscreen mode Exit fullscreen mode

递归方法的缺点是，与迭代方法相比，它在大多数情况下会更慢(我们的目标是代码的简单性和可读性)，并且可能会导致堆栈溢出错误，因为每个函数调用都需要作为一个帧保存到堆栈中。为了避免这种尾部递归是优选的，特别是当递归进行太多次时。在尾递归中，递归调用是函数执行的最后一件事，因此编译器不需要保存函数堆栈帧。大多数编译器可以像优化迭代代码一样优化尾部递归代码，从而避免性能损失。遗憾的是，Go 编译器不做这种优化。

现在使用尾部递归，同样的函数可以写成如下，但是 Go 并没有对此进行优化，尽管有[的变通方法](https://www.reddit.com/r/golang/comments/3vndco/why_no_tail_call_elimination/cxphgqe/)，它在基准测试中仍然表现得更好。

```
func factorialTailRec(num int) int {
    return factorial(1, num)
}

func factorial(accumulator, val int) int {
    if val == 1 {
        return accumulator
    }
    return factorial(accumulator*val, val-1)
}

func main() {
    fmt.Println(factorialTailRec(20)) // 2432902008176640000
} 
```

Enter fullscreen mode Exit fullscreen mode

我用这三种方法运行了一些基准测试，结果如下，正如你所看到的，循环仍然是性能最好的，其次是尾部递归。

```
goos: linux
goarch: amd64
BenchmarkFactorialLoop-12           100000000           11.7 ns/op         0 B/op          0 allocs/op
BenchmarkFactorialRec-12            30000000            52.9 ns/op         0 B/op          0 allocs/op
BenchmarkFactorialTailRec-12        50000000            44.2 ns/op         0 B/op          0 allocs/op
PASS
ok      _/home/deepu/workspace/deepu105.github.io/temp  5.072s
Success: Benchmarks passed. 
```

Enter fullscreen mode Exit fullscreen mode

为了可读性和不变性，考虑在编写 Go 代码时使用递归，但是如果性能很重要或者迭代次数很大，就使用标准循环。

### 懒评

惰性求值或非严格求值是将表达式的求值延迟到需要时进行的过程。一般来说，Go 会进行严格/急切的求值，但是对于像`&&`和`||`这样的操作数，它会进行惰性求值。我们可以利用高阶函数、闭包、goroutines 和通道来模拟惰性求值。

以 Go 急切评估一切为例。

```
func main() {
    fmt.Println(addOrMultiply(true, add(4), multiply(4)))  // 8
    fmt.Println(addOrMultiply(false, add(4), multiply(4))) // 16
}

func add(x int) int {
    fmt.Println("executing add") // this is printed since the functions are evaluated first
    return x + x
}

func multiply(x int) int {
    fmt.Println("executing multiply") // this is printed since the functions are evaluated first
    return x * x
}

func addOrMultiply(add bool, onAdd, onMultiply int) int {
    if add {
        return onAdd
    }
    return onMultiply
} 
```

Enter fullscreen mode Exit fullscreen mode

这将产生下面的输出，我们可以看到两个函数总是被执行

```
executing add
executing multiply
8
executing add
executing multiply
16 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用高阶函数将它重写为一个延迟评估版本

```
func add(x int) int {
    fmt.Println("executing add")
    return x + x
}

func multiply(x int) int {
    fmt.Println("executing multiply")
    return x * x
}

func main() {
    fmt.Println(addOrMultiply(true, add, multiply, 4))
    fmt.Println(addOrMultiply(false, add, multiply, 4))
}

// This is now a higher-order-function hence evaluation of the functions are delayed in if-else
func addOrMultiply(add bool, onAdd, onMultiply func(t int) int, t int) int {
    if add {
        return onAdd(t)
    }
    return onMultiply(t)
} 
```

Enter fullscreen mode Exit fullscreen mode

输出如下，我们可以看到只执行了所需的功能

```
executing add
8
executing multiply
16 
```

Enter fullscreen mode Exit fullscreen mode

也有其他的方法可以做到这一点，使用同步和期货，如[这个](https://blog.merovius.de/2015/07/17/lazy-evaluation-in-go.html)，使用渠道和 goroutines，如[这个](https://gist.github.com/nickcarenza/0ac9057ad06036d1e934)。在大多数情况下，在 Go 中进行延迟求值可能不值得增加代码的复杂性，但是如果所讨论的函数在处理方面很繁重，那么延迟求值是绝对值得的。

### 类型系统

Go 有很强的类型系统，也有相当不错的类型推理。与其他函数式编程语言相比，唯一缺少的是 case 类和模式匹配。

### 参照透明

来自维基百科:

> 函数式程序没有赋值语句，也就是说，函数式程序中的变量值一旦定义就永远不会改变。这消除了任何副作用的可能性，因为任何变量都可以在任何执行点用它的实际值替换。因此，函数式程序在引用上是透明的。

不幸的是，在 Go 中严格限制数据突变的方法并不多，但是通过使用纯函数，以及通过使用我们前面看到的其他概念明确地避免数据突变和重新分配，这是可以实现的。默认情况下，Go 按值传递变量，但切片和贴图除外。因此，尽可能避免通过引用(使用指针)传递它们。

例如，当我们通过引用传递参数时，下面的代码会改变外部状态，因此不能确保引用的透明性

```
func main() {
    type Person struct {
        firstName string
        lastName  string
        fullName  string
        age       int
    }
    var getFullName = func(in *Person) string {
        in.fullName = in.firstName + in.lastName // data mutation
        return in.fullName
    }

    john := Person{
        "john", "doe", "", 30,
    }

    fmt.Println(getFullName(&john)) // johndoe
    fmt.Println(john) // {john doe johndoe 30}
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们通过值传递参数，我们可以确保引用的透明性，即使在函数
中传递的数据有意外的变化

```
func main() {
    type Person struct {
        firstName string
        lastName  string
        fullName  string
        age       int
    }
    var getFullName = func(in Person) string {
        in.fullName = in.firstName + in.lastName
        return in.fullName
    }

    john := Person{
        "john", "doe", "", 30,
    }

    fmt.Println(getFullName(john))
    fmt.Println(john)
} 
```

Enter fullscreen mode Exit fullscreen mode

当传递的参数是地图或切片时，我们不能依赖于此。

### [数据结构](https://en.wikipedia.org/wiki/Purely_functional_data_structure)

当使用函数式编程技术时，鼓励使用函数式数据类型，如堆栈、映射和队列。因此，在函数式编程中，作为数据存储，映射比数组或散列集更好。

* * *

## 结论

对于那些试图在 Go 中应用一些函数式编程技术的人来说，这只是一个介绍。在 Go 中可以做的事情还有很多，随着下一个主要版本中泛型的加入，这应该会更加容易。正如我前面说过的，函数式编程不是银弹，但它提供了许多有用的技术，使代码更容易理解、维护和测试。它可以与命令式和面向对象的编程风格完美共存。事实上，我们都应该利用一切最好的东西。

* * *

我希望你觉得这是有用的。如果你有任何问题，或者如果你认为我错过了什么，请添加评论。

如果你喜欢这篇文章，请留下赞或评论。

你可以在 [Twitter](https://twitter.com/deepu105) 和 [LinkedIn](https://www.linkedin.com/in/deepu05/) 上关注我。