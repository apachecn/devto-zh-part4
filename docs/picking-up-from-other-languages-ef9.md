# 从其他语言中学习

> 原文：<https://dev.to/ivergara/picking-up-from-other-languages-ef9>

在学习 Python 多年后，首先作为业余爱好者，然后在专业环境中，我不得不做一些围棋编程。此外，我一直在尝试探索其他语言，主要是 Rust，但在跟踪一些项目时接触了一些 C 语言([【1】](http://www.buildyourownlisp.com)和[【2】](https://handmadehero.org))。

为了学习而学习其他语言是有价值的，一个人最终可以通过它加深知识并获得启迪。我记得几年前在 MOOC 上做一些 Ruby 的东西时有过这种感觉。在这篇文章中，我想探索我最近学到的一些东西。要全面了解这个主题以及它如何主要反映在 Python 中，这篇文章是一篇很好的有见地的读物。

## 打字

在我刚开始从事 Go 工作的时候，处于编译时强制执行的静态类型语言的领域有点奇怪。这些年来，我对编译语言(Pascal、C、C#、Java)有了一些经验，但从来没有认真地使用过。然而，我很快开始享受静态类型所提供的清晰性，主要是通过类型检查在像 Jetbrain 的 Goland 这样的 ide 中实现的。

在使用 Python 和 Go 时，我发现自己缺少静态类型所提供的清晰性和帮助性。我意识到了`mypy`以及在 Python 中添加一些类型注释的全部努力，但我对此观察入微。这次使用 Go 的经历驱使我开始使用`mypy`。总的来说，我发现它是一个很棒的工具，我正在尽可能地注释它。在对代码进行大规模重构/回收时，这是一个很大的帮助，在其他情况下，它帮助我找到了一些“隐藏”在代码中的潜在错误。另一方面，有时精确地注释参数和变量可能有点棘手。就我个人而言，我没有发现 Python 中的类型系统像社区中的一些人所表达的那样是错误的或特别糟糕，很可能是因为我所做的事情相当简单，并没有真正使用任何元编程或滥用 Python 的动态特性(太多)。

## 从 Go 导入

此外，从 Go 中，我确实学到了更广泛的社区的一些隐含和明确的约定。我想到的最突出的例子是——避免使用无聊的`else`语句([链接](https://medium.com/@matryer/line-of-sight-in-code-186dd7cdea88))——使用`gofmt`将代码格式化为标准——条件赋值([链接](https://www.calhoun.io/one-liner-if-statements-with-errors/))

列表中的第一点在编写 Python 代码时很容易实现。虽然`try-except`块让这个想法的实现稍微缩进了一些。但是我们仍然可以尽可能地使用这种风格模式。

对于第二点，有一些像`autopep8`、`yapf`、`black`这样的工具。我个人倾向于最后一种，因为它被有意设计成用户可以控制的选项非常少。您可能喜欢或不喜欢这些决定，就像使用`gofmt`一样，但是它在不同的代码库之间保持了高度的一致性，并且将您从尝试(和失败)遵循特定的风格中解放出来。我当然更喜欢不必担心和有一些一致性，而不是取悦我个人的代码美学感。在使用了几个月的**黑化**代码后，我很难读懂风格混乱的代码。

现在，关于最后一个，我正在等待 Python 3.8，其中包含了 [*表达式赋值*](https://www.python.org/dev/peps/pep-0572/) 操作符。PEP-572
的一个简单例子

```
# Python 3.7 env_base = os.environ.get("PYTHONUSERBASE", None)
if env_base:
    return env_base

# Python 3.8 if env_base := os.environ.get("PYTHONUSERBASE", None):
    return env_base 
```

Enter fullscreen mode Exit fullscreen mode

这让我很惊讶，因为我从来没有想过我会需要它，但是一旦我在 Go 中使用了它，我就会不断地找到在 Python 中使用它的地方。

## 从 _ 导入

既然这篇文章不叫“将围棋教学引入 Python”之类的东西，那么是时候介绍另一种语言了。正如本文开头所提到的，在我减少的业余时间里，我一直在探索 Rust，因为它似乎是一个非常有用的工具。我不会解释为什么 Rust 很酷，很有价值，但我会提到它有一些非常有趣的东西，涉及到 Python 中的类型注释主题。

Rust 有一个`Result<T, Err>`和`Option<T>`“类型”，用作函数的返回值。它们对返回值的不同可能性进行编码。

在第一种情况下，`Result<T, Err>`，返回一个复合“object ”,它包含一个 T 类型的值或一个错误。不知何故，在我看来，这感觉很像 Go 1。x 负责错误处理。

```
// Go
x, err := double_number("10")
if err {
    fmt.Println("Error: ", err)
} else {
    fmt.Println("Doubled number: ", x)
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// Rust
match double_number("10") {
    Ok(n) => println!("Doubled number: {:?}", n),
    Err(err) => println!("Error: {:?}", err),
} 
```

Enter fullscreen mode Exit fullscreen mode

这在 Python 中没有直接的对等物，因为它有`exceptions`而不是错误值/类型，但是可以通过捕捉异常并返回一些值来创建一个类似于 Go 中的模式。

第二个是`Option<T>`，对 T 类型的值进行编码，或者类似于 Python 中的`None`。以[文档](https://doc.rust-lang.org/std/option/) ( [游乐场](https://play.rust-lang.org/?code=%23!%5Ballow(unused)%5D%0Afn%20main()%20%7B%0Afn%20divide(numerator%3A%20f64%2C%20denominator%3A%20f64)%20-%3E%20Option%3Cf64%3E%20%7B%0A%20%20%20%20if%20denominator%20%3D%3D%200.0%20%7B%0A%20%20%20%20%20%20%20%20None%0A%20%20%20%20%7D%20else%20%7B%0A%20%20%20%20%20%20%20%20Some(numerator%20%2F%20denominator)%0A%20%20%20%20%7D%0A%7D%0A%0A%2F%2F%20The%20return%20value%20of%20the%20function%20is%20an%20option%0Alet%20result%20%3D%20divide(2.0%2C%203.0)%3B%0A%0A%2F%2F%20Pattern%20match%20to%20retrieve%20the%20value%0Amatch%20result%20%7B%0A%20%20%20%20%2F%2F%20The%20division%20was%20valid%0A%20%20%20%20Some(x)%20%3D%3E%20println!(%22Result%3A%20%7B%7D%22%2C%20x)%2C%0A%20%20%20%20%2F%2F%20The%20division%20was%20invalid%0A%20%20%20%20None%20%20%20%20%3D%3E%20println!(%22Cannot%20divide%20by%200%22)%2C%0A%7D%0A%7D) )
为例

```
fn divide(numerator: f64, denominator: f64) -> Option<f64> {
    if denominator == 0.0 {
        None
    } else {
        Some(numerator / denominator)
    }
}

// The return value of the function is an option
let result = divide(2.0, 3.0);

// Pattern match to retrieve the value
match result {
    // The division was valid
    Some(x) => println!("Result: {}", x),
    // The division was invalid
    None    => println!("Cannot divide by 0"),
} 
```

Enter fullscreen mode Exit fullscreen mode

在研究了 Rust 一点之后，我开始更多地使用 Python 中的`Optional`类型注释来明确地表示函数/方法返回值或`None`。当然，在 Python 中返回或接收一个`None`是一件相当频繁的事情，但是将它形式化为一种类型会使它变得非常明确和清晰。以之前移植到 Python 的 Rust 示例为例，它看起来像下面的

```
from typing import Optional

def divide(numerator: float, denominator: float) -> Optional[float]:
    if denominator == 0.0:
        return None
    return numerator / denominator

result = divide(2.0, 3.0)

if result:
    print(f"Result: {result}")
else:
    print("Cannot divide by 0.") 
```

Enter fullscreen mode Exit fullscreen mode

这可能不是最好的例子，但它展示了如何以适当的注释形式应用同样的模式。注释然后运行 mypy 将迫使你覆盖你的基础，从而减少代码中一些愚蠢的错误。

非常有趣的是，我发现[这篇](https://sobolevn.me/2019/02/python-exceptions-considered-an-antipattern)文章展示了这两个想法在 Python 中的某种实现。值得一查，很有意思的实验！我希望在某个时候尝试一下。

此外，`Optional`类型还可以用于参数的参数注释，这些参数可能具有类型或`None`的特定值，当它们将`None`作为缺省值时可能就是这样。

```
from typing import Optional

def fib(n: int) -> int:  # declare n to be int
    a, b = 0, 1
    for _ in range(n):
        b, a = a + b, b
    return a

def cal(n: Optional[int]) -> None:
    print(fib(n)) 
```

Enter fullscreen mode Exit fullscreen mode

如果 mypy 运行了这段代码，它会在`print(fib(n))`行抱怨说`Argument 1 to "fib" has incompatible type "Optional[int]"; expected "int"`。很明显，此时`n`可能是某个整数或者`None`。这迫使我们事先确保`n`实际上是一个数字，从而避免一些不明确的调用和极有可能的运行时错误。因此，通过在详细模式下修改如下的`cal`函数
，可以修复前面代码中的注释错误

```
def cal(n: Optional[int]) -> None:
    if n:
        print(fib(n))
    else:
        print("No number provided!") 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，`mypy`在从事中等规模的项目时是一个很好的工具。Dropbox 和 Instagram 之类的其他公司在从 Python 2 迁移到 Python 3 的过程中发现了它的无价之处。

## 结束语

去探索其他语言吧！它很可能会让你深入了解自己喜欢的语言和自己的编程风格。没有必要成为专家，但是理解一门新语言的挑战已经提供了大量的教导。

关于 Python 本身，使用`black`和注释代码，特别是进入生产和由团队生产的代码。