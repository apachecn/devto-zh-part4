# 为 JHIpster 设置环境

> 原文：<https://dev.to/amatosg/setting-up-environment-for-jhipster-3ccb>

<small>*[照片](https://unsplash.com/photos/1td5Iq5IvNc)由[马克西米利安·魏斯贝克尔](https://unsplash.com/@maximilianweisbecker)于 [Unsplash](https://unsplash.com/)*</small>
由于我的公司批准我每周 2 天远程工作，他们刚刚给了我一台新的(非常棒的戴尔 Latitude)装有 Ubuntu 的笔记本电脑。

我有很多软件包要安装，因为这是一个全新的安装，所以我想这将是一个好主意，这样我就不必每次需要设置我的机器时都要重新搜索一遍(以前发生过几次)。

# ce 对接器

我觉得这个不需要太多的文字。

### 安装证书

```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common 
```

Enter fullscreen mode Exit fullscreen mode

### 添加 source.list 库

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \  $(lsb_release -cs)  \ stable" 
```

Enter fullscreen mode Exit fullscreen mode

### 更新& &安装

```
sudo apt-get update && sudo aptitude install docker-ce docker-ce-cli containerd.io 
```

Enter fullscreen mode Exit fullscreen mode

# 复合坞站

运行 JHipster 生成的`yml`文件(用于数据库、代码分析、监控等等)棒极了。

### 安装坞站复合材料

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose 
```

Enter fullscreen mode Exit fullscreen mode

### 使其可执行

```
sudo chmod +x /usr/local/bin/docker-compose 
```

Enter fullscreen mode Exit fullscreen mode

# 【nodejs】&&NPM

从官方仓库安装 nodejs 后，我发现它们有点过时，也许我需要修改一些东西，但我发现这个选项工作正常。

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get install -y nodejs 
```

Enter fullscreen mode Exit fullscreen mode

# JHipster

一切都很简单，JHipster 的安装也不例外。只需运行

```
sudo npm install -g generator-jhipster 
```

Enter fullscreen mode Exit fullscreen mode

注意:如果你得到一个提到`Error: ENOSPC: System limit for number of file watchers reached, watch 'target/classes'`的错误，有一个解释:

> 在 Linux 上，Listen 默认使用 inotify 来监视目录的变化。遇到系统限制您可以监控的文件数量的情况并不少见。例如，Ubuntu Lucid(64 位)的 inotify 限制设置为 8192。

您可以通过运行来解决这个问题，这将在重新启动时保持:

```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p 
```

Enter fullscreen mode Exit fullscreen mode

# 附加包

*   针对开发者的聊天，Jhipster 也有它的聊天功能！)
*   [Kitematic](https://github.com/docker/kitematic/releases) (Kitematic 是一个管理 Mac、Linux 和 Windows 上 Docker 容器的简单应用。).
*   Gitkraken (我用过的最好的 git 客户端)

就是这样:)