# 针对节点版本管理的 nave 与 nvm

> 原文：<https://dev.to/goodevilgenius/nave-vs-nvm-for-node-version-management-47hp>

我最近发现了 [`nave`](https://github.com/isaacs/nave) 。我是在 [`npm`的安装页面](https://www.npmjs.com/get-npm)找到的。

这是一个节点版本管理器，这意味着它允许你在你的机器上安装和使用多个版本的节点。

它类似于 [`nvm`](https://github.com/nvm-sh/nvm) ，我已经使用了很长一段时间，我想你们中的许多人也是如此。

主要区别在于`nvm`一般要求你修改你当前的 shell。您必须修改您的`.bashrc`文件，以确保它正确加载。`nave`不需要你在你的外壳上添加任何东西。你只需下载脚本并把它放在当前的`$PATH`中的任何地方，它就可以使用了。

要使用它，您需要做`nave use <version>`，它会打开一个新的 shell，并将`<version>`的路径添加到`$PATH`中。完成后，您只需`exit`，就可以返回到没有节点版本的常规 shell。

如果你愿意，`nave`可以像`nvm`一样工作，你可以将它添加到你的`.bashrc`中，它可以用来修改你当前的 shell，但这不是必需的。

`nvm`在`$PREFIX`设置时使用也有问题。然而`nave`创建了一个 subshell，将您需要的节点版本添加到您的`$PATH`中。因为`$PREFIX`的问题，我已经[无法在手机上使用](https://github.com/nvm-sh/nvm/issues/1645) [Termux](https://termux.com/) 中的`nvm` 。出于同样的原因，其他人在 MacOS 上使用`nvm`时也会遇到使用 [`brew`](https://brew.sh/) 的问题。

我在我的桌面上安装了`nave`，并在我的 shell 中手动设置了一个`PREFIX`，而`nave`仍然正常工作。

我仍然无法在手机上使用`nave`，因为，由于`Termux`基于 Busybox，预编译的二进制文件无法在 Termux 中使用，所以必须编译 node。不幸的是，[的另一个依赖问题](https://github.com/termux/termux-packages/issues/1020)导致编译失败，但我希望这个问题能够得到解决，我将能够在我的手机上使用`nave`进行`node`开发。

说到 Busybox，`nvm`声明在基于 Busybox 的 Alpine Linux 上，您必须指定`nvm install -s version`来告诉它从源代码编译，因为正如我提到的，预编译的二进制文件不能工作。

在我的电话 Termux 下，`nave`自动看到它需要下载源代码并编译，所以我只是做了`nave install latest`，它获取最新版本的源代码并试图编译，而在我的桌面上，它获取二进制文件。所以，在这方面，`nave`似乎比`nvm`聪明一点。

然而，关于 Alpine，我确实尝试在 Alpine 下使用了`nave`。我旋转了一个阿尔卑斯山`docker`集装箱。它花了一段时间安装必要的软件包才能工作。我需要`bash`、`curl`和`perl-utils`，也许还需要更多。我也只好把壳换成了`bash`。之后，我做了`nave install latest`，以为它会下载源代码并编译，就像在 Termux 下一样。它没有。它下载了二进制文件。而且之后做`nave use latest`开了新壳，但是`node --version`不行了，因为前面提到的原因。`nave`不像`nvm`那样提供强制源码安装的标志，所以我不得不运行`env NAVE_SRC_ONLY=1 nave install latest`。然后我不得不安装更多的包，这样编译就可以工作了，比如`g++`、`make`、`linux-headers`和`python`(?).经过(*终于*)编译后，如预期的那样工作了。

然而，如果它能检测到预编译的二进制文件实际上并不工作，那就太好了。这让我相信，它在 Termux 下的 Android 上编译比`nave`更聪明。可能只是它没有意识到一个`aarch64`架构和`arm64`是一样的。

无论如何，在传统 Linux 发行版的桌面上，我发现`nave`到目前为止真的很好，我将使用它一会儿，看看我是否比 nvm 更喜欢它。

* * *

另外，因为我现在已经安装了`nvm`和`nave`，为了避免冲突，我修改了我的`.bashrc`，这样如果我在一个`nave`启动的潜艇里，它就不会使用`nvm`。

下面是我的代码:

```
# No point using two different node installers
if [[ -z "$NAVE" ]]; then export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
fi 
```

Enter fullscreen mode Exit fullscreen mode