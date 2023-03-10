# Windows 10 中的 Web 开发(没有 WSL)

> 原文：<https://dev.to/gvescu/web-development-in-windows-10-without-wsl-2plc>

*警告:这篇文章的部分内容已经过时了，这是由于 WSL2 和工具的发展，比如 VSCode，甚至是支持它的第三方工具。我正在考虑尽快制作这篇文章的第二个版本。*

最近看了很多关于 Windows 10 中 webdev 的文章，但是都是用 WSL。我想测试一下在我的 Windows PC 上仅使用所需软件的本地版本是否有可能管理一个完整的 web 开发环境(至少是我需要的堆栈),结果给我留下了非常深刻的印象。

### 何以见得？

WSL 很棒，它创造了许多以前复杂的工作流(例如 Ruby/Rails)。但对我来说，它有两个我无法回避的缺点。

*   它的 I/O 速度不如在本地发行版上应该达到的速度(有时甚至不如在 Windows 上应该达到的速度)
*   Windows 应用程序中隐藏了 WSL 可执行文件。例如，来自 WSL 安装的`git`对于 **Visual Studio 代码**是不可见的(至少[不容易](https://github.com/hangxingliu/wslgit))

此外，许多优秀的程序员花费时间和精力为我们这些喜欢 Windows 的人移植了一些工具，所以为什么不先尝试一下呢？😉

让我们从应用程序开始，在我看来，它让这一切成为可能。

## 巧克力

对于习惯于通过终端管理系统的我们来说，这个实用程序太棒了。这是一个用于 Windows 的包管理器，而且是一个非常好的包管理器。安装后(你需要一个管理员控制台)，你可以像`choco install <package-name>`一样通过 PowerShell 使用它。你可以在这里查看更多关于实用程序[的信息，在这里](https://chocolatey.org/)查看可用软件包列表[。](https://chocolatey.org/packages)

我们要用它。**很多**。

我们继续。

## 终端

每个加入 Windows 行列的 web 开发者的第一个痛点。在改版的 Windows 终端正式发布之前(尽管你可以从微软商店获得预览版的 T2)，我发现自己一直在使用 felixse 的流畅终端。这是一个基于 UWP 和网络技术的终端模拟器，有很多功能，包括标签，主题和配置文件。最简单的安装方法是通过 Chocolatey，但是您也可以通过下载最新版本并执行附带的 PowerShell 脚本来安装它。

```
choco install -y fluent-terminal 
```

Enter fullscreen mode Exit fullscreen mode

<figure>

[![Fluent Terminal screen](img/d9a737fd0e95843addbf801bb56e78ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wTQKcLCE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oa5as8wlduvyqb7t18bv.png)

<figcaption>Fluent Terminal in all its fluent glory</figcaption>

</figure>

使用 Fluent 终端的注意事项是，您无法获得提升的终端(例如，作为管理员的 PowerShell)。对于任何需要管理权限的命令(比如从 Chocolatey 安装东西)，您都需要一个提升的终端。

## 节点。射流研究…

我们从安装 [`nvm`](https://github.com/coreybutler/nvm-windows) 实用程序开始，是的，名字是相似的，但是工具与*nix 的对应工具不一样。这是[科里·巴特勒](http://www.coreybutler.com/)的作品，是推荐节点。JS 版本管理器。我们可以按照 GitHub 页面上的说明或者使用 Chocolatey 来安装它。

```
choco install -y nvm 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们可以检查可用的节点。使用`nvm list available`的 JS 版本，使用`nvm install <version>`安装我们想要的版本，然后通过`nvm use <version>`启用它(这将提示一个 UAC 对话框)。

[![nvm screen](img/12b36d2a76462d5c4a4c01d0bac1c694.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G55Gj_ct--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2p53o7njke3rmr0qayzx.png)

恭喜你！现在你有了节点。JS native for Windows，其工作方式与*nix 相似！唯一的警告是不支持`.nvmrc`文件，所以如果你必须为不同的项目使用不同的版本，你必须使用`nvm use <version>`手动改变它们，而不是依赖那个文件。然而，这仍然比自己管理这些 envvars 要简单得多！

现在，您可以根据需要继续处理您的节点。JS 工具。

## WAMP 栈

这个不错。实际上，我们可以在这里使用类似于 Windows 的 [MAMP，然后就到此为止，但只是为了尝试在 Windows 中做一些类似于*nix 服务器的事情，我们将使用 Chocolatey 手动安装所有东西。](https://www.mamp.info/en/mamp/)

### 阿帕奇

```
choco install -y apache-httpd --params "/port:80" 
```

Enter fullscreen mode Exit fullscreen mode

该命令将安装 Apache Haus 提供的 Apache for Windows。请注意，我添加了一个用于定义端口的参数。这是由软件包维护者定义的，并且会将参数发送给安装程序用于配置目的。以 Apache 为例，我们可以发送我们希望它安装在哪里(默认情况下，它将进入%appdata% path)、端口、服务名称以及我们是否需要该服务。你可以随时[查看套餐页面](https://chocolatey.org/packages/apache-httpd)了解更多信息。

[![Apache screen](img/2d4c59c1ba82ab79ac1cd233ef5acebf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tydm8V5k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/du924gpg9lee2zx80cei.png)

一旦安装完毕，你可以进入`http://localhost:<port>`，你会看到默认页面。不错！

### [MySQL](#mysql)

```
choco install -y mysql --version 5.7.18 
```

Enter fullscreen mode Exit fullscreen mode

该命令将安装 MySQL for Windows，特别是版本 5.7.18。是的，你也可以指定版本。你可以[查看软件包页面](https://chocolatey.org/packages/mysql)了解更多信息/配置选项。默认情况下，它将安装在`C:\tools\mysql`中。配置文件应该在`C:\tools\mysql\current.my.cnf`中。

安装程序将创建一个没有密码的 MySQL 用户`root`,这样你就可以输入`mysql -uroot`来进入终端，并随意更改。

### PHP

```
choco install -y php --params "/ThreadSafe" 
```

Enter fullscreen mode Exit fullscreen mode

该命令将安装 PHP for Windows，特别是与 Apache 兼容的线程安全版本。默认情况下，它将安装在`C:\tools\php<version>`中。这也是配置文件`php.ini`的位置。

你可以检查是否有你需要的分机，你可以检查 [PECL](https://pecl.php.net) 。

### 让 Apache 和 PHP 协同工作

现在我们需要告诉 Apache that 已经安装好了。我们打开`httpd.conf`配置文件(默认情况下在`%appdata%\Apache<version>\conf\httpd.conf`中)，并在文件末尾添加以下内容(在我的例子中，我使用的是 PHP 7.3 和 Apache 2.4):

```
LoadModule php7_module "c:/tools/php73/php7apache2_4"
AddType application/x-httpd-php .php
PHPIniDir "C:\tools\php73" 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了`index.php`文件作为 DirectoryIndex 的一个选项。

```
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule> 
```

Enter fullscreen mode Exit fullscreen mode

[![PHP and Apache](img/ea9e7260241856086f8c9f4015898d20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ckPuvuiC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9pmywyuor8r9ruh42tg2.png)

就是这样！重启 Apache 后，我们现在应该有 PHP 支持了！因此...

### 从 WAMP 栈重启服务

除非另有说明，否则我们安装的所有这些都是为 Windows 添加的服务，因此您可以在服务应用程序中找到它们。但是同样，为了在任何事情上都使用终端，我们将从终端进行操作。

首先，我们可以看到 PowerShell 中运行的所有服务都执行了以下内容:

```
Get-Service    //all services, or
Get-Service | Where {$_.status –eq 'running'}    //only running 
```

Enter fullscreen mode Exit fullscreen mode

在那里，我们可以看到按名称、描述和状态排列的服务列表。当我们拥有我们想要的服务时，我们只需运行:

```
Restart-Service <service-name>    //restart a running service
Stop-Service <service-name>    //stop a running service
Start-service <service-name>    //start a stopped service 
```

Enter fullscreen mode Exit fullscreen mode

然而，这里有一个警告:为了通过 PowerShell 管理服务，我们需要拥有管理员权限，所以所有这些命令(除了`Get-Service`)都需要从提升的终端执行。

## 最后的话

这是一个很好的实验。尽管这些年来，Windows 似乎被其他操作系统中的优秀开发工具所忽视，但有一种工具可以帮助管理一个相当不错的环境。

不过，似乎有很多困难要克服，所以我不会责怪任何人对 WSL 感到兴奋。最后，我们可以像其他人一样使用相同的工具，并更容易地遵循示例。此外，由于如此受欢迎，我非常肯定微软会让 Windows 应用程序更容易地接入 WSL 可执行文件，而不会有太多麻烦。

我期待着 WSL2，也期待着微软继续让选择他们平台的开发者满意。在那之前，我对无 WSL 的设置相当满意。也许当我需要再次使用 Rails 时，我会回到 WSL。