# Visual Studio 代码远程容器:Azure 区块链

> 原文：<https://dev.to/cmendibl3/visual-studio-code-remote-containers-azure-blockchain-20om>

在与 [Azure Ansible](https://github.com/microsoft/vscode-dev-containers/tree/master/containers/azure-ansible) 容器合作后，我决定也为那些想要或需要使用 [Azure 区块链开发工具包以太坊](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)来创建智能合约的人开发一个开发者容器，消除在你的机器上安装 Python、Truffle、Ganache 和 NodeJS 的负担。

我再次与 [Chuck Lantz](http://chuxel.github.io/) 合作，容器定义产生了以下两个文件:

## 1。devcontainer.json 文件

* * *

这个文件用指定的扩展名
配置远程容器

```
{
    "name": "Azure Blockchain",
    "dockerFile": "Dockerfile",
    // Uncomment the next line if you will use a ptrace-based debugger like C++, Go, and Rust.
    // "runArgs": ["--cap-add=SYS_PTRACE", "--security-opt", "seccomp=unconfined"],
    // Uncomment the next line if you want to publish any ports.
    // "appPort": [],
    // Uncomment the next line if you want to add in default container specific settings.json values
    // "settings": { "workbench.colorTheme": "Quiet Light" },
    // Uncomment the next line to run commands after the container is created.
    // "postCreateCommand": "az --version",
    "extensions": [
        "ms-vscode.azurecli",
        "azblockchain.azure-blockchain"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

## 2。Dockerfile 文件

* * *

这是包含开发环境所有工具的 docker 文件。

```
#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------

FROM python:2.7

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Configure apt and install packages
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils 2>&1 \
    #
    # Verify git, process tools installed
    && apt-get -y install git procps \
    #
    # Install nodejs
    && curl -sL https://deb.nodesource.com/setup_10.x | bash - \
    && apt-get install -y nodejs \
    #
    # Install Truffle Suite
    && npm i --unsafe-perm -g truffle \
    #
    # Install Ganache CLI
    && npm install -g ganache-cli \
    # 
    # Install the Azure CLI
    && apt-get install -y apt-transport-https curl gnupg2 lsb-release \
    && echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" > /etc/apt/sources.list.d/azure-cli.list \
    && curl -sL https://packages.microsoft.com/keys/microsoft.asc | apt-key add - 2>/dev/null \
    && apt-get update \
    && apt-get install -y azure-cli \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=dialog 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于这个远程容器的信息，请点击[这里](https://github.com/microsoft/vscode-dev-containers/tree/master/containers/azure-blockchain)。

希望有帮助！