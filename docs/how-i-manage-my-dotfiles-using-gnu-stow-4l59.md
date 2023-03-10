# 我如何使用 GNU Stow 管理我的点文件

> 原文：<https://dev.to/spacerockmedia/how-i-manage-my-dotfiles-using-gnu-stow-4l59>

最初发表于 [writingco.de](https://writingco.de) 。这是我的内容博客的交叉帖子。我每周都会发布新的内容，如果你想直接在收件箱里收到我的文章和其他信息，你可以注册我的[时事通讯](https://writingco.de/newsletter)！

对于开发者来说，管理你的点文件是一件相当普通的事情。但对许多人来说，事情往往“行得通”。如果管道胶带真的是一个开发工具，它肯定会在这里找到。但像专业人士一样处理你的点文件应该不难。在这篇文章中，我将向您展示如何。如何像专业人士一样管理你的点文件？

开发人员不必经常在 windows 上处理管理点文件的问题。所以可能只有 ssh 之类的项目，docker 之类的编排工具，以及 python 中的环境、为 Rust 构建目标或设置 Go 之类的特定于语言的东西。然而，这些工具仍然可以自动化。Stow 还有其他高级用法和运行方式。在 windows 上，可以使用后面提到的一个叫做`dploy`的工具。

## GNU Stow

重要的事情是第一位的。在您选择的平台上安装 stow 二进制文件。

### OSX

安装了`brew`之后，您可以简单地运行:

```
brew install stow 
```

Enter fullscreen mode Exit fullscreen mode

嘣你就完了！很简单。

### Debian/Ubuntu(及衍生品)

也很简单。

```
sudo apt install stow 
```

Enter fullscreen mode Exit fullscreen mode

### 拱门

就这么简单

```
sudo pacman -S stow 
```

Enter fullscreen mode Exit fullscreen mode

## 创建您的点文件

现在让我们创建一个文件夹来开始。你可以在任何地方管理你的文件。我把它和我的其他代码放在一起放在`~/code/dotfiles`里。所以从现在开始，我就把它简称为`$DOT`。如果您想继续，只需在您的终端中运行这个:

```
export DOT=$HOME/code/dotfiles 
```

Enter fullscreen mode Exit fullscreen mode

只需替换您的点文件所在的路径。我看到其他人使用的一个常见位置是`$HOME/.dotfiles`。现在，当您在 bash 兼容的 shell 中粘贴代码时，它仍然可以工作。在这个目录中，让我们首先创建一些目录和文件

```
mkdir -p __setup git termite/.confg bin/bin bash
touch setup.sh 
```

Enter fullscreen mode Exit fullscreen mode

这将在你的点文件中创建几个目录。

*   `__setup`:用于一次性设置，如不同平台上的软件包安装。
*   `git`:这将是一个我们可以保存像`.gitconfig`这样的全局 git 配置的地方
*   这将是我们存储一个名为白蚁的终端配置的地方。我在这里用白蚁来帮助展示文件夹是如何构造的以及为什么。
*   `bin/bin`:您将在这里保存 bash 中生成的命令行二进制文件，以便它们在系统范围内可用。该目录将被添加到您的`$PATH`
*   我们将在这里保存我们的 bash 配置。

## 去吧

这很简单，我们想要管理并跟踪我们的点文件中的 Git 配置。因此，我们将把我们的全局`.gitconfig`文件移动到这个目录

```
mv ~/.gitconfig $DOT/git 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了`$HOME/code/dotfiles/git/.gitconfig`。我们需要使用`stow`来安装它。我们去吧。从$DOT 目录中，运行这个:

```
➜ stow -v -R -t ~ git
LINK: .gitattributes => code/dotfiles/git/.gitattributes
LINK: .gitconfig => code/dotfiles/git/.gitconfig 
```

Enter fullscreen mode Exit fullscreen mode

仅仅意味着冗长，这样我们就可以看到它在做什么。`-R`告诉 stow 首先清除旧的链接，确保清除旧的引用。`-t ~`是目标，或者这个 stow 应该安装到哪里。最后，我们为要安装的目录指定目录`git`。

```
➜ ls -la | grep .git
lrwxrwxrwx 32 shawn 17 Jun 23:05 .gitattributes -> code/dotfiles/git/.gitattributes
lrwxrwxrwx 28 shawn 17 Jun 23:05 .gitconfig -> code/dotfiles/git/.gitconfig 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在上面看到的，它将文件放回到主目录中，并对它们进行符号链接。请注意，这在 windows 上不起作用，但在`WSL`环境中可以工作，而且做得很好。

## 自动化点文件

因此，我们知道我们需要做更多的工作。如果你不得不做不止一次，你应该自动化它，对不对？当然了。自动化是管理重复性任务的关键，因此我们将使用 bash 脚本来安装我们的 dotfiles 文件夹。

<figure>[![https://imgs.xkcd.com/comics/automation.png](img/0659b44a2218b0fad4ec52a71f7f35dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4uFpNKSD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/automation.png) 

<figcaption>礼遇 xkcd[https://xkcd.com/1319/](https://xkcd.com/1319/)</figcaption>

</figure>

打开`setup.sh`并将以下内容放入其中:

```
#!/usr/bin/env bash

# make sure we have pulled in and updated any submodules
git submodule init
git submodule update

# what directories should be installable by all users including the root user
base=(
    bash
)

# folders that should, or only need to be installed for a local user
useronly=(
    git
)

# run the stow command for the passed in directory ($2) in location $1
stowit() {
    usr=$1
    app=$2
    # -v verbose
    # -R recursive
    # -t target
    stow -v -R -t ${usr} ${app}
}

echo ""
echo "Stowing apps for user: ${whoami}"

# install apps available to local users and root
for app in ${base[@]}; do
    stowit "${HOME}" $app 
done

# install only user space folders
for app in ${useronly[@]}; do
    if [[! "$(whoami)" = *"root"*]]; then
        stowit "${HOME}" $app 
    fi
done

echo ""
echo "##### ALL DONE" 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们将只为本地用户安装`git`目录，因为 root 用户不需要。然而，我们接下来要做的`bash`既可以用于本地用户，也可以用于 root 用户。然后我们创建一个名为`stowit`的 bash 函数，用我们需要的参数运行实际的 stow 命令。如果我们在安装`git`的时候把它叫做

```
stowit ~ git 
```

Enter fullscreen mode Exit fullscreen mode

你可以在我调用`stowit`之后的循环中看到。这真的很简单。第一个循环是为任何用户安装文件夹，第二个循环检查为任何用户*安装文件夹，除非*是`root`用户。因此，让我们设置 bash 目录。

```
mv ~/.bashrc $DOT/bash
mv ~/.bash_profile $DOT/bash
mv ~/.profile $DOT/bash 
```

Enter fullscreen mode Exit fullscreen mode

OSX 可能没有所有这些，Windows 肯定没有。如果你没有它们，那么你可以忽略它们。

### 运行设置

`bash`文件现在在您的点文件存储库中进行管理。现在让我们试着运行我们的安装文件。

```
➜ chmod +x setup.sh
 ➜ ./setup.sh
Stowing apps for user:
LINK: .profile => code/dotfiles/bash/.profile
LINK: .bashrc => code/dotfiles/bash/.bashrc
LINK: .bash_profile => code/dotfiles/bash/.bash_profile
UNLINK: .gitattributes
UNLINK: .gitconfig
LINK: .gitattributes => code/dotfiles/git/.gitattributes (reverts previous action)
LINK: .gitconfig => code/dotfiles/git/.gitconfig (reverts previous action)

##### ALL DONE 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到`stow`在链接我们的文件和文件夹方面非常聪明。它链接了我们新的 bash 文件。但是当我们再次运行 stow 时，它检查了我们之前链接的`git`文件，重新链接了它们。实际上，您可以配置如何使用不同的标志来处理这些情况。`stow`还会在发现以前没有收藏过的新文件时中止收藏文件夹，并告诉你哪些文件可以修复。如果我们运行

```
sudo ./setup.sh 
```

Enter fullscreen mode Exit fullscreen mode

那么只有`bash`文件会被设置在根目录下。这很好，因为经常当我们不得不改变到根用户时，我们失去了我们为用户做的所有酷的设置。

## bin 目录

我们的设置中还有最后两个文件。会容易些，那就这么办吧。在`$DOT/bin/bin`文件夹中，我们可以放置任何想要跨系统保存的二进制文件。只要确保它们是跨平台的。这就是为什么我喜欢使用 bash 和 Go 二进制文件，因为你不需要担心依赖性。Python 也是一样，但这取决于`python`的可用版本。尽管我喜欢 python，但 bash 在这里是最好的，除非 python 中有一些神奇的工具来做我们需要的事情。比如处理 CSV 文件或者操作数据。

在`$DOT/bin/bin`文件夹中，我们创建一个名为`$`的文件…是的！只是一个美元符号。你可能会问为什么，但你会明白的。将其内容更新为:

```
#!/bin/zsh

# Ever pasted "$ somecommand" into the terminal and gotten this error?
# -bash: $: command not found

# Begone, silly errors! Lazy copy + paste forever!! ETCETERA!!!!

echo 'Quit pasting in commands from the internet, you lazy bum.'
"$@" 
```

Enter fullscreen mode Exit fullscreen mode

然后，在`$HOME/.bashrc`中简单地添加这一行:

```
export PATH="$HOME/bin/$:$PATH" 
```

Enter fullscreen mode Exit fullscreen mode

再次运行`setup.sh`链接文件。一旦链接，我们需要更新我们的 bash 终端环境。你可以重启你的终端，或者运行

```
source ~/.bashrc 
```

Enter fullscreen mode Exit fullscreen mode

变戏法。您应该能够键入:

```
➜ which $
/home/shawn/bin/$ 
```

Enter fullscreen mode Exit fullscreen mode

如果是这样的话，那么它是有效的。现在假设您正在从堆栈溢出中复制并粘贴一个命令。你不小心从他们的终端粘贴了`$`。这通常表示他们的提示从哪里开始。通常，对于`$`命令，您会得到某种类型的`command not found`错误。但是现在，如果你把命令`$ echo "I work now"`粘贴到你的终端，你会得到:

```
$ echo "I work now"
Quit pasting in commands from the internet, you lazy bum.
I work now 
```

Enter fullscreen mode Exit fullscreen mode

现在，当你从一个代码样本中复制一个命令，同时也复制了`$`字符时，它将继续运行这个命令，但是也会给出一个消息(如果你愿意的话)来确保不要复制`$`。使得它在许多情况下不会断裂。现在，在`$HOME/bin`目录中，您可以放置任何您希望系统范围内可用的命令。

## 白蚁

现在让我们做白蚁。我在这里主要是为了帮助你理解它是如何复制目录的。所以如果你不用白蚁，就跟着读吧。

程序`termite`将其配置文件不保存在`$HOME`中，而是保存在`$HOME/.config/termite`中。或者，更具体地说，`$XDG_CONFIG_HOME/termite`通常默认为`$HOME/.config`。

当我们运行`stow -R -t ~ termite`时，它获取源目录，在本例中为`$DOT/termite`，并将其内容映射到目标目录，即`~`又名`$HOME`。对我来说那就是`/home/shawn`。在 OSX 会是`/Users/shawn`。在`termite`目录中有一个. config 文件夹。既然`$HOME/.config`已经有了，那就必须再深入一层。

[![https://cdn-images-1.medium.com/max/1600/1*cwR_ezx0jliDvVUV6yno5g.jpeg](img/7b69a37c3745a829ca181d76c6a96209.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UcJhcS8y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AcwR_ezx0jliDvVUV6yno5g.jpeg)

现在，它会将`$DOT/termite/.config/termite`与`$HOME/.config/termite`进行比较，并看到它还不存在于`$HOME/.config/termite`中，并将运行

```
ln -s $HOME/code/dotfiles/termite/.config/termite $HOME/config/termite 
```

Enter fullscreen mode Exit fullscreen mode

当我们再次运行`setup.sh`时，我们可以看到这是真的

```
➜ pwd
/home/shawn/.config

 ➜ ls -la | grep termite
lrwxrwxrwx 40 shawn 14 Jun 9:37 termite -> ../code/dotfiles/termite/.config/termite 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我已经向你展示了如何像专业人士一样管理你的点文件。这很简单。我还没有过多谈论我们制作的`_setup`目录。您可以为您的系统保留其他只应运行一次的安装文件。我用`_setup/osx.sh`来安装`homebrew`和自制软件包，并设置其他系统设置。我也有使用`pacman`或`trizen`安装我的软件包的`_setup/arch.sh`。

从这里，您可以看到这个简单的设置如何使管理 linux 和 osx 的点文件变得更加容易。需要一些额外的步骤才能让它们在 windows 中工作。你可以使用类似 https://github.com/arecarn/dploy 的，它是`stow`的一个 python 端口，但是需要在你的系统上安装 python(请使用 python 3.6+)。它知道如何处理 bash ( `$VAR`)和 windows ( `%VAR%`)之间的环境变量，以及使用兼容的系统链接。你可能不会在 windows 上使用`termite`，但是有些人会使用像 [Hyper Term](https://hyper.is/) 这样的工具，尽管它是建立在 electronic 上的，但是它是一个非常好的跨平台终端。

现在，您应该能够轻松地将 vim/neovim、tmux 或任何其他配置放入您的点文件中。如果你想为 tmux 之类的东西使用子模块，只需做

```
git submodule add [tmux-repo] tmux/ 
```

Enter fullscreen mode Exit fullscreen mode

然后，当运行`setup.sh`脚本时，它将确保代码是本地的，并相应地链接一切。

### 寻找开发专家？

给我发个信息就行了！

[联系我](https://writingco.de/contact/)

如何像专业人士一样用 stow 管理你的点文件？第一次出现在我的博客上是在[写作公司](https://writingco.de)。