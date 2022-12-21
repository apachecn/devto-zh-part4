# 嘶嘶的嗡嗡声

> 原文：<https://dev.to/codetips/fizz-buzz-13i8>

[![Fizz Buzz](img/6bd5007ea5b33e250455a6e386988d41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FtGeigdx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/08/Webp.net-compress-image-2.jpg)

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。

* * *

在这篇文章中，我们给你带来了一个叫做 Fizz Buzz 的挑战，它是由 [AlgoDaily](https://algodaily.com/) 提供的。

我们建议在阅读我们的解释之前，尝试自己完成挑战，[这里](https://algodaily.com/challenges/fizz-buzz)。

### 挑战

我们得到一个数字`n`。

编写一个[函数](https://www.codetips.co.uk/what-is-a-function/)，根据以下规则返回从 1 到`n`的所有数字的字符串表示:

*   如果是 3 的倍数，就表示为“fizz”。
*   如果是 5 的倍数，就用“嗡嗡”来表示。
*   如果它是三和五的倍数，用“fizzbuzz”来表示。
*   如果都不是，就返回号码本身。

因此，`fizzBuzz(15)`将导致`'12fizz4buzzfizz78fizzbuzz11fizz1314fizzbuzz'`。

### 测试用例

*   期望`fizzBuzz(0)`等于`''`
*   期望`fizzBuzz(1)`等于`'1'`
*   期望`fizzBuzz(15)`等于`'12fizz4buzzfizz78fizzbuzz11fizz1314fizzbuzz'`

了解如何使用以下工具完成这项挑战:

*   Java Script 语言
*   去

这项挑战的解决方案和测试案例可以在我们的 [GitHub 账户](https://github.com/realcodetips)中找到。

* * *

### JavaScript

首先，我们将创建`fizzBuzz`函数和一个[循环](https://www.codetips.co.uk/what-are-loops/)，它打印从`0`到参数值`n`的数字。

```
const fizzBuzz = n => {
  for (let i = 0; i < n; i++) {
    console.log(i)
  }
}

fizzBuzz(4) // prints 0, 1, 2, 3
fizzBuzz(10) // prints 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
fizzBuzz(1) // prints 0 
```

从上面的测试案例中我们已经知道，这不是预期的行为，因为数字应该从`1`开始，而不是从`0`开始。

循环也应该继续，直到我们到达`n`的值，然而它现在停止了一个数字(例如`fizzBuzz(4)`停止在`3`而不是`4`)。

我们可以通过以下方式解决这些问题:

*   在初始化语句(`let i = 1`)中将`i`的值赋给`1`。
*   将条件表达式由“`i`小于`n`”(`i < n`)修改为“`i`小于等于`n`”(`i <= n`)。

```
const fizzBuzz = n => {
  for (let i = 1; i <= n; i++) {
    console.log(i)
  }
}

fizzBuzz(4) // prints 1, 2, 3, 4
fizzBuzz(10) // prints 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
fizzBuzz(1) // prints 1 
```

接下来，让我们算出每个数字是否是 3 和/或 5 的倍数。为此，我们将使用模数(%)运算符:

```
const fizzBuzz = n => {
  for (let i = 1; i <= n; i++) {
    if (i % 3 === 0) {
      console.log(`${i} is a multiple of three`)
    }
    if (i % 5 === 0) {
      console.log(`${i} is a multiple of five`)
    }
  }
}

fizzBuzz(4)
/**
3 is a multiple of three
*/
fizzBuzz(15)
/**
3 is a multiple of three
5 is a multiple of five
6 is a multiple of three
9 is a multiple of three
10 is a multiple of five
12 is a multiple of three
15 is a multiple of three
15 is a multiple of five
*/
fizzBuzz(1) 
/**
*/ 
```

现在，让我们添加一个新的`result`变量，我们将使用它来附加每个数字的正确表示；根据挑战描述:

```
const fizzBuzz = n => {
  let result = ''
  for (let i = 1; i <= n; i++) {
    if (i % 3 === 0) {
      result += 'fizz'
    } else if (i % 5 === 0) {
      result += 'buzz'
    } else {
      result += i
    }
  }
  return result
}

console.log(fizzBuzz(4)) // 12fizz4
console.log(fizzBuzz(15)) // 12fizz4buzzfizz78fizzbuzz11fizz1314fizz
console.log(fizzBuzz(1)) // 1 
```

看起来我们已经完成了，但是还缺少一样东西。在我们继续之前，使用挑战测试用例，看看你是否能找出什么是不对的。

你应该已经看到`fizzbBuzz(15)`没有返回正确的值。原因是第三个挑战要求:

*   如果它是三和五的倍数，用“fizzbuzz”来表示。

我们将十五表示为`fizz`而不是`fizzbuzz`，十五是三和五的倍数，因为我们不会检查一个数字是否同时满足两个条件。现在我们来补充一下:

```
const fizzBuzz = n => {
  let result = ''
  for (let i = 1; i <= n; i++) {
    if (i % 3 === 0 && i % 5 === 0) {
      result += 'fizzbuzz'
    } else if (i % 3 === 0) {
      result += 'fizz'
    } else if (i % 5 === 0) {
      result += 'buzz'
    } else {
      result += i
    }
  }
  return result
}

console.log(fizzBuzz(4)) // 12fizz4
console.log(fizzBuzz(15)) // 12fizz4buzzfizz78fizzbuzz11fizz1314fizzbuzz
console.log(fizzBuzz(1)) // 1 
```

作为最后一步，让我们通过使用变量:
使我们的代码更具可读性，并减少重复

```
const fizzBuzz = n => {
  let result = ''
  for (let i = 1; i <= n; i++) {
    const multipleOfThree = i % 3 === 0
    const multipleOfFive = i % 5 === 0

    if (multipleOfThree && multipleOfFive) {
      result += 'fizzbuzz'
    } else if (multipleOfThree) {
      result += 'fizz'
    } else if (multipleOfFive) {
      result += 'buzz'
    } else {
      result += i
    }
  }
  return result
} 
```

我们现在已经用 JavaScript 完成了 FizzBuzz 挑战！继续阅读，了解如何使用 Go 完成它。

### 去吧

首先，我们将创建`fizzBuzz`函数和一个[循环](https://www.codetips.co.uk/what-are-loops/)，它打印从`0`到参数值`n`的数字。

```
package main

import "fmt"

func fizzBuzz(n int) {
    for i := 0; i < n; i++ {
        fmt.Println(i)
    }
}

func main() {
    fizzBuzz(4) // prints 0, 1, 2, 3
    fizzBuzz(10) // prints 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
    fizzBuzz(1) // prints 0
} 
```

在挑战描述中，当提供的输入为 15 时，我们得到了一个示例输出`12fizz4buzzfizz78fizzbuzz11fizz1314fizzbuzz`。

注意预期输出如何从`1`开始，以`n`的值结束(即`15`或本例中的`fizzbuzz`)。

如果我们看一下我们的`fizzBuzz`函数打印的内容，我们从`0`开始，以少于`n`一个数字结束。

我们可以通过以下方式解决这些问题:

*   在 init 语句(`i := 1`)中将`i`的值赋给`1`。
*   将条件表达式由“`i`小于`n`”(`i < n`)修改为“`i`小于等于`n`”(`i <= n`)。

```
package main

import "fmt"

func fizzBuzz(n int) {
    for i := 1; i <= n; i++ {
        fmt.Println(i)
    }
}

func main() {
    fizzBuzz(4) // prints 1, 2, 3, 4
    fizzBuzz(10) // prints 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
    fizzBuzz(1) // prints 1
} 
```

接下来，让我们算出每个数字是否是 3 和/或 5 的倍数。为此，我们将使用模数(%)运算符:

```
package main

import "fmt"

func fizzBuzz(n int) {
    for i := 1; i <= n; i++ {
        if i%3 == 0 {
            fmt.Printf("%d is a multiple of three\n", i)
        }
        if i%5 == 0 {
            fmt.Printf("%d is a multiple of five\n", i)
        }
    }
}

func main() {
    fizzBuzz(4)
    /**
    3 is a multiple of three
    */
    fizzBuzz(15)
    /**
    3 is a multiple of three
    5 is a multiple of five
    6 is a multiple of three
    9 is a multiple of three
    10 is a multiple of five
    12 is a multiple of three
    15 is a multiple of three
    15 is a multiple of five
    */
    fizzBuzz(1)
    /**
     */
} 
```

现在，让我们添加一个新的`result`变量，我们将使用它来附加每个数字的正确表示；根据挑战描述:

```
package main

import "strconv"

func fizzBuzz(n int) {
    var result string
    for i := 1; i <= n; i++ {
        if i%3 == 0 {
            result += "fizz"
        } else if i%5 == 0 {
            result += "buzz"
        } else {
            result += strconv.Itoa(i)
        }
    }
}

func main() {
    fizzBuzz(4)
    fizzBuzz(15)
    fizzBuzz(1)
} 
```

因为 Go 是一种[强类型](https://www.codetips.co.uk/translation-and-types/)语言，我们必须使用`strconv`包中的`Itoa`方法将`i`的[类型](https://www.codetips.co.uk/what-is-a-data-type/)从`int`转换为`string`，以匹配`result`变量的类型。

接下来，我们需要更新我们的`fizzBuzz`函数来返回我们的`result`变量。为此，我们添加了一个返回类型`string`，并在函数末尾添加了`return`语句。

```
package main

import (
    "fmt"
    "strconv"
)

func fizzBuzz(n int) string {
    var result string
    for i := 1; i <= n; i++ {
        if i%3 == 0 {
            result += "fizz"
        } else if i%5 == 0 {
            result += "buzz"
        } else {
            result += strconv.Itoa(i)
        }
    }
    return result
}

func main() {
    fmt.Println(fizzBuzz(4)) // 12fizz4
    fmt.Println(fizzBuzz(15)) // 12fizz4buzzfizz78fizzbuzz11fizz1314fizz
    fmt.Println(fizzBuzz(1)) // 1
} 
```

看起来我们已经完成了，但是还缺少一样东西。在我们继续之前，使用挑战测试用例，看看你是否能找出什么是不对的。

你应该已经看到`fizzbBuzz(15)`没有返回正确的值。原因是第三个挑战要求:

*   如果它是三和五的倍数，用“fizzbuzz”来表示。

我们将十五表示为`fizz`而不是`fizzbuzz`，十五是三和五的倍数，因为我们不会检查一个数字是否同时满足两个条件。现在我们来补充一下:

```
package main

import (
    "fmt"
    "strconv"
)

func fizzBuzz(n int) string {
    var result string
    for i := 1; i <= n; i++ {
        if i%3 == 0 && i%5 == 0 {
            result += "fizzbuzz"
        } else if i%3 == 0 {
            result += "fizz"
        } else if i%5 == 0 {
            result += "buzz"
        } else {
            result += strconv.Itoa(i)
        }
    }
    return result
}

func main() {
    fmt.Println(fizzBuzz(4)) // 12fizz4
    fmt.Println(fizzBuzz(15)) // 12fizz4buzzfizz78fizzbuzz11fizz1314fizzbuzz
    fmt.Println(fizzBuzz(1)) // 1
} 
```

作为最后一步，让我们通过使用变量:
使我们的代码更具可读性，并减少重复

```
package main

import (
    "fmt"
    "strconv"
)

func fizzBuzz(n int) string {
    var result string
    for i := 1; i <= n; i++ {
        multipleOfThree := i%3 == 0
        multipleOfFive := i%5 == 0

        if multipleOfThree && multipleOfFive {
            result += "fizzbuzz"
        } else if multipleOfThree {
            result += "fizz"
        } else if multipleOfFive {
            result += "buzz"
        } else {
            result += strconv.Itoa(i)
        }
    }
    return result
}

func main() {
    fmt.Println(fizzBuzz(4)) // 12fizz4
    fmt.Println(fizzBuzz(15)) // 12fizz4buzzfizz78fizzbuzz11fizz1314fizzbuzz
    fmt.Println(fizzBuzz(1)) // 1
} 
```

恭喜，我们已经完成了 Go 中的 FizzBuzz 挑战！为什么不尝试使用 JavaScript 来解决这个问题呢？

### 模数(%)运算符

> 模运算找出一个数除以另一个数后的余数。

如果你仍然不明白它的意思，你会被原谅的，那个官方的解释并不清楚，所以让我们来看一些例子。

让我们从一些简单的除法开始；八除以四(`8 / 4`)是多少？这很简单，因为四除以八等于二。

十除以四(`10 / 4`)是多少？这次我们的结果是一个分数，2.5，因为 4 不能精确地乘以 10。

另一种回答十除以四的方法是四除以十两次(`8`)，余数为二(`10 - 8 = 2`)。这就是模量。它返回余数，所以十模四(`10 % 4`)是二。

这怎么能帮助我们呢？嗯，如果我们需要找到能被 3 和/或 5 整除的数字，我们可以检查模/余数值。

例如，要检查一个数是否能被三整除:

*   `1 % 3` -三进一零次，所以余数是一(`1 - 0 = 1`)。
*   `2 % 3` -三进二零次，所以余数是二(`2 - 0 = 2`)。
*   `3 % 3` -三进三一次，所以余数为零(`3 - 3 = 0`)。
*   `4 % 3`——四进三一次，余数一(`4 - 3 = 1`)。

请注意，当数字被 3 整除时，余数是零。因此，我们可以说当余数为零时，这个数是三的倍数。

如果本文中有任何不清楚的地方，请随时[联系我们](https://www.codetips.co.uk/contact-us/)！

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。