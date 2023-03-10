# 在 WSL 2 中设置 node.js 开发环境

> 原文：<https://dev.to/noejon/setting-up-a-node-js-development-environment-in-2-4eb1>

你好，互联网，

这是我在网上发布的第一篇文章。今天我们将讨论 Linux 的 Windows 子系统，它是作为内部版本 18917 的一部分发布的。我们将尝试安装 WSL2 并设置 node.js 开发环境。

[Windows 从 2016 年开始有了针对 Linux 的子系统](https://docs.microsoft.com/en-us/windows/wsl/about)。它使我们能够在 Windows 10 系统上使用 Linux 发行版。现在，它附带了用于 Linux 2 的[Windows 子系统，其架构发生了转变，提高了性能以及 Windows 和子系统之间的兼容性。更多信息可以在](https://docs.microsoft.com/en-us/windows/wsl/wsl2-index)[微软开发者博客](https://devblogs.microsoft.com/commandline/)中找到

# Node.js 开发环境

我们将建立的开发环境包括:

*   `git`作为我们的版本控制系统。
*   `zsh`和`oh-my-zsh`替换 bash(可选)。
*   作为我们的文字编辑。
*   `node.js`和`npm`。
*   `docker`和`docker-compose`来包装我们的项目。

但是首先我们需要安装 WSL2。

# 安装 WSL2

## Windows 内幕

我们要做的第一件事是启用`Windows Insider Program`

可以在`Settings` > `Update and Security` > `Windows Insider Program`中启用。以下是步骤

*   登录到`Windows Insider account`或创建一个。
*   在撰写本文时，WSL2 在`Fast`构建中，因此`insider settings`应该被设置为`Fast`。
*   然后我们前往`settings` > `Update and Security` > `Windows Update`查看更新。然后，我们安装最新的版本，然后重新启动我们的机器。
*   我们现在应该有了最新的更新，随之而来的是 WSL2。接下来，我们将看看如何激活 WSL2。

## WSL

为了安装 WSL2，首先需要安装 WSL。打开 Powershell(以管理员身份)并键入:

```
Enable-WindowsOptionalFeature  -Online  -FeatureName  Microsoft-Windows-Subsystem-Linux 
```

当系统提示我们重启机器时，我们只需回答“是”。

计算机重启后，我们前往`the Microsoft Store`并搜索术语`linux`。

点击`Run Linux on Windows`后，我们选择获取`ubuntu`作为我们的 linux 发行版。一旦安装了 ubuntu，我们就启动它。在第一次初始化后，我们被提示`Unix username`和`Unix password`。然后我们更新 ubuntu 的包管理器(这可能需要一些时间，通常会提示你两次信息)。

```
sudo apt update && sudo apt upgrade 
```

我们现在离启用 WSL2 只有一步之遥。

## WSL2

让我们再次打开 powershell，并启用另一个可选功能

```
Enable-WindowsOptionalFeature  -Online  -FeatureName  VirtualMachinePlatform 
```

再次提示我们重启系统。又来了。

机器重启后，我们最后一次以管理员身份打开 powershell，以确保我们使用了 WSL2！

```
wsl  --set-version  ubuntu  2 
```

Powershell 表示，这个过程可能需要几分钟。

**注意**:我在同一周内在两台不同的机器上运行了这个安装，有一次我不得不键入`ubuntu`和另一个`ubuntu-18.04`。商店里有几个 ubuntus，我可能挑了不同的。

接下来，我们将 WSL2 设置为 WSL 的默认选择

```
wsl  --set-default-version  2 
```

就是这样，我们现在已经成功地安装了 WSL2。是时候建立我们的开发环境了。

# Linux 文件

[发布公告博客](https://devblogs.microsoft.com/commandline/wsl-2-is-now-available-in-windows-insiders/)要求我们“确保将我们经常访问的文件放在我们的 Linux 根文件系统中，以享受文件性能的好处”。

我们现在可以从窗口`explorer`访问文件。这就像在浏览器导航栏中输入`\\wsl$\Ubuntu\home`一样简单。我们现在在我们的主文件夹中。
个人文件夹可以固定到`Quick access`

大多数安装将在 ubuntu 控制台中进行。作为良好的实践，在 linux 上安装任何东西之前，运行

```
sudo apt update && sudo apt upgrade 
```

我们现在可以开始设置我们的开发环境了。

# 变 bash 为 zsh

在本节中，我们将默认的`bash`端子替换为`zsh`和`oh-my-zsh`。如果您打算继续使用`bash`，可以跳到[开发工具部分](#development-tools)

为了安装`zsh`，我们打开一个`terminal`(Ubuntu 应用程序)并运行

```
sudo apt install zsh 
```

现在只需在终端中键入`zsh`即可启动`zsh`。需要创建一个`.zshrc`文件，并提示我们进行选择。当我们安装`oh-my-zsh`时，`.zshrc`文件将被替换。我们不想每次启动 ubuntu 应用程序时都键入 zsh，所以接下来我们要做的是改变默认的 shell 来使用`zsh`。为此，我们将按照本例中的[使用](https://askubuntu.com/questions/131823/how-to-make-zsh-the-default-shell) [chsh](http://man7.org/linux/man-pages/man1/chsh.1.html) 命令。只需运行

```
chsh -s $(which zsh) 
```

完成后，我们将改变我们`zsh`的主题，这样做，我们将利用 [oh-my-zsh](https://ohmyz.sh) 的力量。一个简单的命令将安装它:

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" 
```

我把我的主题改成了λ主题，但是你可以在所有现存的主题中自由选择[和](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)，并按照指示进行修改。

还附带了一组插件，可能有助于提高您的开发速度。`npm`也有补全插件。请参考[插件页面](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)找到适合你的。我喜欢使用 [git 插件](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/git/)。说到 git，它是我们要安装的下一个工具。

**注意**:运行`sudo apt upgrade`或`sudo apt-get update`时，可能会遇到以下错误代码:

```
E: Release file for https://download.docker.com/linux/ubuntu/dists/bionic/InRelease is not valid yet (invalid for another 15h 3min 13s). Updates for this repository will not be applied. 
```

这是由于 WSL 中的一个已知问题。一个临时的解决办法是打电话给

```
sudo hwclock -s 
```

# 开发工具

## 去吧

### 安装

为了在 wsl 终端中安装 git，我们运行

```
sudo apt update && sudo apt upgrade
sudo apt install git 
```

### 行尾

下一步是处理跨平台问题，有时 git 会识别文件中的变化，而实际上并没有。这是因为 windows 使用 CRLF 和 linux LF 来表示行尾。要解决这个问题，可以运行下面一行:

```
git config --global core.autocrlf input 
```

### 配置 SSH

首先，我们在新的 linux 子系统上创建一个 SSH 密钥。说明可以在这里找到[[https://help . github . com/en/enterprise/2.15/user/articles/generating-a-new-ssh-key-and-add-it-to-the-ssh-agent](https://help.github.com/en/enterprise/2.15/user/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)]

为了生成 git 的私钥和公钥，我们运行:

```
ssh-keygen -t rsa -b 4096 -C "email@email.com" #replace with your email 
```

这将生成一个新的 ssh 密钥。当提示选择保存密钥的文件时，我们可以使用默认位置或输入文件的首选位置。对于本例，我们将考虑使用默认位置`~/.ssh/id_rsa`

启动 ssh 代理:

```
eval "$(ssh-agent -s)" 
```

然后，我们将 ssh 私有密钥添加到 ssh 代理中

```
ssh-add ~/.ssh/id_rsa 
```

之后，我们可以将密钥添加到一个 [github](https://help.github.com/en/enterprise/2.15/user/articles/adding-a-new-ssh-key-to-your-github-account) 或一个(git lab)[[https://docs . git lab . com/ee/ssh/# adding-an-ssh-key-to-your-git lab-account](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)]帐户。

**注意**:我现在每次重启笔记本电脑都需要运行`eval "$(ssh-agent -s)"`和`ssh-add ~/.ssh/id_rsa`。

现在我们安装了 git，我们确信我们编写的代码不会丢失。现在，为了编写我们的代码，让我们安装 Visual Studio 代码。

### Visual Studio 代码

我们浏览(visual studio 代码网站)[[https://code.visualstudio.com/](https://code.visualstudio.com/)]，下载 **Windows** 的 Visual Studio 代码，并安装。

在安装过程中，我们确保选中复选框以将 Visual Studio 代码添加到路径中，这是我们稍后安装的扩展的建议。

代码附带了大量的扩展，但是我们感兴趣的是 [VS 代码远程开发](https://code.visualstudio.com/docs/remote/remote-overview)。它捆绑了一些对远程开发有用的扩展，包括为我们带来魔力的`Remote - WSL`。

在 VS 代码中，在 extension 选项卡中我们寻找`Remote Development`并安装它。

在`zsh`终端中，我们浏览到我们的主文件夹并创建一个`dev`文件夹:

```
cd ~ && mkdir dev && cd dev 
```

现在我们从 ubuntu 终端
开始编码

```
code . 
```

它应该会打开一个新的 VS 代码窗口，并在我们的 WSL 上安装一个 VS 代码服务器。完成后，我们现在可以在编辑器中创建文件，它们将在 linux 文件系统中创建。
本文正是使用这种设置编写的

这最后一部分是给我们当中安装了`zsh`的人看的。一旦 VS 代码连接到 WSL，就有可能将默认 shell 修改为`zsh`。这将在重新启动终端后生效。

现在我们已经安装了 Visual Studio 代码，让我们安装 node.js

### Node.js

#### 节点版本管理器(nvm)

为了安装 [node.js](https://nodejs.org/en/) ，我们将利用 [nvm](https://github.com/nvm-sh/nvm) 。[装置](https://github.com/nvm-sh/nvm#install--update-script)再一次非常直接。在 ubuntu 终端中，我们运行:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | zsh 
```

**注意**:如果没有安装`zsh`，之前的命令应该用`bash`运行，而不是用`zsh`。

如果安装成功，下面的命令将返回`nvm`。如果没有，只需重启终端就可以解决这个问题。

```
command -v nvm 
```

#### 安装节点和 npm

让我们来看看 nvm 中可用的命令

```
nvm --help 
```

`nvm install`命令中我们感兴趣的命令。让我们安装 node 的最新 LTS(长期支持)版本:

```
nvm install --lts 
```

就是这样，我们安装了`node`和`npm`。但是让我们通过检查版本来确认我没有说谎。

```
node --version && npm --version 
```

我们建立开发环境的旅程即将完成。我们需要做的最后一件事就是进行最后一次(鲸鱼)乘船旅行。Docker 来了。

### 码头工人

#### ce 对接器

为了安装 [docker](https://www.docker.com/) ，我们将遵循官方文档中的[步骤](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

首先我们需要更新`apt-get`

```
sudo apt-get update 
```

之后我们安装了一些必需的依赖项

```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common 
```

然后我们需要添加 Docker 的官方 GPG 键

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - 
```

我们需要确保我们有带指纹`9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`
的钥匙

```
sudo apt-key fingerprint 0EBFCD88 
```

Docker 需要添加到存储库列表中

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \  $(lsb_release -cs)  \ stable" 
```

**注意**:我的处理器是 amd64 工艺，如果你有不同类型的处理器，相应替换`[arch=amd64]`(其他可能的值:`armhf`、`arm64`、`ppc64e1`、`390x`)。

我们现在准备安装(Docker 社区版)[[https://docs.docker.com/install/](https://docs.docker.com/install/)]

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io 
```

现在让我们确保 docker 已安装。

```
sudo docker run hello-world 
```

这将下载一个测试映像，并在一个容器中运行它。如果 docker 服务没有启动，它需要一个(手动 kickstart)[[https://docs.docker.com/config/daemon/systemd/](https://docs.docker.com/config/daemon/systemd/)，在我们的例子中，对于 ubuntu 我们将使用:

```
sudo service docker start
sudo docker run hello-world 
```

#### 复合坞站

在这个充满微服务的世界里，像 [docker compose](https://docs.docker.com/compose/) 这样的工具就是一个省时器。它允许我们从一个命令运行几个应用程序容器。[安装步骤](https://docs.docker.com/compose/install/)如下:

首先下载 Docker Compose 的稳定版本(在撰写本文时为`1.24.1`)

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
```

二进制文件下载后，需要有执行权限:

```
sudo chmod +x /usr/local/bin/docker-compose 
```

最后，我们通过运行
来检查安装

```
docker-compose --version 
```

它应该返回 docker-compose 版本和构建 id(在撰写本文时为`docker-compose version 1.24.1, build xxxxxxxx`)

我们现在已经安装了工具，可以开始开发节点应用程序了。WSL2 目前只处于测试模式，所以我们很可能会遇到[问题](https://github.com/microsoft/WSL/issues)。不要犹豫报告遇到的任何问题(如果已经遇到问题，先仔细检查)。

我希望你喜欢这篇文章，这是我写的第一篇文章。请随意留下一些反馈。

那都是乡亲们！

乔纳森。