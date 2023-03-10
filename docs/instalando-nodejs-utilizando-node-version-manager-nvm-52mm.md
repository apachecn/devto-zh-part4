# Instalando 节点利用率和节点版本管理器(nvm)

> 原文：<https://dev.to/mistyblunch/instalando-nodejs-utilizando-node-version-manager-nvm-52mm>

据我在论坛和同一个节点文档中读到，不建议使用“sudo”al 来安装带有“**NP**的软件包”。

所以为了避免写**【sudo】**而不让我们看到许可问题，我用的东西是**【nvm】**，因为除了不用**【sudo】**之外，它还帮我处理我想用的版本

安装起来很简单，我们只在终端上运行这个命令:

```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash 
```

o

```
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash 
```

然后关闭并重新打开终端，准备好了！

要验证是否已安装，请键入以下命令:

```
nvm --version 
```

我们将尝试安装节点:

```
nvm install 12.2.0 
```

这是节点的最新版本，为了确保我们安装了节点，我们运行:

```
node -v 
```

如果要安装另一版本的 NodeJS，则必须重新键入上面的命令以及要安装的版本。

```
nvm install 10.15.3 
```

要查看我们安装的 NodeJS 的所有版本，我们运行:

```
nvm list 
```

* * *

默认情况下，节点的第一个版本将是我们安装的第一个版本，但如果要更改此版本，我们只需运行“
”

```
nvm alias default 10.15.3 
```

我希望它为你们服务

参考文献:

*   [https://github.com/nvm-sh/nvm](https://github.com/nvm-sh/nvm)