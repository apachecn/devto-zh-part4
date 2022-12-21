# 在 Ubuntu 中将 nodeJS 更新至最新版本

> 原文：<https://dev.to/obaaa/update-nodejs-to-the-latest-version-in-ubuntu-11c6>

带有 *- lts* 的 NVM(节点版本管理器)

NVM 会为您安装最新的稳定节点和 npm

`curl https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash`

`source ~/.nvm/nvm.sh`

`nvm install --lts`

`nvm use --lts`

`npm --version`

现在你准备好了