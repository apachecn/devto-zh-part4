# TPP 主题 25:断言编程

> 原文：<https://dev.to/steadbytes/tpp-topic-25-assertive-programming-10oa>

> 这篇文章最初出现在[steadbytes.com](https://steadbytes.com/blog/the-pragmatic-programmer-20th/topic-25-exercises/)
> 
> 见实用程序员 20 周年纪念版系列[第一帖](https://dev.to/steadbytes/the-pragmatic-programmer-20th-anniversary-edition-series-1e2l)介绍。

## 习题 16

> 快速现实核查。这些“不可能”的事情中，哪些可能发生？
> 
> 1.  少于 28 天的一个月
> 2.  系统调用的错误代码:无法访问当前目录
> 3.  在 C++中:`a = 2; b = 3;`但是`(a + b)`不等于`5`
> 4.  内角和≠180°的三角形
> 5.  没有 60 秒的一分钟
> 6.  `(a + 1) <= a`

所有人！

1.  1752 年，英国从使用儒略历改为公历。这需要 11 天的修正时间；导致九月只有 **19 天**。
2.  谈到文件系统，没有什么是确定的(看看丹·卢的精彩演讲[文件充满危险](https://danluu.com/deconstruct-files/))。仅举几个*的例子来说明这个特殊错误的原因:目录可能已经被另一个进程删除了，进行系统调用的进程可能没有访问目录的权限，或者目录所在的驱动器可能已经断开。*
3.  如果不小心的话，并发可能会导致在执行`(a + b)`之前更新`a`。
4.  在曲线几何中，三角形的内角总和不等于 180 度。在[椭圆几何](https://en.wikipedia.org/wiki/Elliptic_geometry)中，总和将大于 180，而在[双曲线几何](https://en.wikipedia.org/wiki/Hyperbolic_geometry)中，总和将小于 180。
5.  日期和时间也充满了危险！在这种情况下，[闰秒](https://en.wikipedia.org/wiki/Leap_second)会导致分钟有 59 或 61 秒。
    *   注意:在撰写本文时，所有闰秒都是正数——导致一分钟有 61 秒。如果地球自转速度加快而不是像现在这样减慢，就会出现负闰秒。
6.  [整数溢出](https://en.wikipedia.org/wiki/Integer_overflow)可能导致`a + b`的值为负。发生溢出的确切情况取决于所使用的语言和编译器/解释器；Rosetta 代码上的[整数溢出](https://rosettacode.org/wiki/Integer_overflow)页面有来自许多不同语言的例子。对于这个具体的例子，我使用 Go ( [GitHub](https://github.com/SteadBytes/study/blob/master/the-pragmatic-programmer-20th/25/exercises/16/overflow.go) )给出一个例子:

```
// overflow.go

package main

import "fmt"

func main() {
    var a int32 = 2147483647
    fmt.Printf("a = %d\n", a)
    fmt.Printf("(a + 1) <= a = false, got %t\n", (a+1) <= a)
    fmt.Printf("(%d + 1) = 21474836478, got %d\n", a, a+1)
} 
```

输出:

```
$ go run overflow.go
a = 2147483647
(a + 1) <= false, got true
(2147483647 + 1) = 21474836478, got -2147483648

Program exited. 
```