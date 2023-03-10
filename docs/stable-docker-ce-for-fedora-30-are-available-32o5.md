# Fedora 30 的稳定 docker CE 现已上市！

> 原文：<https://dev.to/robbinespu/stable-docker-ce-for-fedora-30-are-available-32o5>

你使用 docker 吗？如果你正在使用 Fedora 30，那么我有好消息告诉你。他们正式发布了 Fedora 30 的稳定 docker CE，耶！

我们大多数人从 2 月份开始就在等稳定的 docker，OMG！你可以检查一下问题 [#600](https://github.com/docker/for-linux/issues/600) 是如何让大多数 docker 用户感到沮丧的，因为我们没有稳定的发布，并且因为缺少`containerd.io`而无法使用测试或夜间发布，并且迫使开发人员使用旧的回购(F29)或使用[波德曼](https://serverascode.com/2019/05/19/docker-podman-fedora-30.html)作为变通办法来寻求替代方案。

### 如何在 Fedora 30 上安装 docker？

很简单，首先你需要安装并启用 docker 存储库，用 DNF 更新元数据缓存

```
$ sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
$ sudo dnf makecache 
```

现在安装 docker CE 包，通过 DNF 安装在您的工作站上。

```
$ sudo dnf install docker-ce 
```

成功安装 docker 引擎后，让我们启用并启动 Docker 服务。

```
$ sudo systemctl enable docker.service
$ sudo systemctl start docker.service 
```

祝贺你，docker 已经安装并运行在你的系统上。你需要检查其他教程如何使用 docker 的进一步解释。

我更喜欢手动启动 docker，而不是每次启动工作站时自动加载。因为我只在需要的时候使用 docker。

感谢稳定发布！