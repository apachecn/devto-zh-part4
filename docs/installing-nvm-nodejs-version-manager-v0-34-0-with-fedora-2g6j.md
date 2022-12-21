# 使用 Fedora 安装 NVM (NodeJS 版本管理器)v0.34.0

> 原文：<https://dev.to/robbinespu/installing-nvm-nodejs-version-manager-v0-34-0-with-fedora-2g6j>

这只是一个简单的教程。只是想提高我的写作技巧

如果你是一个普通的栈开发者，你一定已经知道什么是 NVM。对于初学 NodeJS 语言的人，我建议你使用 NVM (NodeJS 版本管理器)而不是在你的电脑上安装特定的版本。因为有了版本管理器，它比我们通常使用的方法更容易、更健壮、更干净。试试看！

我正在使用 Fedora 30，我想分享一下如何在 NVM 上安装 NodeJS。首先，NVM 的当前版本是 v034.0。你可以在 github 上查看最新版本。

打开你的终端，输入`$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash`

你会得到这样的东西:

```
% Total % Received % Xferd Average Speed Time Time Time Current
                                 Dload Upload Total Spent Left Speed
100 13226 100 13226 0 0 22531 0 --:--:-- --:--:-- --:--:-- 22493
=> Downloading nvm from git to '/home/rnm/.nvm'
=> Cloning into '/home/rnm/.nvm'...
remote: Enumerating objects: 278, done.
remote: Counting objects: 100% (278/278), done.
remote: Compressing objects: 100% (249/249), done.
remote: Total 278 (delta 33), reused 88 (delta 16), pack-reused 0
Receiving objects: 100% (278/278), 142.36 KiB | 121.00 KiB/s, done.
Resolving deltas: 100% (33/33), done.
=> Compressing and cleaning up git repository

=> Appending nvm source string to /home/rnm/.bashrc
=> Appending bash_completion source string to /home/rnm/.bashrc
=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[-s "$NVM_DIR/nvm.sh"] && \. "$NVM_DIR/nvm.sh" # This loads nvm
[-s "$NVM_DIR/bash_completion"] && \. "$NVM_DIR/bash_completion" # This loads nvm bash_completion 
```

关闭当前终端，然后重新打开终端。以确保导出路径得到更新。

检查你的电脑上安装的 NVM 版本:

```
$ nvm --version
0.34.0 
```

要下载、编译和安装 node 的最新版本，请执行`$ nvm install node # "node" is an alias for the latest version`

```
Downloading and installing node v12.4.0...
Downloading https://nodejs.org/dist/v12.4.0/node-v12.4.0-linux-x64.tar.xz...
########################################################################################################################################################################################## 100.0%
Computing checksum with sha256sum
Checksums matched!
Now using node v12.4.0 (npm v6.9.0)
Creating default alias: default -> node (-> v12.4.0) 
```

所以我们现在已经安装了节点

```
$ node --version
v12.4.0

$ nvm run node --version
Running node v12.4.0 (npm v)
v12.4.0

$ nvm which 12.4.0
/home/rnm/.nvm/versions/node/v12.4.0/bin/node 
```

安装特定版本的节点:

```
$ nvm install 6.14.4 # or 10.10.0, 8.9.1, etc 
```

安装的第一个版本成为默认版本。新的外壳将以节点的默认版本开始(例如，nvm 别名默认)。

您可以使用 ls-remote 列出可用版本:

```
$ nvm ls-remote 
```

然后在任何新的 shell 中只需使用已安装的版本`nvm use node`或者你可以直接运行它`nvm run node --version`

或者，您可以在子 shell 中运行任意命令，使用期望版本的节点`nvm exec 12.4.0 node --version`

您还可以获得可执行文件的安装路径`nvm which 12.4.0`