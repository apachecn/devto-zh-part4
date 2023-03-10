# 一个开发人员到 API 的客户端诞生了

> 原文：<https://dev.to/victoravelar/a-dev-to-api-client-is-born-29pj>

# 第 2 集-项目基金会

让我们开始吧，确保你的电脑上安装了 golang，如果你像我一样使用 macOS 和 Homebrew，那么你只需要执行下面的命令:

```
brew install go 
```

Enter fullscreen mode Exit fullscreen mode

## API 客户端

我们要创建的模块将展示前 10 个链接，以避免一个巨大的显示块。

为此，我们需要访问来自 [devto API](https://docs.dev.to/api) 的信息，为此，我们需要代码以编程方式访问这些信息并能够使用它们。

让我们开始吧

## 项目目录

打开一个终端窗口，导航到您存储 go 代码的地方，由于 go 模块的实现，它不需要在 GOPATH 中的`src`文件夹中，所以我将在我的终端上导航到`~/Code`。

我们将创建我们的模块所在的目录，然后我们将进入最近创建的文件夹，你可以通过执行下面的
来完成

```
mkdir devto-api-go && cd devto-api-go 
```

Enter fullscreen mode Exit fullscreen mode

## 初始化 Go 模块支持

要初始化一个 Go 模块，你需要执行 go mod 工具的`init`命令，所以让我们开始吧，首先我会放置“模板”,然后实际执行，我喜欢这样做，因为这是我的大脑存储信息的方式，所以如果它令人困惑，我很抱歉。

> ⚠️，这很重要，⚠️，当你学习 go 的时候，你会明白一些使用第三方代码的 go 工具依赖于 git 特性。
> 
> 所以当初始化一个模块时，你应该使用`{provider}/{username}/{repository}`形式的
> 库的路径

我喜欢把它看作是对我在使用$GOPATH 时创建的文件夹结构的模拟。

所以从现在开始，我将使用`path-to-your-repo`来别名我之前引用中描述的成分。

```
# Template for initializing a new go module once you're inside the 
# project directory
go mod init {path-to-your-repo} 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的项目来说是

```
# Template for initializing a new go module once you're inside the 
# project directory
go mod init github.com/VictorAvelar/devto-api-go 
```

Enter fullscreen mode Exit fullscreen mode

如果没有任何消息，请不要感到惊讶，你也会习惯于这样一个事实:在 go 中，没有消息就是惊人的消息😂

## 最后的触碰

一旦我们的模块准备好了，你就可以创建一个名为`devto`的文件夹，这是没有原因的，这是我挑选的一个东西，但是我不记得怎么做了🤔，但将来会有用的，我可以保证。

最后，这就是你的`go.mod`文件应该看起来像
的样子

```
module github.com/VictorAvelar/devto-api-go

go 1.12 
```

Enter fullscreen mode Exit fullscreen mode