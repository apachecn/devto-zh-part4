# Visual Studio 代码远程容器:Azure Ansible

> 原文：<https://dev.to/cmendibl3/visual-studio-code-remote-containers-azure-ansible-1oc4>

去年，我在做一个使用 Ansible 部署 Azure 服务的项目，让我告诉你一些事情:那时像[Visual Studio Remote Containers](https://github.com/microsoft/vscode-dev-containers)这样的特性会给我们很大帮助！

为什么？因为只需安装 [Visual Studio 代码](https://code.visualstudio.com/)、[远程开发扩展包](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)和 [Docker](https://www.docker.com/products/docker-desktop) ，你就拥有了一个杀手组合，让你可以快速创建一个开发环境，并与你的源代码共享。

当我了解到这个特性时，我觉得我应该为那些需要使用 Azure 和 Ansible 的人创建一个开发人员容器，所以我开始动手，在与 [Chuck Lantz](http://chuxel.github.io/) 合作后，容器定义产生了以下两个文件:

## 1。devcontainer.json

* * *

这个文件用指定的扩展名
配置远程容器

```
{
    "name": "Azure Ansible",
    "dockerFile": "Dockerfile",
    "runArgs": [
        // Uncomment the next line if you will use a ptrace-based debugger like C++, Go, and Rust.
        // "--cap-add=SYS_PTRACE", "--security-opt", "seccomp=unconfined",
        "-v", "/var/run/docker.sock:/var/run/docker.sock"
    ],

    // Uncomment the next line if you want to publish any ports.
    // "appPort": [],

    // Uncomment the next line if you want to add in default container specific settings.json values
    // "settings": { "workbench.colorTheme": "Quiet Light" },

    // Uncomment the next line to run commands after the container is created.
    // "postCreateCommand": "ansible --version",

    "extensions": [
        "vscoss.vscode-ansible",
        "redhat.vscode-yaml",
        "ms-vscode.azurecli"
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

# Pick any base image, but if you select node, skip installing node. 😊
FROM debian:9

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Configure apt and install packages
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils 2>&1 \
    #
    # Verify git, required tools installed
    && apt-get install -y \
        git \
        curl \
        procps \
        unzip \
        apt-transport-https \
        ca-certificates \
        gnupg-agent \
        software-properties-common \
        lsb-release 2>&1 \
    #
    # [Optional] Install Node.js for Azure Cloud Shell support 
    # Change the "lts/*" in the two lines below to pick a different version
    && curl -so- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash 2>&1 \
    && /bin/bash -c "source $HOME/.nvm/nvm.sh \
        && nvm install lts/* \
        && nvm alias default lts/*" 2>&1 \
    #
    # [Optional] For local testing instead of cloud shell
    # Install Docker CE CLI.
    && curl -fsSL https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/gpg | apt-key add - 2>/dev/null \
    && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(lsb_release -is | tr '[:upper:]' '[:lower:]') $(lsb_release -cs) stable" \
    && apt-get update \
    && apt-get install -y docker-ce-cli \
    #
    # [Optional] For local testing instead of cloud shell
    # Install the Azure CLI
    && echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" > /etc/apt/sources.list.d/azure-cli.list \
    && curl -sL https://packages.microsoft.com/keys/microsoft.asc | apt-key add - 2>/dev/null \
    && apt-get update \
    && apt-get install -y azure-cli \
    #
    # Install Ansible
    && apt-get install -y libssl-dev libffi-dev python-dev python-pip \
    && pip install ansible[azure] \
    #
    # Clean up
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=dialog 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于 Azure Ansible 远程容器的信息，请点击[这里](https://github.com/microsoft/vscode-dev-containers/tree/master/containers/azure-ansible)。

希望有帮助！