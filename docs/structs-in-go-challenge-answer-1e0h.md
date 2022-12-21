# go 中的 struts _ 挑战答案)

> 原文：<https://dev.to/codetips/structs-in-go-challenge-answer-1e0h>

[![Structs in Go (challenge answer)](img/a0ddec619276feeb7ec8d73e0173daa5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ie9RbO65--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1432821596592-e2c18b78144f%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

警告:**挑战剧透警报**

在“ [Structs in Go](https://www.codetips.co.uk/structs-in-go/) ”文章的最后，我们要求您完成以下任务:

1.  在我们的`person`结构中创建一个新的字段，类型为`bool`，我们将使用它来控制某人是否是朋友。
2.  使用新字段来控制打印出的报表。

如果你没有尝试过；我们马上就要给你答案了，所以现在是时候去试试了。

第一个任务是在我们的`person`结构中添加一个新字段，我们可以用它来控制某人是否是朋友。为了保持一致，我们将这个新字段称为`isFriend`。

```
type person struct {
    name string
    age int
    isFriend bool
} 
```

但是我们现在有了一个`too few values in composite literal`错误，这里我们使用了`person`结构(如下)。

```
me := person{"Simon", 29}

friends := [5]person{
    {"David", 17},
    {"Bill", 42},
    {"Charlie", 12},
    {"Abby", 24},
    {"Edith", 74},
} 
```

这个错误基本上意味着我们没有传递足够的值；我们的`person`结构需要三个属性(`name`、`age`和`isFriend`，但是我们只传递了两个(`name`和`age`)

让我们通过指定`isFriend`属性来解决这个问题。

```
me := person{"Simon", 29, false}

friends := [5]person{
    {"David", 17, true},
    {"Bill", 42, true},
    {"Charlie", 12, true},
    {"Abby", 24, true},
    {"Edith", 74, true},
} 
```

这是完成的第一个任务，现在让我们更新我们的程序，使用新的`isFriend`字段来控制打印出哪个语句。

我们已经将类型为`person`的变量传递给了`printAgeBracket`函数，所以我们已经可以访问`isFriend`属性了。

在`printAgeBracket`函数中，我们需要做的唯一改变是使用新的`isFriend`字段(`p.isFriend`)代替`isFriend`参数。

```
func printAgeBracket(p person, isFriend bool) {
    if p.isFriend { // Change this line
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
```

现在您的程序正在使用新的`isFriend`字段！所以，我们结束了？还没有。

我们的程序运行良好，但是我们留下了我们的`isFriend`参数，尽管我们没有使用它。清理未使用的代码是一个好习惯；这将有助于防止将来的错误，并使代码看起来更干净。

要从`printAgeBracket`函数中移除`isFriend`参数，我们只需将其从函数括号中移除即可:

```
func printAgeBracket(p person) {
    if p.isFriend {
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
```

然后更新调用它的任何东西，以避免任何错误:

```
printAgeBracket(me)

for i := 0; i < len(friends); i++ {
    printAgeBracket(friends[i])
} 
```

请记住，您可以随时[联系我们](https://www.codetips.co.uk/contact-us/)提出任何问题或反馈——我们希望听到您的反馈！

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。