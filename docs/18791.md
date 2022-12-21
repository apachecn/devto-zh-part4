# 用 chromebook 安装 node.js 进行 web 开发

> 原文：<https://dev.to/jacoboakley/web-development-with-a-chromebook-installing-nodejs-3fo0>

安装 Nodejs 是启动 Chromebook 并运行 Web 开发的重要部分。随着 Linux 应用程序和终端的增加，开发者现在可以安装 Nodejs 并访问 NPM。我将指导您如何安装它，但首先您需要安装终端。如果你还没有这样做，你可以通过阅读[Web Development With a chrome book:Terminal-Setup](https://medium.com/@jacoboakley/web-development-with-a-chromebook-terminal-setup-dc0d4668d411)来了解如何 at。

1.  打开你的终端
2.  通过运行以下命令获取更新:
    *   sudo apt-get 更新
3.  安装 curl 和 gnupg:
    *   sudo apt-get 安装 curl gnupg -y
4.  install node . js 安装节点. js
    *   https://deb.nodesource.com/setup_10.x | sudo-E bash-sudo apt-get install-y nodejs

对于不同的版本，您可以访问 [Nodejs](https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions) 页面。