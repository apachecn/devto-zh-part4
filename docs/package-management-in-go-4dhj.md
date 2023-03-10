# Go 中的包管理

> 原文：<https://dev.to/deepsource/package-management-in-go-4dhj>

包管理是 Go 一直缺少的东西之一。以前版本(1.11 之前)的一个主要缺点是缺乏对管理依赖版本和实现可重现构建的支持。社区已经开发了包管理器和工具，像 Glide、dep 和其他许多作为版本依赖的实际解决方案。

> "我使用 go get 进行生产构建."——说从来没有人。

Go 对包管理的实现可以追溯到 Google(它有一个巨大的存储库来存放所有的源代码)。让我们来分析一下“预运行模块”包管理工具有什么问题。

1.  版本依赖关系
2.  供应商依赖性
3.  `GOPATH`的必要性

## 版本依赖

`go get`默认不支持模块版本化。go 包管理的第一个版本背后的想法是——不需要模块版本化，不需要第三方模块库，您可以从您当前的分支构建一切。

在 Go 1.11 之前，添加依赖项意味着在您的`GOPATH`中克隆该依赖项的源代码 repo。大概就是这样。没有版本的概念。相反，在克隆时，它总是指向当前的主分支。当不同的项目需要依赖关系的不同版本时，另一个主要问题出现了——这也是不可能的。

## 卖命依赖

包出售通常是指依赖包存储在与项目相同的位置。这通常意味着您的依赖项被签入到您的源代码管理系统中，比如 Git。

考虑这种情况——A 使用依赖项 B，依赖项 B 使用了 C 1.5 版本中引入的依赖项 C 的一个特性，B 必须能够确保 A 的构建使用 C 1.5 或更高版本。在 Go 1.5 之前，如果不重写导入路径，就没有机制在命令旁边携带依赖代码。

## 必然性`GOPATH`

`GOPATH`存在有两个主要原因:

1.  在 Go 中，`import`声明通过完全限定的导入路径引用一个包。`GOPATH`的存在使得 go 工具可以从`GOPATH/src`中的任何目录计算出相关包的绝对导入路径。
2.  存储由`go get.`获取的依赖项的位置

这有什么不好？

1.  不允许在选择的目录中检查项目的源代码，就像他们在其他语言中习惯的那样。
2.  另外，`GOPATH`不允许开发人员同时签出一个以上的项目副本(或者它的依赖项)。

## 介绍 Go 模块

Go 1.11 引入了对 Go 模块的初步支持。来自 Go Wiki，

> 一个*模块*是一个相关 Go 包的集合，这些包作为一个单元一起被版本化。模块记录精确的依赖需求，并创建可重复的构建。

Go 模块内置了三个重要特性，

1) `go.mod`文件类似于`package.json`或`Pipfile`。

2)机器生成的可传递依赖描述- `go.sum`。

3)不再有`GOPATH`限制。模块可以在任何路径中。

```
$ go help mod
Go mod provides access to operations on modules.

Note that support for modules is built into all the go commands,
not just 'go mod'. For example, day-to-day adding, removing, upgrading,
and downgrading of dependencies should be done using 'go get'.
See 'go help modules' for an overview of module functionality.

Usage:

    go mod <command> [arguments]

The commands are:

    download    download modules to local cache
    edit        edit go.mod from tools or scripts
    graph       print module requirement graph
    init        initialize new module in current directory
    tidy        add missing and remove unused modules
    vendor      make vendored copy of dependencies
    verify      verify dependencies have expected content
    why         explain why packages or modules are needed

Use "go help mod <command>" for more information about a command. 
```

相关[讨论线程](https://groups.google.com/forum/#!topic/golang-dev/a5PqQuBljF4)。

## 迁移到 Go 模块

要使用 Go 模块，请将 Go 更新到版本`>= 1.11`。由于`GOPATH`即将退出，用户可以通过以下两种方式之一激活模块支持:

*   在`GOPATH/src`树之外的目录中调用`go`命令，当前目录中有一个有效的`go.mod`文件。
*   如果源在`GOPATH`下，Go 模块不起作用。要覆盖这种行为，调用设置了`GO111MODULE=on`环境变量的`go`命令。

让我们按照这些简单的步骤开始移植:

*   由于不再需要`GOPATH`，将模块移出`GOPATH`。

*   从项目根，创建初始模块定义- `go mod init github.com/username/repository`。最好的部分是，`go mod`自动转换来自现有包管理器的依赖关系，如`dep`、`Gopkg`、`glide`和[以及其他六个](https://tip.golang.org/pkg/cmd/go/internal/modconv/?m=all#pkg-variables)。这将创建一个名为`go.mod`的文件，其中包含模块名及其版本的依赖关系。

```
$ cat go.mod
module github.com/deepsourcelabs/cli

go 1.12

require (
    github.com/certifi/gocertifi v0.0.0-20190410005359-59a85de7f35e
    github.com/getsentry/raven-go v0.2.0
    github.com/pkg/errors v0.0.0-20190227000051-27936f6d90f9 
```

*   运行`go build`创建一个`go.sum`文件，该文件包含特定模块版本内容的预期加密校验和。这是为了确保这些模块的未来下载检索与第一次下载相同的位。请注意，go.sum 不是锁文件。

```
$ cat go.sum
github.com/certifi/gocertifi v0.0.0-20190410005359-59a85de7f35e h1:9574pc8MX6rF/QyO14SPHhM5KKIOo9fkb/1ifuYMTKU=
github.com/certifi/gocertifi v0.0.0-20190410005359-59a85de7f35e/go.mod h1:GJKEexRPVJrBSOjoqN5VNOIKJ5Q3RViH6eu3puDRwx4=
github.com/getsentry/raven-go v0.2.0 h1:no+xWJRb5ZI7eE8TWgIq1jLulQiIoLG0IfYxv5JYMGs=
github.com/getsentry/raven-go v0.2.0/go.mod h1:KungGk8q33+aIAZUIVWZDr2OfAEBsO49PX4NzFV5kcQ=
github.com/pkg/errors v0.0.0-20190227000051-27936f6d90f9 h1:dIsTcVF0w9viTLHXUEkDI7cXITMe+M/MRRM2MwisVow=
github.com/pkg/errors v0.0.0-20190227000051-27936f6d90f9/go.mod h1:bwawxfHBFNV+L2hUp1rHADufV3IMtnDRdf1r5NINEl0= 
```

> 版本说明:为了保持向后兼容性，如果模块是版本 v2 或更高版本，模块的主要版本*必须作为`/vN`包含在`go.mod`文件中使用的模块路径的末尾*(例如`module github.com/username/repository/v2`

## 日常命令

### 列出依赖关系

`go list -m all`列出当前模块及其所有依赖关系。

```
$ go list -m all
github.com/deepsourcelabs/cli
github.com/certifi/gocertifi v0.0.0-20190410005359-59a85de7f35e
github.com/getsentry/raven-go v0.2.0
github.com/pkg/errors v0.0.0-20190227000051-27936f6d90f9 
```

> 在`go list`输出中，当前模块，也称为*主模块*，总是第一行，后面是按照模块路径排序的依赖关系。

### 列出一个包的可用版本

列出软件包的可用版本。

```
$ go list -m -versions github.com/getsentry/raven-go
github.com/getsentry/raven-go v0.1.0 v0.1.1 v0.1.2 v0.2.0 
```

### 添加依赖关系

添加依赖项是隐式的。在代码中导入一个依赖项后，运行`go build`或`go test`命令获取模块的最新版本，并将其添加到`go.mod`文件中。如果您想显式添加一个依赖项，请运行`go get github.com/username/repository`。

### 升级/降级依赖关系

`go get github.com/username/repository@vx.x.x`下载并设置依赖关系的具体版本，并更新`go.mod`文件。

```
$ go get github.com/getsentry/raven-go@v0.1.2
go: finding github.com/getsentry/raven-go v0.1.2
go: downloading github.com/getsentry/raven-go v0.1.2
go: extracting github.com/getsentry/raven-go v0.1.2 
```

```
$ cat go.mod 
module github.com/deepsourcelabs/marvin-go

go 1.12

require (
    github.com/certifi/gocertifi v0.0.0-20190410005359-59a85de7f35e
    github.com/getsentry/raven-go v0.1.2
    github.com/pkg/errors v0.0.0-20190227000051-27936f6d90f9
) 
```

```
$ cat go.sum
github.com/certifi/gocertifi v0.0.0-20190410005359-59a85de7f35e h1:9574pc8MX6rF/QyO14SPHhM5KKIOo9fkb/1ifuYMTKU=
github.com/certifi/gocertifi v0.0.0-20190410005359-59a85de7f35e/go.mod h1:GJKEexRPVJrBSOjoqN5VNOIKJ5Q3RViH6eu3puDRwx4=
github.com/getsentry/raven-go v0.1.2 h1:4V0z512S5mZXiBvmW2RbuZBSIY1sEdMNsPjpx2zwtSE=
github.com/getsentry/raven-go v0.1.2/go.mod h1:KungGk8q33+aIAZUIVWZDr2OfAEBsO49PX4NzFV5kcQ=
github.com/getsentry/raven-go v0.2.0 h1:no+xWJRb5ZI7eE8TWgIq1jLulQiIoLG0IfYxv5JYMGs=
github.com/getsentry/raven-go v0.2.0/go.mod h1:KungGk8q33+aIAZUIVWZDr2OfAEBsO49PX4NzFV5kcQ=
github.com/pkg/errors v0.0.0-20190227000051-27936f6d90f9 h1:dIsTcVF0w9viTLHXUEkDI7cXITMe+M/MRRM2MwisVow=
github.com/pkg/errors v0.0.0-20190227000051-27936f6d90f9/go.mod h1:bwawxfHBFNV+L2hUp1rHADufV3IMtnDRdf1r5NINEl0= 
```

### 卖命依赖

使用模块时，go 命令完全忽略供应商目录。为了向后兼容旧版本的 Go，或者为了确保用于构建的所有文件都存储在一个文件树中，运行`go mod vendor`。

这将在主模块的根目录下创建一个名为`vendor`的目录，并在那里存储来自依赖模块的所有包。

> 注意:要使用主模块的顶级供应商目录进行构建，请运行“go build -mod=vendor”。

### 删除未使用的依赖项

`go mod tidy`修剪未使用的依赖项并更新`go.mod`文件。

## 常见问题

#### 不再需要`GOPATH`了吗？

不再见`GOPATH`。

#### 默认拉哪个版本？

go.mod 文件和 go 命令更普遍地使用语义版本作为描述模块版本的标准形式，以便可以比较版本来确定哪个应该比另一个更早或更晚被考虑。像`v1.2.3`这样的模块版本是通过在底层源代码库中标记修订版来引入的。未标记的修订可以使用类似于`v0.0.0-yyyymmddhhmmss-abcdefabcdef`的“伪版本”来引用，其中时间是 UTC 中的提交时间，最后一个后缀是提交散列的前缀。

#### `go.sum`是否应该检入版本控制？

是的。