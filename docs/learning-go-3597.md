# 学习围棋

> 原文：<https://dev.to/cassidycodes/learning-go-3597>

这个周末我去了一个由丹尼斯·于组织的学习围棋的研讨会。我没能全程留下来，但我学到了一些围棋的基本知识。专注一天学习新东西真的很好。

## GOPATH

让我措手不及的第一件事是 Go 如何寻找 Go 代码。在研讨会上，他们通过建议每个人开始用`~/go`写代码来敷衍这个话题。对我来说，他们这样做是有道理的，这样人们就可以专注于编写代码，而不是配置他们的设置。但是当然，我做的事情不同，这打破了所有的东西！

根据 [Go 语言文档](https://golang.org/cmd/go/#hdr-GOPATH_environment_variable),`GOPATH`环境变量列出了你电脑上所有可以查找 Go 代码的地方。所以当你写`import "mylibrary"`的时候，Go 会在`GOPATH`中列出的所有目录中查找`mylibrary`。

当我有这样的目录结构时:

```
myproject
  main.go
  src
    mylibrary
      mylibrary 
```

Enter fullscreen mode Exit fullscreen mode

Go 找不到我的库！这是因为我的`GOPATH`是空的，而 Go 在默认位置进行查找，默认位置是`~/go`。我的代码在别的地方！

活动中的一位助教说，每次他开始一个新项目时，他都会设置 T0。我决定给它起一个别名，这样当我移动到一个新目录时，我可以快速设置`GOPATH`。

```
# Set GOPATH to current directory and give helpful feedback.
alias setgopath="export GOPATH=`pwd` && echo \"GOPATH is set to $GOPATH\""
# Really tiny alias because I'm lazy.
alias sgp=setgopath 
```

Enter fullscreen mode Exit fullscreen mode

所以现在当我试图运行我的`main.go`代码时，GOPATH 被设置为`myproject`，Go 能够找到`mylibrary`。

*注意*:如果你在不同的系统上， [Go 有关于如何设置你的 GOPATH 的文档](https://github.com/golang/go/wiki/SettingGOPATH)

## `src`是推断！

Go 在`GOPATH`中查找三个目录:

1.  为你的库保存源代码。
2.  为你的库保存编译好的二进制文件。
3.  `pkg`保存编译后的包对象。

有件事我不知道。编译二进制和编译包对象有什么区别？嗯…也许那是改天的博客帖子。

## 一个文件的基本结构。

```
// This line tells you what the package name is. Packages with the name `main` are are usually compiled into a standalone binary. Packages that are meant to be used by other Go programs will have another name, like myStringTools, for example.
package main

// The import statement tells us what packages to import. I'm going to import fmt here because it gives us tools for formatting out put to the console.
import "fmt"

// The entry point! We named our package main, and the entry point matches. When we build this program as a binary, this is the function that will get called.
func main() {
  fmt.PrintLn("Don't go! Stay!")
} 
```

Enter fullscreen mode Exit fullscreen mode

## 语言基础知识

### 值

Go 拥有所有常见的值类型，如字符串、整数、浮点数和布尔值。我发现有趣的一点是字符串不能用单引号声明。他们要么需要使用反斜线(字符串文字)或双引号。

单引号用来表示“[符文](https://devdocs.io/go/builtin/index#rune)”，我不完全明白，但它们似乎以某种方式代表 32 位整数。

### 变量

Go 是一种强类型语言。也就是说，一旦你告诉 Go 某个变量是一个整数，Go 就会期望这个变量总是一个整数。

有两种方法可以声明一个变量:

```
package main

import "fmt"

// Declare the variable and its type in the global scope.
var myInt int
// Assign a value to it!
myInt = 1

// Variables assigned with a capital letter at the beginning will be imported into other files when you use `import`.
var MyExport int

func main() {
  // := lets you declare and assign a value in one line!
  myOtherInt := 1
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`:=`的一个技巧是你不能在全局范围内使用它。您只能在函数内部以这种方式声明和分配变量。

Go 中的值有所谓的零值。这意味着，如果你声明了一个变量，但没有赋值，它就有一个默认值。对于整数，这是零，对于字符串，这是一个空字符串。

### 为

`for`循环看起来很眼熟:

```
package main

import "fmt"

func main() {
  for i := 1; i <= 10; i++ {
    fmt.Println(j)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一部分是循环内部的局部变量。第二部分是条件，在这种情况下，保持循环直到`i`小于或等于 10。第三部分是在循环结束时要做什么。这里我们说的是将`i`加 1。

### 列阵&切片

我认为数组和切片的实现很有趣。数组是编号列表。在像 Ruby 这样的语言中，你可以把数据放在数组的末尾，而不用担心它会变得多大。

在围棋中，你不能这样做。如果我像这样声明一个数组:`var a [5]int`。创建一个包含五个零值整数的数组:`[0,0,0,0,0]`。我不能再向这个数组中添加任何项目了！为什么会这样？程序需要向底层操作系统请求内存空间。如果我要求给五个整数足够的空间，我只能得到这些！需要更多吗？我必须要求更多，然后将我的原始数组复制到新数组。

听起来工作量很大，对吧？我们会检查出一片！

一个切片就像一个数组，但是其中内置了一些繁重的工作。需要在切片末尾添加更多数据？去吧！

要创建切片，您可以使用内置函数`make`，它用于初始化[切片、贴图和变化](https://devdocs.io/go/builtin/index#make)。什么是改变？好问题。但是我将把它留到另一篇博文中，因为我不知道。

```
package main

import(
  "fmt"
  "strings"
)

func main() {
  // Initialize a slice with members.
  mySlice := make([]string, 4)

  mySlice[0] = "My"
  mySlice[1] = "slice"
  mySlice[2] = "of"
  mySlice[3] = "pizza"
  // Append two strings to the slice!
  append(mySlice, "is", "good!")

  fmt.PrintLn(strings.Join(mySlice, " "))
  // => My slice of pizza is good!
} 
```

Enter fullscreen mode Exit fullscreen mode

### 范围

今天的最后一个。范围！我不完全确定我是否掌握了这里的语言，但是范围似乎是迭代器或枚举器。例如，如果您有 slice，您可以使用它在一个`for`循环中迭代数据。

```
package main

import "fmt"

func main() {
  sentence := "My slice of pizza is good."
  words = strings.Split(sentence, " ")

  for index, word := range words {
    fmt.Println(word)
  }
  // => My
  // => slice
  // => of
  // => pizza
  // => is
  // => good.
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我学围棋很开心！这种语言我觉得很熟悉。到目前为止，我发现棘手的事情是理解什么是范围，并计算出我的`GOPATH`。我很高兴我能弄清楚这些事情，并编写一些有用的程序。我敢肯定，如果我更频繁地在围棋中工作，我会发现一些新的围棋被难住了。

你可以通过下面的链接查看我的所有代码。我还将链接到我们正在使用的指南。

我的代号:[https://github.com/cassidycodes/hellogo](https://github.com/cassidycodes/hellogo)

实用围棋:[https://idiomat.gitbook.io/gobridge/](https://idiomat.gitbook.io/gobridge/)