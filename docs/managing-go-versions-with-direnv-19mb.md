# 使用 direnv 管理 Go 版本

> 原文：<https://dev.to/shihanng/managing-go-versions-with-direnv-19mb>

最近我加入了一个需要以前版本的 Go 的项目。因为我的机器上已经安装了最新版本，所以我一直在寻找一种方法来在一台机器上管理不同版本的 Go。在 Python 世界中，有一个非常著名的版本管理，叫做 [pyenv](https://github.com/pyenv/pyenv) ，这个版本管理受到了 Ruby 版本管理的高度启发: [rbenv](https://github.com/rbenv/rbenv) 。

至于 Go，似乎也没有什么权威的办法来管理 Go 的版本。可能这不是一个大问题，因为许多人会升级到语言的最新版本，因为它本质上保证了 1 的新的次要版本。x 不会打破旧的。然而，通过简单的谷歌搜索，我发现这个问题有三种主要的解决方法:

1.  从源代码安装每个版本的 Go。Kale Blankenship 的这篇文章详细描述了这些步骤。
2.  Go 生态系统中还有一个类似于 **pyenv** 和 **rbenv** 的工具，称为 [gvm](https://github.com/moovweb/gvm) ，它还附带了其他功能，例如管理不同位置的`GOPATH`。
3.  最后，我们也可以用`go get`[。是啊！去拿！](https://golang.org/doc/install#extra_versions)

## 安装不同版本的 Go

在本帖中，我们将探讨如何使用`go get`来管理不同版本的 Go。对于这种方法，有一点需要注意，并不是所有 Go 版本都受支持。支持的版本列在[下载页面](https://godoc.org/golang.org/dl)上。假设我们需要`go1.8.7`，我们要做的就是:

```
$ go get golang.org/dl/go1.8.7
$ go1.8.7 download 
```

一旦下载完成，我们可以使用
用`v1.8.7`编译我们的源代码

```
$ go1.8.7 version
go version go1.8.7 darwin/amd64
$ go1.8.7 build 
```

卸载可以通过移除`GOROOT`目录:
来完成

```
$ rm -rf "$(go1.8.7 env GOROOT)" 
```

## [T3 使用`goX.Y.Z`作为`go`](#using-raw-goxyz-endraw-as-raw-go-endraw-)

通常在一个项目中，我们有一些 Makefiles 的构建脚本，它们会调用`go`而不是`go1.8.7`来构建我们的 Go 二进制文件。因此，如果我们可以将可执行文件`go1.8.7`用作特定目录/项目的`go`，那将非常方便。这就是 [**direnv**](https://github.com/direnv/direnv) 发挥作用的地方。

> direnv 是 shell 的环境切换器。它知道如何挂钩
> bash、zsh、tcsh、fish shell 和 elvish 来根据当前目录加载或卸载环境
> 变量

出于演示的目的，我们将在`GOPATH`中创建一个新项目，如下所示:

```
$ export GOPATH="$(go1.8.7 env GOPATH)"
$ mkdir -p "${GOPATH}/src/github.com/user/hello"
$ cd "${GOPATH}/src/github.com/user/hello"
$ cat >main.go <<EOL package main

import "fmt"

func main() {
    fmt.Println("Hello, world.")
} EOL 
```

为了在我们的项目目录中启用 **direnv** ，我们需要设置`.envrc`文件。这可以通过
来完成

```
$ direnv edit . 
```

在我们项目的根目录中。`.envrc`文件的内容应该是:

```
export GOROOT="$(go1.8.7 env GOROOT)"
PATH_add "$(go1.8.7 env GOROOT)/bin" 
```

在第一行，我们将`GOROOT`指向我们想要使用的版本。然后我们将 Go 1.8.7 编译器添加到`PATH`中，这样它将在默认编译器之前被找到。一旦设置完成，运行`go version`会给我们一个旧版本。

```
$ go version
go version go1.8.7 linux/amd64 
```

然后我们可以使用
构建`main.go`

```
$ go build
$ gdb ./hello
...
(gdb) p 'runtime.buildVersion'
$1 = 0x4a5208 "go1.8.7" 
```

上面还展示了如何检查哪个版本的 Go 被用来构建`hello`二进制文件，如[这篇文章](https://dave.cheney.net/2017/06/20/how-to-find-out-which-go-version-built-your-binary)中所述。

* * *

演示到此为止。希望这篇文章对你有用。我个人认为这种方法在简单性和可用性之间取得了很好的平衡。我经常发现在像`pyenv`这样的工具中有用的另一件事是`pyenv install --list`子命令，它显示了可以用`pyenv`安装的可用版本。当然上面的方法没有这个特性。我们可以用来列出终端上所有可用版本的一种方法是使用带有`curl` :
的 [`pup`](https://github.com/ericchiang/pup) 命令

```
$ curl -s https://godoc.org/golang.org/dl | pup 'body > div.container > table > tbody > tr > td:nth-child(1) > a text{}'

go1.10
go1.10.1
go1.10.2
go1.10.3
go1.10.4
... 
```

这并不漂亮，但很管用💪。

#### 发现错别字？

感谢您的阅读！发现错别字？在这篇博文中，你看到了可以改进的地方或者应该更新的内容吗？感谢[这个项目](https://github.com/maxime1992/dev.to)，你可以很容易地在 https://github.com/shihanng/dev.to 的[上创建一个拉请求来提出一个修复方案！](https://github.com/shihanng/dev.to)