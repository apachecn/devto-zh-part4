# Go 上的模块介绍-第 2 部分

> 原文：<https://dev.to/prassee/intro-to-modules-on-go-part-2-ijj>

在之前的[帖子](https://dev.to/prassee/intro-to-modules-on-go-part-1-1k77)中，我们看到了如何初始化模块，编写一个，发布它(本地使用`replace`指令)&在另一个 Go 项目/模块中使用它。这部分我们要把模块发布到 github，去掉`replace`的走样方式。

**发布到 Github**

发布到 github 是很容易的事情，只需初始化一个 git repo 并将其推送到 github。然而，回购的名称在这里很重要，因为我们模块的消费者会使用它作为标识符。

让我们更改`localmod1`上的 go.mod 文件。以便它指向一个正确的 git repo，而不仅仅是一个名称。

```
module github.com/prassee/localmod1

go 1.12 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们把它推到 github。

```
git init . 
git remote add origin git@github.com:prassee/localmod1.git
...
git cm 'initial commit' 
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

现在模块发布到了 [github](https://github.com/prassee/localmod1) 。让我们继续在其他模块中使用它。

**在其他模块中使用**

这一步不需要任何编码，但我们只需要编辑`go.mod`文件。让我们打开`usingmod1`模块的
go.mod 文件。

```
module usingmod1

replace github.com/prassee/localmod1 => ../localmod1

go 1.12

require github.com/prassee/localmod1 v0.0.0-00010101000000-00000000
0000 // indirect 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们只需要用下面的代码替换第二行，更新后的版本应该如下所示。

```
module usingmod1
require github.com/prassee/localmod1 v1.0.2
go 1.12 
```

Enter fullscreen mode Exit fullscreen mode

现在如果我们运行`go mod tidy`。我们可以看到它下载了我们想要的版本。

```
> go mod tidy
go: finding github.com/prassee/localmod1 v1.0.2
go: downloading github.com/prassee/localmod1 v1.0.2
go: extracting github.com/prassee/localmod1 v1.0.2 
```

Enter fullscreen mode Exit fullscreen mode

**注释**

*   在这个练习中，我们从未使用过`go get`命令，也没有任何供应商文件夹来管理我们的依赖项。
*   模块是在`GOPATH`之外编写的。

这就是所有的人....我想我已经介绍了我所知道的关于围棋模块的知识。