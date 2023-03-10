# 使用 Rest API 访问 Docker

> 原文：<https://dev.to/idevkamboj/access-docker-using-rest-api-pi>

大多数时候我们使用 Docker CLI 来管理 Docker 对象，如容器、映像、卷和网络。但是我们也可以使用 Rest API 来管理 Docker 对象。

最初发布于 [https://devkamboj.in](https://devkamboj.in) 。

Docker CLI 也使用 Docker rest API 来管理对象。正如你在上面的图片中看到的。但是也可以直接使用 Rest API。

Docker 守护进程可以通过三种不同类型的套接字监听 Docker 引擎 API 请求: **unix** 、 **tcp** 和 **fd** 。

默认情况下，unix 套接字是启用的。如果您想远程访问 Docker Rest API，这没有帮助。因为 unix 套接字用于在同一主机操作系统上执行的进程之间交换数据。
如果需要远程访问 Docker 守护进程，需要启用 TCP 套接字。

```
sudo dockerd -H tcp://0.0.0.0:2375 
```

您可以使用-H tcp://0.0.0.0:2375 监听所有网络接口上的端口 2375

### 还有另外一种方法就是使用 **systemd**

```
cd /lib/systemd/system
vi docker.service
ExecStart=/usr/bin/dockerd -H fd:// -H tcp://0.0.0.0:2375
#just add -H tcp://0.0.0.0:2375 and restart docker 
```

现在你可以开始使用 api

```
curl -X GET http://ip:4243/containers/json
[{
  "Id":"fff8c7927e19d5a7da280424a09572729641137a8f42e116c3073c72da2978d3",
 "Names":["/rockey"],
  "Image":"busybox",
  ...
}] 
```

但是如果您不想远程访问 Rest API，那么您不需要启用 TCP。那你就用 unix socket 就可以了。

### 列出运行相同的容器，如**码头工人 ps**

```
$ curl --unix-socket /var/run/docker.sock http:/v1.24/containers/json
[{
  "Id":"ae63e8b89a26f01f6b4b2c9a7817c31a1b6196acf560f66586fbc8809ffcd772",
  "Names":["/tender_wing"],
  "Image":"bfirsh/reticulate-splines",
  ...
}] 
```

您可以使用 Python 和 Go，例如:

### 巨蟒

```
import docker
client = docker.from_env()
for container in client.containers.list():
  print container.id 
```

### 去吧

```
package main

import (
    "os"

    "github.com/docker/docker/api/types"
    "github.com/docker/docker/api/types/container"
    "github.com/docker/docker/pkg/stdcopy"
    "github.com/docker/docker/client"
    "golang.org/x/net/context"
)

func main() {
    ctx := context.Background()
    cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
    if err != nil {
        panic(err)
    }

    reader, err := cli.ImagePull(ctx, "docker.io/library/alpine", types.ImagePullOptions{})
    if err != nil {
        panic(err)
    }
    io.Copy(os.Stdout, reader)

    resp, err := cli.ContainerCreate(ctx, &container.Config{
        Image: "alpine",
        Cmd:   []string{"echo", "hello world"},
        Tty:   true,
    }, nil, nil, "")
    if err != nil {
        panic(err)
    }

    if err := cli.ContainerStart(ctx, resp.ID, types.ContainerStartOptions{}); err != nil {
        panic(err)
    }

    statusCh, errCh := cli.ContainerWait(ctx, resp.ID, container.WaitConditionNotRunning)
    select {
    case err := <-errCh:
        if err != nil {
            panic(err)
        }
    case <-statusCh:
    }

    out, err := cli.ContainerLogs(ctx, resp.ID, types.ContainerLogsOptions{ShowStdout: true})
    if err != nil {
        panic(err)
    }

    stdcopy.StdCopy(os.Stdout, os.Stderr, out)
} 
```

有许多其他语言的库。

| 语言 | 图书馆 |
| --- | --- |
| C | libdocker |
| C# | 码头工人。DotNet |
| C++ | lasso/dock _ client |
| 镖 | bwu _ docker |
| 占线小时 | 埃尔多克尔 |
| Gradle | grad dock 外挂程式 |
| 绝妙的 | 码头工人-客户 |
| 哈斯克尔 | 码头工人 |
| HTML (Web 组件) | docker-元素 |
| Java 语言(一种计算机语言，尤用于创建网站) | 码头工人-客户 |
| Java 语言(一种计算机语言，尤用于创建网站) | docker-java |
| Java 语言(一种计算机语言，尤用于创建网站) | dock Java API |
| NodeJS | 码头大道 |
| NodeJS | 港务长 |
| Perl 语言 | Eixo::Docker |
| 服务器端编程语言（Professional Hypertext Preprocessor 的缩写） | dock-PHP |
| 红宝石 | 坞站 api |
| 锈 | 码头工人铁锈 |
| 锈 | 从商店中偷商品 |
| 斯卡拉 | 拖船 |
| 斯卡拉 | 反应式码头 |
| 迅速发生的 | docker 客户端-swift |

现在，您可以使用 Docker Rest API 创建自己的用户界面。这个 API 可以帮助你实现应用程序的自动化。