# 如何使用 nvm，为什么

> 原文：<https://dev.to/jlouiss/how-to-use-nvm-and-why-4e05>

# 什么是 nvm

节点版本管理器是...节点版本管理器。它允许您轻松地更改节点版本，例如当您使用不同的 NodeJS 版本处理多个项目时，这非常有用。
[![Image showing how you can change node versions using nvm in the terminal](img/89ccff56a27d3f55ac09f9d977e3d941.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XyZA3bUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5xw6y0y2aili4a00d8mo.png) 
*你可以用一个简单的命令瞬间改变你的节点版本——酷*😎

* * *

# 安装

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash`
[![nvm installation in the terminal](img/a5da181835e180f22ac67ccfb01394a1.png)T4】](https://res.cloudinary.com/practicaldev/image/fetch/s--nh8Ju0c2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o7oxzl56jnto5gljrmii.png)

安装完成后，您应该将上面看到的最后三行添加到您的 shell 配置文件中(这意味着将它们复制粘贴到`~/.bash_profile`或您正在使用的任何地方)。之后，你应该重启你的终端来使用`nvm`。

查看[此链接](https://github.com/nvm-sh/nvm#installation-and-update)获取最新版本以及如何在你的机器上安装`nvm`的更多细节(macOS 和 Linux)。
【根据我自己的经验，我不推荐使用 windows 进行 web 开发——但是你可以在谷歌上搜索“nvm windows”来获得 nvm 的 Windows 兼容实现】。

* * *

# 用法

首先安装自己喜欢的节点版本:`nvm install 10`。如果您没有指定确切的版本，nvm 将自动下载最新版本。
用`nvm`安装任何版本的 NodeJS 后，你可以通过运行`nvm use <version>`轻松切换到那个版本。

现在你可以走了。

* * *

# 有用的命令

*   `nvm ls`显示您安装的节点版本
*   `nvm ls-remote`显示所有可用的节点版本
*   `nvm install <version>`安装指定版本
*   `nvm uninstall <version>`的工作方式类似于`install`命令
*   `nvm alias default <version>`设置您的默认节点版本

* * *

# 提示&诡计

[![How to switch versions using nvm](img/568dfb52969803388e04b4b0a08a1a66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cMHEP1t---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h05nvf8gpt7vj76zpk5c.png) 
当你在一个目录下运行`nvm install`如果你有一个`.nvmrc`文件，`nvm`会自动安装`.nvmrc`文件中指定的版本。

如果您正在处理一个共享项目，并且需要一个特定的 NodeJS 版本，这将非常有用。

* * *

如果你有任何建议或者你喜欢这篇文章，请评论