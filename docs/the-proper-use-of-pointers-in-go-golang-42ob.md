# 围棋中指针的正确使用

> 原文：<https://dev.to/bootdotdev/the-proper-use-of-pointers-in-go-golang-42ob>

围棋近年来越来越受欢迎，尤其是在我所在的地方。Go 一直在取代其他后端语言，如 Ruby、Python、C#和 Java。Go 因其简单性、明确性、速度和低内存消耗而受到青睐。

许多不熟悉这种语言的开发人员，或者不熟悉可以直接使用指针处理内存的语言的开发人员，最终都会误用这些指针。

## 什么是指针？

指针是存储值的地址的变量，而不是值本身。如果将计算机的内存(RAM)想象成一个 JSON 对象，指针就像是键，普通变量就是值。

```
{
  "pointer": "variableValue"
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看一个例子:

```
package main

import "fmt"

func main() {
        // create a normal string variable
    name := "original"
        // pass in a pointer to the string variable using '&'
    setName(&name, "qvault")
    fmt.Println(name)
}

func setName(ptr *string, newName string) {
        // dereference the pointer so we can modify the value
        // and set the value to "qvault"
    *ptr = newName
} 
```

Enter fullscreen mode Exit fullscreen mode

这张照片:

```
qvault 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，因为我们有一个指向变量地址的指针，我们可以修改它的值，甚至在另一个函数的范围内。如果值不是一个指针，这将不起作用:

```
package main

import "fmt"

func main() {
    name := "original"
    setNameBroken(name, "qvault")
    fmt.Println(name)
}

func setNameBroken(ptr string, newName string) {
    ptr = newName
} 
```

Enter fullscreen mode Exit fullscreen mode

印刷品:

```
original 
```

Enter fullscreen mode Exit fullscreen mode

指针可能是有用的，但同样地，它们也可能是危险的。例如，如果我们取消引用一个没有值的指针，程序将会崩溃。由于这个原因，我们总是在打印之前检查错误值是否为零。

## 语法

**1。创建指针:&**

```
newString := ""
newStringPointer := &newString 
```

Enter fullscreen mode Exit fullscreen mode

如果你打印这个指针，你会看到一个内存地址。

```
package main

import "fmt"

func main() {
    newString := ""
    newStringPointer := &newString
    fmt.Println(newStringPointer)
} 
```

Enter fullscreen mode Exit fullscreen mode

印花:0xc00000e1e0

也就是你机器中那个变量的内存地址。

**2。描述指针:***

在函数签名或类型定义中，*用于表示值是指针。

```
func passPointer(pointer *string) {
} 
```

Enter fullscreen mode Exit fullscreen mode

**3。取消对指针的引用:***

这可能有点令人困惑，但是*是用来描述指针的，它也用作取消引用指针的操作符。

```
func derefPointer(pointer *string) {
    newStringVariable := *pointer
        // newStringVariable is just a normal string
} 
```

Enter fullscreen mode Exit fullscreen mode

## 什么时候应该使用指针？

可能有许多微妙的情况表明指针是一个好主意，但我猜想 95%的情况下使用指针都是出于以下原因:

**1。使其参数之一发生变异的函数**

当我调用一个以指针为参数的函数时，我希望我的变量会发生突变。如果你没有改变函数中的变量，那么你可能不应该使用指针。

**2。更好的性能**

[![](img/97614d84b4a2b0d8f07f0a526d3736b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qi9M-Lg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/09/photo-1498084393753-b411b2d26b34-1024x576.jpg)

如果在内存中有一个包含整部小说的字符串，那么每次将它传递给新函数时，复制该变量的开销会非常大。可能值得传递一个指针，这样可以节省 CPU 和内存。然而，这是以牺牲可读性为代价的，所以只有在必要时才进行这种优化。

**3。需要一个零值选项**

有时函数需要知道某个东西的值是多少，以及它是否存在。我通常在读 JSON 的时候用这个来知道一个字段是否存在。例如，如果一个 JSON 对象是:

```
{ "name": "qvault" } ----> *name: "qvault"

{ "name": "" } ----------> *name: ""

{} ----------------------> *name: nil 
```

Enter fullscreen mode Exit fullscreen mode

## 感谢

这些是在代码中何时使用指针的一些经验法则。如果你不确定，一个正常值就可以了，我建议不要用指针。指针是有用的工具，但是很容易导致讨厌的错误或者不可读的代码。

由莱恩·瓦格纳 [@wagslane](https://twitter.com/wagslane)

下载 q vault:[https://q vault . io](https://qvault.io/)

星我们的 Github:[https://github.com/q-vault/qvault](https://github.com/q-vault/qvault)

围棋(golang) 中正确使用指针的帖子[最早出现在](https://qvault.io/2019/09/25/the-proper-use-of-pointers-in-go-golang/) [Qvault](https://qvault.io) 上。