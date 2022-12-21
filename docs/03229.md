# 在 Windows 子系统上安装 Docker for Linux v2(Ubuntu)

> 原文：<https://dev.to/bartr/install-docker-on-windows-subsystem-for-linux-v2-ubuntu-5dl7>

# 在 Windows 子系统上安装 Docker for Linux v2(Ubuntu)

Windows 子系统 for Linux v2 [(WSL2)](https://docs.microsoft.com/en-us/windows/wsl/wsl2-about) 在面向 Windows 10 用户的预览版中提供。WSL2 是对 WSL 的重大改进，提供了更快的文件系统性能和完整的系统调用能力。这意味着我们终于可以在 WSL 中运行 dockerd 了！

WSL2 目前只能通过 [Windows Insider 程序](https://insider.windows.com/en-us/)作为预览功能使用。WSL2 需要 Windows 10 build 18917 或更高版本，目前需要 Windows Insider 的“快速环”。

如果您不准备在您的 dev box 上运行快速环预览，您可以下载一个 ISO 并在 VM 中试验 WSL2。Windows Insider ISOs 可从[这里获得:](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewadvanced)

## 设置 WSL2

WSL2 的安装说明可从[这里获得:](https://docs.microsoft.com/en-us/windows/wsl/wsl2-install)确保从[微软商店](https://aka.ms/wslstore)安装 Ubuntu 发行版

设置完成后，启动命令提示符并运行以下命令来验证 Ubuntu 是否设置为版本 2

```
 # Set WSL to default to v2
wsl --set-default-version 2

# check the version
wsl -l -v

# Output should show Ubuntu and version 2
# if not, you can upgrade the distro
# this usually takes 5-10 minutes
wsl --set-version Ubuntu 2 
```

## 安装 Docker

从 WSL bash 中，运行以下命令来设置 Docker。这些与在 Ubuntu 虚拟机上设置几乎相同。

```
 # update the package manager and install some prerequisites (all of these aren't technically required)
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common libssl-dev libffi-dev git wget nano

# create a group named docker and add yourself to it
#   so that we don't have to type sudo docker every time
#   note you will need to logout and login before this takes affect (which we do later)
sudo groupadd docker
sudo usermod -aG docker ${USER}

# add Docker key and repo
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

# (optional) add kubectl key and repo
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# update the package manager with the new repos
sudo apt-get update

# upgrade the distro
sudo apt-get upgrade -y
sudo apt-get autoremove -y

# install docker
sudo apt-get install -y docker-ce containerd.io

# (optional) install kubectl
sudo apt-get install -y kubectl

# (optional) install latest version of docker compose
sudo curl -sSL https://github.com/docker/compose/releases/download/`curl -s https://github.com/docker/compose/tags | \
grep "compose/releases/tag" | sed -r 's|.*([0-9]+\.[0-9]+\.[0-9]+).*|\1|p' | head -n 1`/docker-compose-`uname -s`-`uname -m` \
-o /usr/local/bin/docker-compose && sudo chmod +x /usr/local/bin/docker-compose 
```

## 完成设置

目前 Docker 不自动启动，所以每次都要启动服务。以下命令将修补您的。每次登录时启动 Docker 的配置文件。

```
 echo "sudo service docker start" >> ~/.profile

# exit and then restart WSL
exit 
```

一旦退出并重新启动 wsl(只需从命令提示符或 Windows Run 命令运行“WSL”)，Docker 应该可以正常工作。要进行测试，从 WSL bash 运行以下命令。

```
 docker run -it hello-world 
```

## 拉尼亚普

我更喜欢 WSL 总是从我的主目录(~)开始，所以我使用下面的代码将它添加到我的。简介

```
 echo "cd ~" >> ~/.profile 
```

安装 Azure CLI

```
 # add Azure CLI key and repo
curl -sL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/microsoft.asc.gpg
CLI_REPO=$(lsb_release -cs)
echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ ${CLI_REPO} main" | sudo tee /etc/apt/sources.list.d/azure-cli.list

# update the package manager
sudo apt-get update

# install Azure CLI
sudo apt-get install -y azure-cli 
```

安装点网核心

```
 # add the dotnet core repo
echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-bionic-prod bionic main" | sudo tee /etc/apt/sources.list.d/dotnetdev.list

# update the package manager
sudo apt-get update

# install dotnet core
sudo apt-get install -y dotnet-sdk-2.2 
```