# 只是为了 func

> 原文：<https://dev.to/projectpage/just-for-func-541j>

如果你错过了这一系列关于学习围棋的第一集，你应该从头开始(见上面的链接)。

今天我们将从函数中获得乐趣。不过，首先，我必须为从著名的 gopher [Francesc Campoy](https://twitter.com/francesc) 那里窃取了今天课程的标题而道歉，他制作了一个非常有趣的播客系列，叫做[只是为了 Func](https://www.youtube.com/channel/UC_BzFbxG2za3bp5NRRRXJSw) -一旦你对围棋有了进一步的了解，你应该看看它们，从另一个角度了解这门语言。

除了今天的 go 函数，我们还将学习三个新的关键词:

*   *const* -不变的值
*   *var* -一个可能改变的值
*   *return*——留下一个函数，给调用者一些输出

如果你在 C 家族中使用过另一种语言，你可能对所有这些都很熟悉。让我们从上次停止的地方开始，使用 main 函数，但是我们将尝试添加另一个可以从 main 调用的函数。我们用上次看到的 func 关键字声明函数，用 const 声明常量，用 var 声明变量(尽管在 Go 中这通常是不必要的，稍后您会看到)。

我们将定义一个函数来寻找一个数字在圆周率中的位置:

```
// piPosition finds the first location of a given number in pi
func piPosition(number int) int {
    ...
    return index
} 
```

这使用 func 来声明一个名为 piPosition 的函数(注意 camel 的情况，这就是我们在 Go 中使用的)，它接受一个 int (integer)类型的数字，并返回一个整数，表示这个数字在 pi 中的索引。

完整的代码在下面或者这里的——我们为 pi 声明了一个常量(注意没有指定类型，这是隐含的)——你可以在关于[常量](https://blog.golang.org/constants)的 go 博客上读到更多关于允许这样做的原因。

然后，我们声明一个整数，给它加 1，跳过一些注释掉的代码(它向您展示了您*不能*做的事情——尝试取消注释，看看会发生什么)，并调用函数 piPosition。piPosition 是一个奇怪而无用的查找索引的小函数，但它将向我们展示函数如何在 Go 中工作以及如何使用 return。在 piPosition 中，我们将 pi 和 number(本例中为 53)都转换为字符串，并在 pi 中找到数字的索引(如果你看得足够长，可以在 pi 中找到大多数数字)。

这里有一个有趣的问题——如果您选择在 printf 中使用说明符%f(将 pi 转换为 float ),您将不会得到 53 的结果，因为 pi 将被截断为适合 float 类型的 number 的大小。这告诉我们一些有趣的事情——pi 常量声明把它变成了 float 以外的东西。

```
// Package main compares strings
package main

// These are the packages imported by this program
import (
    "fmt"
    "strings"
)

// A constant cannot be changed during program execution
const pi = 3.141592653589793238462643383279502884

// the entry point for the program
func main() {

    // A variable stores a value that may change
    var number int = 52

    // We can change number, but not pi
    number = number + 1

    // This is a compile error, try uncommenting to see
    // go is a compiled language and will not allow you to
    // compile a program which does a Bad Thing like this
    // pi = 52

    // We also can't assign a string to a number, try this to see 
    // number = "string"

    // Let's call the function piPosition 
    // and assign its return value to index
    index := piPosition(number)

    // Print something with a format - see Printf for more details.
    fmt.Printf("The number %d is at position %d in pi", number, index)

}

// piPosition finds the index in pi 
// (as a string) of a given number
func piPosition(number int) int {
    // We're going to cheat and use strings
    p := fmt.Sprintf("%v", pi)
    n := fmt.Sprintf("%d", number)

    // A quick search in the standard library docs 
    // turns up this function to find the index in a string
    // let's return this as an answer from the function, 
    // using the return keyword
    return strings.Index(p, n)
} 
```

注意，当我们调用 piPosition 时，我们将结果赋给一个变量，而不使用 var，甚至不使用类型。这就是你如何在 Go 中隐式地创建新的变量，这很方便，所以你会经常看到。变量类型由函数的返回类型设置。

```
 // Let's call the function piPosition 
    // and assign its return value to index
    index := piPosition(number) 
```

你可以在这里尝试今天的代码[https://play.golang.org/p/h82XuiWFaJL](https://play.golang.org/p/h82XuiWFaJL)——注意这个聪明的 URL 被创建为一个散列(一个唯一的字符串，只映射到这个内容)。为了给你的朋友留下深刻印象并挫败你的敌人，这是你将在本课程的后半部分学到的一种聪明的做法。

是的，我正在使用 [fmt](https://golang.org/pkg/fmt) 将数字转换为字符串，并将数字作为字符串进行比较，有经验的 go 程序员不要@我:)你也可以使用标准库中的 [strconv](https://golang.org/pkg/strconv) 包来实现这一点，但我希望事情简单一些。如果你想出了为什么%f 说明符[不起作用](https://play.golang.org/p/UEQ1BnARlqi)，以及 pi 最终是什么类型，请在下面的评论中发表——浮点数是出了名的棘手。

概括一下，到目前为止，我们已经讨论了这 6 个关键词:

*   *包* -包含 go 文件的文件夹
*   *导入* -一种导入 go 包的方式，包括[标准库](https://golang.org/pkg/#stdlib)
*   *func* -这代表[函数](https://en.wikipedia.org/wiki/Function_(mathematics))，一组用于转换数据的指令
*   *const* -不变的值
*   *var* -一个可能改变的值
*   *return*——留下一个函数，给调用者一些输出

再过 19 年你就是专家了！在下一课中，我们将学习如何使用优秀的 Go 标准库，用几行代码建立一个 web 服务器，还将学习更多的关键字，我们将走上在 53words.com 制作下一个 twitter 的道路。

如果你还没有，你应该[下载 go](https://golang.org/dl/) 并使用你的编辑器在本地进行设置——你可以使用这个 hello world 练习在你的计算机上进行测试。我推荐把 VS Code 作为 Mac 上的编辑器，但是还有很多其他的编辑器，而且大多数都支持 go。网上有很多关于安装的[说明](https://www.google.com/search?q=how+do+I+install+golang)，这里就不赘述了。