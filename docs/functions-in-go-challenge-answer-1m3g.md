# 围棋中的函数(挑战答案)

> 原文：<https://dev.to/codetips/functions-in-go-challenge-answer-1m3g>

[![Functions in Go (challenge answer)](img/184b2f2c53747ce0655af80c2ed2c296.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJ2T5tJ4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1516589091380-5d8e87df6999%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

警告:**挑战剧透警报**

在上一篇文章的末尾([“Go 中的函数”](https://www.codetips.co.uk/functions-in-go/))，我们要求您更新`printAgeBracket`函数以接受一个`Boolean`参数，这样我们就可以有条件地打印一条语句并减少重复。

如果你没有尝试过；我们马上就要给你答案了，所以现在是时候去试试了。

作为参考，以下是我们要求您修改的`printAgeBracket`声明:

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

这是我们想要整合到这个函数中的代码:

```
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
```

唯一阻止我们使用`printAgeBracket`函数的是我们打印出一个不同的语句；`Hello world. My name is: %s` vs `$s:`。

这个问题的一个可能的解决方案是给`printAgeBracket`添加一个新参数，称为`isFriend`，并给我们的函数添加一个`if/else`语句:

```
func printAgeBracket(name string, age int, isFriend bool) {
    if isFriend {
        log.Printf("%s: ", name)
    } else {
        log.Printf("Hello World. My name is %s.", name)
    }

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

然后，我们需要修改使用该函数的行，以使用这个新参数:

```
printAgeBracket(friendOneName, friendOneAge, true)
printAgeBracket(friendTwoName, friendTwoAge, true)
printAgeBracket(friendThreeName, friendThreeAge, true)
printAgeBracket(friendFourName, friendFourAge, true)
printAgeBracket(friendFiveName, friendFiveAge, true) 
```

如果我们新的`isFriend`参数是`true`，我们打印第一条语句，`else`我们打印第二条语句。

如果您自己没有做到这一步，花五分钟看看您是否可以修改下面的代码来使用`printAgeBracket`函数:

```
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
```

完成了吗？太棒了。

您应该得到以下结果:

```
printAgeBracket(myName, myAge, false) 
```

`isFriend`参数是`false`，所以它打印出了`else`语句中的语句，我们刚刚从代码中删除了更多的重复内容。

我们的整个程序现在看起来像这样:

```
package main

import "log"

func printAgeBracket(name string, age int, isFriend bool) {
    if isFriend {
        log.Printf("%s: ", name)
    } else {
        log.Printf("Hello World. My name is %s.", name)
    }

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

    printAgeBracket(myName, myAge, false)

    log.Println("I have five friends, and they are as follows:")

    printAgeBracket(friendOneName, friendOneAge, true)
    printAgeBracket(friendTwoName, friendTwoAge, true)
    printAgeBracket(friendThreeName, friendThreeAge, true)
    printAgeBracket(friendFourName, friendFourAge, true)
    printAgeBracket(friendFiveName, friendFiveAge, true)

    // End of our code

} 
```

我们的新功能减少了代码中的大量重复，并使我们的程序易于维护(例如，添加新的年龄段)，但仍有几处需要改进:

1.  每创建一个新朋友，我们都要创建两个新变量。我们目前只有 15 行代码来创建 5 个朋友。
2.  当我们打印我们有多少朋友(“我有五个朋友，他们如下”)时，我们必须手动添加数字(五)。我们有一个真正的风险，我们可能会添加/删除朋友，而忘记更新这个。

这两件事都降低了我们的可维护性。请继续关注未来的文章，在那里我们将解决这些问题。

如果您需要任何帮助来理解我们在本文中讨论的内容，[请联系我们](https://www.codetips.co.uk/contact-us/)，我们将尽我们所能提供帮助。

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。