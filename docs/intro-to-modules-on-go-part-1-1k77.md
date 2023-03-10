# Go 上的模块简介-第 1 部分

> 原文：<https://dev.to/prassee/intro-to-modules-on-go-part-1-1k77>

## 关于 Go 的模块介绍-第 1 部分

**什么是 Go 模块？**

从 Go 1.11 开始，引入了“模块”来简化/形式化 Go 项目之间的代码共享，就像其他主流语言一样。

Go 模块是将代码包含到可分发单元中的非常方便的方法。
我们通过使用`go mod`命令将项目声明为模块。这个
创建的模块可以被包含为“依赖”。

**编写 Go 模块**

让我们写一个模块，使一个数字加倍。Go 模块只不过是一个简单的 Go 项目，但是驻留在您的`$GOPATH`之外。Go 模块在项目
中用`go mod`命令初始化。让我们在$GOPATH
之外创建一个名为`localmod1`的目录，并使用命令初始化模块

`mkdir localmod1`

`cd localmod1`

`go mod init localmod1`

上面的步骤创建了一个 go.mod 文件。它包含以下内容。

```
module localmod1

go 1.12 
```

Enter fullscreen mode Exit fullscreen mode

创建一个名为 localmod1.go 的文件，并放入下面的代码。

```
package localmod1

import (
"fmt"
)

func DblX(x int) int {
fmt.Printf("dboubling %v", x)
return x * 2
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的模块已经准备好了一些真正的代码。下一步是消费它，以便为其他模块使用它们做准备。

**在另一个 Go 模块中使用已发布的模块**

模块可以通过两种方式使用，要么从本地使用，要么直接从 github 使用。虽然后者是最推荐的，但是在某些情况下，把它们当作在本地发布是有意义的(也是有用的)。

让我们创建另一个使用`localmod1`的模块，比如说`usingmod1`。

`mkdir usingmod1`

`cd usingmod1`

`go mod init usingmod1`

像上面的步骤之前一样创建一个空的`go.mod`文件如下所示

```
module usingmod1
go 1.12 
```

Enter fullscreen mode Exit fullscreen mode

*   **本地**

现在，让我们通过将“localmod1”添加为依赖项来使用它。因为 localmod1 仍然在本地，但是我们可以假设它已经发布到 github，从而创建了一个指向 localmod1 绝对路径的别名。将以下几行添加到 go.mod

```
 require github.com/prassee/localmod1 v0.0.0
    replace github.com/prassee/localmod1 => ../localmod1 
```

Enter fullscreen mode Exit fullscreen mode

在这里，git repo `github.com/prassee/localmod1`是一个虚构的或尚未创建的位置。添加 let 后，运行`go mod tidy`命令来让我们的依赖定义形式化。升级后的版本如下。

```
 module usingmod1
    replace github.com/prassee/localmod1 => ../localmod1
    go 1.12
    require github.com/prassee/localmod1 v0.0.0-00010101000000-000000000000 // indirect 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在代码中使用导入的模块了。在`main.go`中添加以下几行。

```
 package main

    import (
        "fmt"
        "github.com/prassee/localmod1"
    )
    func main() {
        fmt.Printf("using mod1")
        localmod1.DblX(23)
    } 
```

Enter fullscreen mode Exit fullscreen mode

那是暂时的。在接下来的[部分](https://dev.to/prassee/intro-to-modules-on-go-part-2-ijj)中，我们将探讨如何在 Github 中发布一个模块。

在此之前，请享受 Go 模块...

**参考文献**

我仍然在学习围棋。这里是我用来学习自己编写围棋模块的参考资料。

*   [https://roberto.selbach.ca/intro-to-go-modules/](https://roberto.selbach.ca/intro-to-go-modules/)