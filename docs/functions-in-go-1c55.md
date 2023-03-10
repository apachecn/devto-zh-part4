# Go 中的功能

> 原文：<https://dev.to/codetips/functions-in-go-1c55>

[![Functions in Go](img/a95f6e5f78c14f05a281dcbeeefd3c25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c79XrU_9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1529156069898-49953e39b3ac%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在 Go 文章的[条件句的最后，我们以一个打印出你的`name`、`age`、`hobbies`和基于`age`的语句的程序结束。](https://www.codetips.co.uk/languages/go/conditionals-in-go/)

```
package main

import "log"

func main() {

  // Start of our code

    name := "Simon"
    age := 29
    hobbies := [4]string{"example1", "mountain biking", "example2", "example3"}

    log.Printf("Hello World. My name is %s.", name)

    if age < 13 {
        log.Println("I am considered a child")
    } else if age < 20 {
        log.Println("I am considered a teenager")
    } else if age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    log.Printf("I have %d hobbies, and they are: ", len(hobbies))

    for i := 0; i < len(hobbies); i++ {
        log.Println(hobbies[i])
    }

    // End of our code

} 
```

我们现在将添加一些“朋友”,并根据他们的年龄为他们打印基于年龄的声明。

我们已经从上面的 Go 片段中的[条件句得到了我们需要的逻辑，所以我们需要做的就是为我们的朋友复制这个。](https://www.codetips.co.uk/languages/go/conditionals-in-go/)

```
package main

import "log"

func main() {

    // Start of our code

    myName := "Simon"
    myAge := 29

    friendOneName := "David"
    friendOneAge := 17

    friendTwoName := "Bill"
    friendTwoAge := 42

    log.Printf("Hello World. My name is %s.", myName)

    if myAge < 13 {
        log.Println("I am considered a child")
    } else if myAge < 20 {
        log.Println("I am considered a teenager")
    } else if myAge < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    log.Println("I have two friends, and they are as follows:")

    log.Printf("%s: ", friendOneName)

    if friendOneAge < 13 {
        log.Println("I am considered a child")
    } else if friendOneAge < 20 {
        log.Println("I am considered a teenager")
    } else if friendOneAge < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    log.Printf("%s: ", friendTwoName)

    if friendTwoAge < 13 {
        log.Println("I am considered a child")
    } else if friendTwoAge < 20 {
        log.Println("I am considered a teenager")
    } else if friendTwoAge < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    // End of our code

} 
```

让我们来分解一下我们所做的改变:

*   我们把我们的`name`和`age`变量改成了`myName`和`myAge`，这样我们的信息和朋友的信息就有了明显的区别。
*   我们添加了两个朋友(`friendOne`和`friendTwo`)，并在单独的变量中添加了他们的姓名/年龄。
*   我们添加了一个新的打印声明来记录我们有多少朋友。
*   我们记录每个朋友的名字，并运行一个`if/else if/else`语句来打印出适当的基于年龄的语句。

这可能看起来不错，但是应该尽可能避免重复代码。

在上面的代码中，我们有三个`if/else if/else`语句，唯一的区别是被检查的变量(`myAge`、`friendOneAge`或`friendTwoAge`)。

如果我们用这种方法添加了 100 个朋友，然后决定在成年人和养老金领取者之间添加另一个年龄段，会怎么样？

我们必须一个接一个地检查所有的`if/else if/else`语句，以添加新的逻辑。

谢天谢地，我们可以使用[函数](https://www.codetips.co.uk/beginner/what-is-a-function/)来减少这种重复。

在 Go 中声明[函数](https://www.codetips.co.uk/beginner/what-is-a-function/)的语法很简单。事实上，我们已经在我们的程序中实现了——`main`函数:

```
func main() {

} 
```

现在让我们看一个稍微复杂一点的例子；接受两个输入，将它们相乘，并打印结果的函数。

```
func multiply(num1 int, num2 int) {
    log.Println(num1 * num2)
} 
```

让我们来分解一下，一步一步来:

*   我们使用关键字`func`定义了一个新函数，并将其命名为`multiply`。
*   我们指定两个参数(`num1`和`num2`)，并给它们一种类型的`int`。
*   我们声明我们的函数体，它打印了`num1`乘以`num2`(`*`)的结果。

现在我们可以尽可能多地使用它！

```
package main

import "log"

func multiply(num1 int, num2 int) {
    log.Println(num1 * num2)
}

func main() {

    multiply(5, 12) // => 60
    multiply(51, 9) // => 459
    multiply(12, 32) // => 384
    multiply(13, 42) // => 546
    multiply(31, 1) // => 31
    multiply(21, 3) // => 63
    multiply(30, 4) // => 120
    multiply(19, 5) // => 95
    multiply(9, 7) // => 63

} 
```

但是有时候我们不仅仅是想打印到屏幕上。如果我们想用`multiply`的结果除以 10 呢？

为此我们可以使用`return`关键字，它做它所说的；它返回结果。

```
func multiply(num1 int, num2 int) int {
    return num1 * num2
} 
```

我们现在已经修改了函数来返回结果，而不是将结果打印到屏幕上。

您可能已经注意到，我们还在参数声明和函数体之间添加了一个额外的`int`。在 Go 语法中，这是返回类型，并且在从函数返回值时是必需的。

我们将它指定为`int`，因为两个`int`相乘(`num1 * num2`)得到一个`int`。

现在我们返回结果。如果我们想使用它，我们需要把它赋给一个变量:

```
package main

import "log"

func multiply(num1 int, num2 int) int {
    return num1 * num2
}

func main() {

    res1 := multiply(5, 12)
    log.Println(res1) // => 60
    res2 := multiply(51, 9)
    log.Println(res2) // => 459
    res3 := multiply(12, 32)
    log.Println(res3) // => 384
    res4 := multiply(13, 42)
    log.Println(res4) // => 546
    res5 := multiply(31, 1)
    log.Println(res5) // => 31
    res6 := multiply(21, 3)
    log.Println(res6) // => 63
    res7 := multiply(30, 4)
    log.Println(res7) // => 120
    res8 := multiply(19, 5)
    log.Println(res8) // => 95
    res9 := multiply(9, 7)
    log.Println(res9) // => 63

} 
```

目前我们只打印结果，但我们可以用许多不同的方式使用结果:

```
package main

import "log"

func multiply(num1 int, num2 int) int {
    return num1 * num2
}

func main() {

    res1 := multiply(5, 12)
    log.Println(res1 * 10) // => 600
    res2 := multiply(51, 9)
    log.Println(res2 * res1) // => 27540
    res3 := multiply(12, 32)
    log.Println(res3 / 40) // => 9
    res4 := multiply(13, 42)
    log.Println(res4 / 10) // => 54
    res5 := multiply(31, 1)
    log.Println(res5 * 4) // => 124
    res6 := multiply(21, 3)
    log.Println(((res6 * 3) / 3) * 5) // => 315
    res7 := multiply(30, 4)
    log.Println(res7 / 2) // => 60
    res8 := multiply(19, 5)
    log.Println(res8 * 4) // => 380
    res9 := multiply(9, 7)
    log.Println(res9 / 9) // => 7

} 
```

现在让我们用这个新发现的智慧来减少我们节目中的重复。让我们回过头来看看打印我们朋友的代码:

```
log.Printf("%s: ", friendOneName)

if friendOneAge < 13 {
    log.Println("I am considered a child")
} else if friendOneAge < 20 {
    log.Println("I am considered a teenager")
} else if friendOneAge < 70 {
    log.Println("I am considered an adult")
} else {
    log.Println("I am considered a pensioner")
}

log.Printf("%s: ", friendTwoName)

if friendTwoAge < 13 {
    log.Println("I am considered a child")
} else if friendTwoAge < 20 {
    log.Println("I am considered a teenager")
} else if friendTwoAge < 70 {
    log.Println("I am considered an adult")
} else {
    log.Println("I am considered a pensioner")
} 
```

代码完全相同，除了变量名(`friendOneName`、`friendOneAge`、`friendTwoName`和`friendTwoAge`)。让我们把它转换成一个叫做`printAgeBracket`的函数:

```
func printAgeBracket(name string, age int) {
    log.Printf("%s: ", name)

    if age < 13 {
        log.Println("I am considered a child")
    } else if age < 20 {
        log.Println("I am considered a teenager")
    } else if age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }
} 
```

现在让我们在代码中使用它:

```
package main

import "log"

func printAgeBracket(name string, age int) {
    log.Printf("%s: ", name)

    if age < 13 {
        log.Println("I am considered a child")
    } else if age < 20 {
        log.Println("I am considered a teenager")
    } else if age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }
}

func main() {

    // Start of our code

    myName := "Simon"
    myAge := 29

    friendOneName := "David"
    friendOneAge := 17

    friendTwoName := "Bill"
    friendTwoAge := 42

    log.Printf("Hello World. My name is %s.", myName)

    if myAge < 13 {
        log.Println("I am considered a child")
    } else if myAge < 20 {
        log.Println("I am considered a teenager")
    } else if myAge < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    log.Println("I have two friends, and they are as follows:")

    printAgeBracket(friendOneName, friendOneAge)
    printAgeBracket(friendTwoName, friendTwoAge)

    // End of our code

} 
```

代替所有重复的代码，我们用不同的变量名调用我们的`printAgeBracket`函数。

我们甚至可以添加另外三个朋友，没有太多麻烦:

```
package main

import "log"

func printAgeBracket(name string, age int) {
    log.Printf("%s: ", name)

    if age < 13 {
        log.Println("I am considered a child")
    } else if age < 20 {
        log.Println("I am considered a teenager")
    } else if age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }
}

func main() {

    // Start of our code

    myName := "Simon"
    myAge := 29

    friendOneName := "David"
    friendOneAge := 17

    friendTwoName := "Bill"
    friendTwoAge := 42

    friendThreeName := "Charlie"
    friendThreeAge := 12

    friendFourName := "Abby"
    friendFourAge := 24

    friendFiveName := "Edith"
    friendFiveAge := 74

    log.Printf("Hello World. My name is %s.", myName)

    if myAge < 13 {
        log.Println("I am considered a child")
    } else if myAge < 20 {
        log.Println("I am considered a teenager")
    } else if myAge < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }

    log.Println("I have five friends, and they are as follows:")

    printAgeBracket(friendOneName, friendOneAge)
    printAgeBracket(friendTwoName, friendTwoAge)
    printAgeBracket(friendThreeName, friendThreeAge)
    printAgeBracket(friendFourName, friendFourAge)
    printAgeBracket(friendFiveName, friendFiveAge)

    // End of our code

} 
```

现在，如果我们想添加另一个年龄段，我们所要做的就是修改我们的`printAgeBracket`函数；这是一个地方，而不是我们决定在我们的节目中增加多少朋友。

**注意:**以这种方式指定我们的朋友(分开变量为`age`和`name`)，也是不理想的。我们将在以后的文章中对此进行改进。

## 挑战

你可能已经意识到我们没有转换与我们的名字(`myName`)和年龄(`myAge`)相关的代码来使用`printAgeBracket`功能。

那是因为和朋友相比，我们印的东西和自己的名字不一样；“你好世界。我的名字是“而不仅仅是名字。

看看是否可以更新`printAgeBracket`函数来接受一个`Boolean`参数，并根据它有条件地打印任一语句。

如果您需要其他提示，或者想向我们展示您的解决方案，[请联系我们](https://www.codetips.co.uk/contact-us/)，我们将尽力帮助您。

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。