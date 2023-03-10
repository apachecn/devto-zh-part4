# go 中的 struts

> 原文：<https://dev.to/codetips/structs-in-go-3jgf>

[![Structs in Go](img/a7b79ee7a984920dbf8117aba5e4a616.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t1lQiT8X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1493476523860-a6de6ce1b0c3%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

在 Go 文章中的[函数的最后，我们删除了大量重复的代码，但是我们仍然有一些重复的代码来打印出`myName`和`myAge`变量。](https://www.codetips.co.uk/functions-in-go/)

我们在 Go(挑战答案)文章的[函数中修复了这种重复，但是我们的代码仍然有点混乱，原因有二:](https://www.codetips.co.uk/functions-in-go-challenge-answer/)

1.  我们必须为每个朋友创建两个新变量。
2.  打印我们有多少朋友是一个手动过程，而不是让我们的程序为我们计算出来。

在本文中，我们将借助结构来解决这两个问题！

### 什么是结构？！

> Go 的结构是字段的类型集合。它们对于将数据组合在一起形成记录很有用。—goby 示例

让我们直接看一个例子来更好地理解这意味着什么。

在 Go(挑战答案)文章的[函数的最后，我们这样声明我们的朋友和我们自己:](https://www.codetips.co.uk/functions-in-go-challenge-answer/)

```
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
```

每个人都有名字和年龄，所以让我们将这些数据组织在一个结构中。用于声明结构的[语法](https://www.codetips.co.uk/what-is-syntax/)很简单:

```
type person struct {
    name string
    age int
} 
```

让我们看一下这个例子做了什么:

*   我们创建了一个新的结构，并将其命名为`person`。
*   我们为我们的`person`结构定义了两个字段:-`string`类型的`name`-`int`类型的`age`

注意，我们在结构定义中使用了关键字`type`。Go 是一种[强类型](https://www.codetips.co.uk/translation-and-types/)语言，我们的新结构也不例外，因此`person`实际上是一种新的[类型](https://www.codetips.co.uk/what-is-a-data-type/)。

```
// built-in type
var age int
// our new person type
var me person 
```

在上面的例子中，我们创建了一个`age`变量，类型为`int`，就像我们以前多次做的那样，但是我们也创建了一个`me`，类型为`person`。

像普通类型一样，有许多方法可以初始化结构。我们只关注两个:

*   方法 1 按照字段值在结构中的定义顺序设置字段值(`name`在前，`age`在后)。
*   方法 2 通过使用标识符(`name`或`age`)设置字段值，并且可以以任何顺序定义。<！-kg-卡-开始:代码- >

```
// Method 1
me := person{"Simon", 28}

// Method 2
me := person{age: 28, name: "Simon"} 
```

出于本文的目的，我们将使用第一种方法。如果您想使用第二种方法，请随意！一切应该还是一样的。

现在，看看你是否能转换下面的代码来使用我们的新的`person`结构，而不是使用两个独立的变量([如果你卡住了就联系我们](https://www.codetips.co.uk/contact-us/))。

```
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
```

完成了吗？您应该得到类似于下面的结果:

```
me := person{"Simon", 29}

friendOne := person{"David", 17}
friendTwo := person{"Bill", 42}
friendThree := person{"Charlie", 12}
friendFour := person{"Abby", 24}
friendFive := person{"Edith", 74} 
```

你现在可能会看到很多错误，因为你有很多“未定义”的变量。这是因为我们使用了不再存在的旧变量名(`friendOneName`、`friendOneAge`等)。

这是现在错误的代码。但是我们怎么解决呢？我们不再有单独的变量用于`name`和`age` …

```
printAgeBracket(myName, myAge, false)

log.Println("I have five friends, and they are as follows:")

printAgeBracket(friendOneName, friendOneAge, true)
printAgeBracket(friendTwoName, friendTwoAge, true)
printAgeBracket(friendThreeName, friendThreeAge, true)
printAgeBracket(friendFourName, friendFourAge, true)
printAgeBracket(friendFiveName, friendFiveAge, true) 
```

对于结构，您可以使用点标记来访问它们的“属性”。这听起来比实际更复杂，让我们来看看实际情况:

```
package main

import "log"

type person struct {
    name string
    age int
}

func main() {

    // Start of our code

    me := person{"Simon", 29}

    log.Printf("Hello, my name is %s and I am %d years old", me.name, me.age)

    // End of our code

} 
```

之所以称之为点符号，是因为变量名和您想要访问的属性(例如`me.name`)之间有一个点(`.`)，它的意思就是“从变量`me`中获取`name`属性”。

你能猜到会印什么吗？

```
2019/06/27 17:34:11 Hello, my name is Simon and I am 29 years old 
```

现在，看看你能否修复下面的代码。**提示:**你只需要修改这几行代码，其他什么都不用做。记住，如果你遇到困难，你可以[联系我们](https://www.codetips.co.uk/contact-us/)。

```
printAgeBracket(myName, myAge, false)

log.Println("I have five friends, and they are as follows:")

printAgeBracket(friendOneName, friendOneAge, true)
printAgeBracket(friendTwoName, friendTwoAge, true)
printAgeBracket(friendThreeName, friendThreeAge, true)
printAgeBracket(friendFourName, friendFourAge, true)
printAgeBracket(friendFiveName, friendFiveAge, true) 
```

您应该得到以下结果:

```
printAgeBracket(me.name, me.age, false)

log.Println("I have five friends, and they are as follows:")

printAgeBracket(friendOne.name, friendOne.age, true)
printAgeBracket(friendTwo.name, friendTwo.age, true)
printAgeBracket(friendThree.name, friendThree.age, true)
printAgeBracket(friendFour.name, friendFour.age, true)
printAgeBracket(friendFive.name, friendFive.age, true) 
```

但是等等！我们不是说`person`是[型](https://www.codetips.co.uk/what-is-a-data-type/)吗？是啊！这意味着我们可以改变函数来接受类型为`person`的参数，而不是分别接受`name`和`age`:

```
func printAgeBracket(p person, isFriend bool) {
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

但是现在我们有更多的错误！让我们看看哪里出了问题:

1.  `printAgeBracket`是指未定义的变量`name`和`age`，因为我们不再将它们作为参数传递。
2.  当我们调用我们的`printAgeBracket`函数时，我们现在传递了太多的参数，以及错误类型的参数。

在我们继续之前，看看您能否修复这些错误。如果你遇到困难，请联系我们。

修好了？让我们看看我们做了什么。

*   为了修复`printAge`函数，我们改变了对`name`和`age`的所有引用，以使用我们的新参数`p`，类型为`person`，使用点符号(即`p.name` & `p.age`)
*   为了解决传递过多参数和错误类型参数的问题，我们更新了函数调用，使用了`person`对象，而不是单独的字段(`person.name`和`person.age`)。

我们的程序现在看起来像这样:

```
package main

import "log"

type person struct {
    name string
    age int
}

func printAgeBracket(p person, isFriend bool) {
    if isFriend {
        log.Printf("%s: ", p.name)
    } else {
        log.Printf("Hello World. My name is %s.", p.name)
    }

    if p.age < 13 {
        log.Println("I am considered a child")
    } else if p.age < 20 {
        log.Println("I am considered a teenager")
    } else if p.age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }
}

func main() {

    // Start of our code

    me := person{"Simon", 29}

    friendOne := person{"David", 17}
    friendTwo := person{"Bill", 42}
    friendThree := person{"Charlie", 12}
    friendFour := person{"Abby", 24}
    friendFive := person{"Edith", 74}

    printAgeBracket(me, false)

    log.Println("I have five friends, and they are as follows:")

    printAgeBracket(friendOne, true)
    printAgeBracket(friendTwo, true)
    printAgeBracket(friendThree, true)
    printAgeBracket(friendFour, true)
    printAgeBracket(friendFive, true)

    // End of our code

} 
```

所以，我们结束了？不，让我们提醒自己我们在本文中说过要解决的两个问题:

1.  我们必须为每个朋友创建两个新变量。
2.  打印我们有多少朋友是一个手动过程，而不是让我们的程序为我们工作。

我们已经修复了第一个问题，但是我们仍然需要手动打印我们的朋友数量。

但是我们如何通过编程来计算我们的朋友数量呢？嗯，答案可能会让你大吃一惊。我们将使用一个[数组](https://www.codetips.co.uk/what-are-arrays/)。

在 Go 文章中的[数组和循环中，我们已经看到了与单独的变量相比，数组是如何提高可维护性的，而且我们已经说过`person`是一个类型，所以我们可以创建一个类型为`person`的数组。](https://www.codetips.co.uk/arrays-and-loops-in-go/)

让我们提醒自己如何创建一个简单的`int`数组:

```
arr := [3]int{1,2,3} 
```

所以创建一个类型为`person`的数组很简单:

```
arr := [0]person{} 
```

或者创建一个数组`person`，初始化 5 个值:

```
friends := [5]person{
    {"David", 17},
    {"Bill", 42},
    {"Charlie", 12},
    {"Abby", 24},
    {"Edith", 74},
} 
```

现在让我们更新代码来使用这个方法，而不是为每个朋友创建一个单独的变量:

```
func main() {

    // Start of our code

    me := person{"Simon", 29}

    friends := [5]person{
        {"David", 17},
        {"Bill", 42},
        {"Charlie", 12},
        {"Abby", 24},
        {"Edith", 74},
    }

    printAgeBracket(me, false)

    log.Println("I have five friends, and they are as follows:")

    printAgeBracket(friendOne, true)
    printAgeBracket(friendTwo, true)
    printAgeBracket(friendThree, true)
    printAgeBracket(friendFour, true)
    printAgeBracket(friendFive, true)

    // End of our code

} 
```

我们现在有了更多未定义的错误，因为我们的个体朋友变量(`friendOne`、`friendTwo`等)不再存在。

现在我们该怎么办？！正如我们从 Go 文章中的[数组和循环中所知，我们可以循环数组中的元素。](https://www.codetips.co.uk/arrays-and-loops-in-go/)

因此，让我们使用相同的方法遍历我们的`friends`数组并调用我们的`printAgeBracket`函数:

```
func main() {

    // Start of our code

    me := person{"Simon", 29}

    friends := [5]person{
        {"David", 17},
        {"Bill", 42},
        {"Charlie", 12},
        {"Abby", 24},
        {"Edith", 74},
    }

    printAgeBracket(me, false)

    log.Println("I have five friends, and they are as follows:")

    for i := 0; i < len(friends); i++ {
        printAgeBracket(friends[i], true)
    }

    // End of our code

} 
```

现在看起来干净多了！我们还可以通过向我们的`friends`数组添加新元素来为我们的程序添加更多的朋友。来吧，试试看！

这很好，但是我们仍然对我们的朋友数量进行硬编码。让我们现在就解决这个问题。

我们已经在使用`len`方法，我们知道它给出了一个数组的长度，所以把我们的`Println`语句改成`Printf`语句并使用数组长度就很简单了:

```
log.Printf("I have %d friends, and they are as follows:", len(friends)) 
```

是的——真的就这么简单！花一些时间来玩代码，并确保您理解我们做了什么。

作为参考，我们的程序现在看起来像这样:

```
package main

import "log"

type person struct {
    name string
    age int
}

func printAgeBracket(p person, isFriend bool) {
    if isFriend {
        log.Printf("%s: ", p.name)
    } else {
        log.Printf("Hello World. My name is %s.", p.name)
    }

    if p.age < 13 {
        log.Println("I am considered a child")
    } else if p.age < 20 {
        log.Println("I am considered a teenager")
    } else if p.age < 70 {
        log.Println("I am considered an adult")
    } else {
        log.Println("I am considered a pensioner")
    }
}

func main() {

    // Start of our code

    me := person{"Simon", 29}

    friends := [5]person{
        {"David", 17},
        {"Bill", 42},
        {"Charlie", 12},
        {"Abby", 24},
        {"Edith", 74},
    }

    printAgeBracket(me, false)

    log.Printf("I have %d friends, and they are as follows:", len(friends))

    for i := 0; i < len(friends); i++ {
        printAgeBracket(friends[i], true)
    }

    // End of our code

} 
```

## 挑战

我们已经走了很长一段路，我们的节目看起来干净多了，但我们还可以做更多的工作来清理它。

比如我们还在为`me`变量和我们的朋友分别做变量。如果我们不用这样就好了。

1.  在我们的`person`结构中创建一个新的字段，类型为`bool`，我们将使用它来控制某人是否是朋友。
2.  使用新字段来控制打印出哪条语句，而不是使用`isFriend`参数。

如果这篇文章中的任何内容没有意义，您需要挑战的提示，或者您只是想向我们展示您的解决方案[联系我们](https://www.codetips.co.uk/contact-us/) -我们希望听到您的意见！

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。