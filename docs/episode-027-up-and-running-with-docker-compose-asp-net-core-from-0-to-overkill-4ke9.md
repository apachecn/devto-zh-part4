# 第 027 集-启动并运行 Docker Compose-ASP.NET 核心:从 0 到矫枉过正

> 原文：<https://dev.to/joaofbantunes/episode-027-up-and-running-with-docker-compose-asp-net-core-from-0-to-overkill-4ke9>

在这一集中，我们将使用之前准备的 Docker 容器，并使用 Docker Compose 来运行完整的 PlayBall 应用程序。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/41pDgXUgHhE](https://www.youtube.com/embed/41pDgXUgHhE)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在前一集里，我们开始用 [Docker](https://docs.docker.com/) 准备运行我们的应用程序，并不是因为部署，因为我们离有用的东西有点远，而是为了更容易让所有的东西一起工作——当然，也是为了学习如何去做🙂。

在这一集里，我们将在[Docker composer](https://docs.docker.com/compose/)的帮助下完成这项工作，目标是用一个命令启动并运行 PlayBall 应用程序。

在未来，我们可能会在实际部署中使用 [Kubernetes](https://kubernetes.io/) ，因为它是目前最流行的容器编排工具。不过现在，Docker Compose 已经足够满足我们的需求了。

## 新的环境配置

当我们转移到一个新的环境——从开发到 Docker 开发——一些配置将需要改变，例如 BFF 将不能再通过`localhost:5005`访问 auth 服务。

回想《T2》第 006 集，我们看了一下 ASP.NET 核心的配置，我们知道我们有很多方法在不同的环境中使用不同的配置。最简单的方法可能是添加一个特定于环境的新的`appsettings.json`文件，但是根据配置的类型(例如一些秘密),从不同的提供商那里获取一些配置可能是有意义的。尽管如此，正如我们在开发环境中所做的,`json`文件已经足够好了。

记住这一点，我们将使用 BFF 作为示例，并在 web 应用程序项目的根目录下创建一个名为`appsettings.DockerDevelopment.json`的新设置文件。

作为参考，我们来看看开发设置文件:
`appsettings.Development.json`

```
{  "Logging":  {  "LogLevel":  {  "Default":  "Debug",  "System":  "Information",  "Microsoft":  "Information"  }  },  "GroupManagementApiSettings":  {  "Uri":  "http://localhost:5002"  },  "AuthServiceSettings":  {  "Authority":  "http://localhost:5005",  "RequireHttpsMetadata":  true,  "ClientId":  "WebFrontend",  "ClientSecret":  "secret"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

那么，对于 Docker 开发环境我们有:
`appsettings.DockerDevelopment.json`

```
{  "Logging":  {  "LogLevel":  {  "Default":  "Debug",  "System":  "Information",  "Microsoft":  "Information"  }  },  "GroupManagementApiSettings":  {  "Uri":  "http://groupmanagement"  },  "AuthServiceSettings":  {  "Authority":  "http://auth.playball.localhost",  "RequireHttpsMetadata":  false,  "ClientId":  "WebFrontend",  "ClientSecret":  "secret"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

正如你注意到的，没什么不同。我们要改变的是端点。代替`localhost`，因为它不能工作，因为应用程序将被托管在不同的容器中，每个容器都有自己的`localhost`，我们现在使用名称来引用其他服务。当我们进入 Docker Compose 部分时，我们将更详细地了解它，但是在那个阶段，我们能够定义应用程序可以通过其相互访问的名称。

至于其他组件(授权服务和组管理 API)，配置调整是相同的，所以我就跳过它，但你可以在[“编码民兵:ASP.NET 核心-从 0 到过度杀伤”组织](https://github.com/AspNetCoreFromZeroToOverkill)的各个存储库中查看所有内容。

## 设置反向代理

为了实现与用户的交互，我们将使用一个反向代理，作为我们内部 Docker 网络的单一入口点。用户永远不会直接向除反向代理之外的任何组件发出请求，事实上，这些组件甚至是不可访问的。

提醒一下，我们现在要用的架构是这样的(见[第 021 集](https://blog.codingmilitia.com/2019/05/29/aspnet-021-from-zero-to-overkill-integrating-identityserver4-part1-overview)):

[![architecture diagram](img/89a5805da7b22d819bacbda96406550b.png)](https://thepracticaldev.s3.amazonaws.com/i/uqyvo0j7dy3s5zgnr4bt.jpg)

我们将使用 [HAProxy](http://www.haproxy.org/) 作为反向代理，但我们也可以使用 [Nginx](https://www.nginx.com/) 或另一个服务器。HAProxy 是专门定制的反向代理，而不是一个成熟的 web 服务器，所以在我看来这是一个不错的选择。

> 请注意，我并没有投入太多时间来学习如何使用 HAProxy。我复制了一个基本配置，根据我的需要进行了调整，然后继续使用。请不要盲目跟风生产🙂。

为了保持通用的部署工具，比如这个反向代理和 Docker 组合工具，我在这里创建了一个新的库。在这里，我创建了一个新的`docker`文件夹，然后是一个子文件夹`reverse-proxy`，我们将在其中放置反向代理 Docker 映像的配置。

### HAProxy 配置

转到`reverse-proxy`文件夹，我们首先创建一个名为`haproxy.cfg`的 HAProxy 配置文件。文件内容如下，然后我们将浏览它们。

`haproxy.cfg`

```
global
    daemon
    maxconn 4096

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

# define the public entrypoint
frontend public
    bind *:80

    # Define hosts
    acl host_main hdr(host) -i playball.localhost
    acl host_auth hdr(host) -i auth.playball.localhost

    # define rules to forward to backends
    use_backend auth if host_auth
    use_backend bff if host_main { path_beg /api/ }
    use_backend spa if host_main

# configure backends that process the requests
backend spa
    option forwardfor
    server spa1 spa:80

backend bff
    option forwardfor
    server bff1 bff:80

backend auth
    option forwardfor
    server auth1 auth:80

# configure states endpoint
listen stats 
  bind *:81
  stats enable  
  stats uri /haproxy
  stats auth user:pass 
```

Enter fullscreen mode Exit fullscreen mode

#### 全局和默认设置

前两节，是我从某处复制的一些默认内容😎，但这是不言自明的，因为它将 HAProxy 设置为在 dameon 模式下运行，最大连接数和一些超时。它还将模式设置为`http`(与`tcp`相反)，这允许 HAProxy 检查 HTTP 请求，从而可以基于此创建规则，我们需要将请求转发到正确的组件。`tcp`模式会更快，所以如果我们不需要检查路由请求，这将是一个更好的选择。

#### 处理传入请求

在这些部分之后，我们进入与我们的用例更相关的部分。我们从一个`frontend`部分(名为`public`)开始，在这里我们配置如何处理传入的请求。我们首先将这个前端绑定到端口 80，我们将在其中监听请求——现在我们只使用 HTTP，将来我们希望端口 443 也公开来处理 HTTPS。

我们通过定义两个 ACL(更多信息[在这里](https://www.haproxy.com/blog/introduction-to-haproxy-acls/))来让我们定义请求处理的规则。在本例中，我们使用它来匹配请求的主机。让我们举一个例子来了解一下这是怎么回事。

```
acl host_main hdr(host) -i playball.localhost 
```

Enter fullscreen mode Exit fullscreen mode

*   `acl`是创建命名 ACL 的关键字。
*   `host_main`是我们正在创建的 ACL 的名称。
*   `hdr`允许我们从请求中获取一个头。
*   `host`是我们想要的标题的名称。
*   `-i`执行不区分大小写的匹配。
*   `playball.localhost`是我们要匹配的主机。

定义了这些 ACL 之后，我们现在可以继续定义规则来将请求匹配到正确的后端。定义`use_backend`规则的顺序很重要，因为请求将被转发到第一个匹配的后端。

> 关于这些主机，我在我的计算机的`hosts`文件中定义了它们，指向本地主机。

```
use_backend auth if host_auth 
```

Enter fullscreen mode Exit fullscreen mode

首先，如果一个请求与`auth`后端(即授权服务)匹配，我们将立即转发它。

```
use_backend bff if host_main { path_beg /api/ } 
```

Enter fullscreen mode Exit fullscreen mode

然后我们定义另一个后端规则，声明如果主机匹配`host_main`并且路径以`/api/`开始，我们希望将请求转发给 BFF。

> 附带说明，当我们讨论到 BFF 的路由时，我最终在 BFF 应用程序的路由中包含了`api`前缀，以避免这些匹配和登录流程中发生的重定向带来的麻烦。我肯定会有另一个解决方案，但我觉得目前不值得花这么大力气。

```
use_backend spa if host_main 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果主机与`host_main`匹配，请求将被转发到 SPA。记住顺序很重要，如果我们把这个规则放在前一个规则之前，BFF 就不会永远匹配，所以所有对`playball.localhost`的请求最终都会被转发到 SPA 后端。

#### 定义处理请求的后端

在`frontend`部分，我们定义了将请求匹配到后端的规则，现在我们需要定义那些后端。我们将抓住其中的一个作为例子，因为它们的定义都是一样的，而且也不太复杂。

```
backend spa
    option forwardfor
    server spa1 spa:80 
```

Enter fullscreen mode Exit fullscreen mode

我们从定义后端的关键字`backend`开始，然后是我们想要给它起的名字。这是我们在`frontend`定义中使用的名称。

然后我们有另外几行。`option forwardfor`，告诉 HAProxy 在转发到后端的请求中包含`X-Forwarded-For`头。

最后，我们使用`server`设置来指示请求应该被转发到的实际服务器。我们为这个服务器设置一个名称(`spa1`)，然后设置它的位置，正如我们在查看 Docker Compose 配置时看到的，它是端口 80 上的主机`spa`。我们只定义了一个服务器，但是我们可以有多个，HAProxy 将充当它们之间的负载平衡器。

#### 统计页面

总结 HAProxy 的配置，我们将 HAProxy 提供的统计页面配置为可访问。这不是我们想在网络上公开的东西，所以要小心。

`HAProxy statistics page`
[![HAProxy statistics page](img/d25405f963c46032e0f861a6f05d2660.png)T4】](https://thepracticaldev.s3.amazonaws.com/i/wi76n0un2c0b19xa5w17.png)

### 创建 Docker 容器图像

我们现在已经准备好了配置，所以我们可以用它来创建一个新的 Docker 容器映像，它将被用作 Docker Compose 部署的一部分。

在`reverse-proxy`文件夹中，我们创建一个新的`Dockerfile`来描述图像。

```
FROM haproxy:alpine

## Copy the config with the proxy settings
COPY haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg 
```

Enter fullscreen mode Exit fullscreen mode

非常简单`Dockerfile`，因为我们只想复制到配置文件中，其余的可以保持不变。

或者，我们可以在直接启动 HAProxy 容器时挂载一个包含配置文件的文件夹，而不是创建一个基于 HAProxy 的容器。从 [Docker Hub](https://hub.docker.com/_/haproxy) 的文档:

```
docker run -d --name my-running-haproxy -v /path/to/etc/haproxy:/usr/local/etc/haproxy:ro haproxy:1.7 
```

Enter fullscreen mode Exit fullscreen mode

## 创建 Docker 撰写文件

做完这些准备，我们最后来看看 Docker Compose。从他们的[文档](https://docs.docker.com/compose/):

> Compose 是一个定义和运行多容器 Docker 应用程序的工具。使用 Compose，您可以使用 YAML 文件来配置应用程序的服务。然后，只需一个命令，您就可以从您的配置中创建并启动所有服务。

这正是我们获得 PlayBall 应用程序所需要的，play ball 应用程序由一堆应用程序组成(而且数量会增加)，开始时没有太多麻烦。

在[新资源库](https://github.com/AspNetCoreFromZeroToOverkill/Deployment/tree/episode027)中创建的`docker`文件夹中，我们将创建一个名为`docker-compose.dev.yml`的新文件，在这里我们将设置所有内容。

这一次，我不会放弃整个文件，而是一部分一部分地进行，但是您可以在 GitHub repo 中看到所有内容。

```
version: "3"
networks:
  internal-network: 
```

Enter fullscreen mode Exit fullscreen mode

文件的第一行，简单地陈述了我们正在使用的 Docker Compose 文件的版本。

之后，我们定义我们想要使用的网络，我们正在定义一个名为`internal-network`的网络。这不是强制配置，但我们出于几个原因想要使用它:

*   容器在隔离的网络中运行，只能在同一网络的容器中通信(当然，除非暴露在互联网中)。
*   同一网络中的容器不需要知道彼此的 IP 地址，让这些网络提供基于容器名称的自动 DNS 解析。这就是为什么在我们之前看到的例子中(`appsettings`文件或 HAProxy 配置文件)，我们可以使用像`spa`或`bff`这样的地址。

在这之后，是 Docker 编写文件服务定义的主要部分。

```
services:
    reverse-proxy:
        build: ./reverse-proxy/.
        image: codingmilitia/reverseproxy:latest
        ports:
            - "80:80"
            - "81:81"
        networks:
            - internal-network
        depends_on:
            - spa
            - bff
            - auth
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

标志着我们希望作为我们整个应用程序的一部分的所有容器的配置的开始。

我们从配置反向代理开始。我们给它一个名称，然后是构建图像所需的文件位置的路径，接下来是我们希望用于结果图像的名称和标签。

`ports`条目定义了我们想要向主机公开的端口。您会注意到这只出现在反向代理定义中，其余的容器将无法从外部访问，因为反向代理应该是唯一的入口点(当然，出于调试目的，我们也可以公开其他容器)。

`networks`条目配置我们希望容器成为其中一部分的网络。在这个文件中，所有的容器都将使用我们上面讨论过的同一个网络。

最后，`depends_on`指出在这个服务启动之前应该运行什么服务。然而，这并没有解决关于依赖性的所有问题，因为它只是等待其他容器启动，它不知道在这些容器中运行的应用程序实际上是否准备好了(例如，web 应用程序可能正在运行迁移，但还没有监听请求)。

```
services:
    # ...
    spa:
        build: ../../WebFrontend/client/.
        image: codingmilitia/webfrontend/spa:latest
        networks:
            - internal-network
        depends_on:
            - bff
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

SPA 定义比反向代理简单，所以这里不多谈了。注意，SPA 容器实际上并不需要 BFF 来运行，但是从应用程序使用的角度来看，它需要 BFF，所以我添加了依赖项。

此外，请注意构建路径假设所有的 repos 都在兄弟文件夹中，因此如果您下载这些 repos 并希望 Docker Compose 工作，您需要有相同的文件夹结构或调整路径。

```
services:
    # ...
    bff:
        build: ../../WebFrontend/server/.
        image: codingmilitia/webfrontend/bff:latest
        networks:
            - internal-network
        depends_on:
            - groupmanagement
        environment:
            - ASPNETCORE_ENVIRONMENT=DockerDevelopment
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

再一次，没有太多关于 BFF 的话要说，因为大部分已经讨论过了。唯一需要注意的是，我们正在设置一个环境变量，因此应用程序使用我们创建的新的`appsettings.DockerDevelopment.json`文件，以正确的环境配置运行。

```
services:
    # ...
    groupmanagement:
        build: ../../GroupManagement/.
        image: codingmilitia/groupmanagement:latest
        networks:
            - internal-network
        depends_on:
            - postgres-db
        environment:
            - ASPNETCORE_ENVIRONMENT=DockerDevelopment
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

组管理 API 配置与 BFF 非常相似。

```
services:
    # ...
    auth:
        build: ../../Auth/.
        image: codingmilitia/auth:latest
        networks:
            internal-network:
                aliases:
                    - auth.playball.localhost # trick to be able to access auth from inside and outside the Docker network
        depends_on:
            - postgres-db
        environment:
            - ASPNETCORE_ENVIRONMENT=DockerDevelopment
    # ... 
```

Enter fullscreen mode Exit fullscreen mode

至于 auth 服务，它几乎与 BFF 和组管理 API 相同，但是有一个额外的网络配置:设置一个别名。

您可能已经注意到，在进行配置时(即`appsettings`文件),我们通过在组合文件中定义的名称(例如`bff`或`groupmanagement`)来引用服务，除了 auth 服务，在 auth 服务中我们使用一个更“看起来正常”的主机名`auth.playball.localhost`。

这里的问题是主机`auth`将在内部组合网络中通信时工作，因此，例如，BFF 可以使用`auth`作为与 auth 服务通信的授权。然而问题是，当 BFF 需要重定向到 auth 服务以供用户登录时，它会重定向到`http://auth/Login?ReturnUrl=RETURN_URL`，正如我们所讨论的，这是不能从浏览器访问的。为了解决这个问题，我们可以在 Compose 文件中设置一个别名(或者我们可以只改变服务的名称)，这样同一个`auth.playball.localhost`就可以用于从 Docker 网络的内部和外部访问 auth 服务。

```
services:
    # ...
    postgres-db:
        image: "postgres"
        networks:
            - internal-network
        environment:
            POSTGRES_USER: "user"
            POSTGRES_PASSWORD: "pass" 
```

Enter fullscreen mode Exit fullscreen mode

为了包装合成文件，我们配置数据库。在这种情况下，它不是基于我们创建的容器映像，所以没有`build`条目。其余的与我们看到的类似，通过环境变量传入一些超级安全的凭证。

## 运行应用程序

好了，我们终于为运行应用程序做好了一切准备。如前所述，现在运行它是一个运行单个命令的问题:

```
docker-compose -f docker-compose.dev.yml up -d --build 
```

Enter fullscreen mode Exit fullscreen mode

基本命令是`docker-compose -f docker-compose.dev.yml up`，它将告诉 Docker Compose 启动由`docker-compose.dev.yml`文件描述的应用程序。然后我们有一些额外的标志，你可能想用也可能不想用。

*   `-d`和我们在上一集看到的`docker run`有相同的意思，让它以守护模式运行。
*   `--build`表示我们想要构建(或重建)服务容器映像。在我们改变和尝试事物时很有用。

如果我们做一个`docker ps`我们应该看到我们所有的容器都在运行。但是有时，由于一个服务准备就绪需要时间，依赖于它的容器可能会失败——例如，PostgreSQL 启动时间稍长，使得组管理 API 失败。我们可以做一些事情来避免这种情况，比如在我们的应用程序中内置重试逻辑，在组合文件中添加一些配置来处理容器故障等等，但是考虑到我们只是使用这些来简化应用程序的测试，让容器在故障时保持关闭甚至可能对我们更好地了解正在发生的事情有好处。

我们现在可以进入浏览器，输入`http://playball.localhost`(不要忘记将这两个条目添加到 hosts 文件中)，然后我们的应用程序就会向我们问好，给出一个命令就可以运行🙂。我们也可以去`http://localhost:81/haproxy`看看它提供的统计数据。

## 其他

这一集到此为止。我们现在能够毫无困难地运行我们的 PlayBall 应用程序，因此我们可以测试到目前为止我们实现的特性。

我们还有很长的路要走，直到我们可以使用该应用程序做一些有用的事情，但至少我们正在探索许多主题。

帖子中的链接:

*   [码头工人](https://docs.docker.com/)
*   [坞站组成](https://docs.docker.com/compose/)
*   [Kubernetes](https://kubernetes.io/)
*   [HAProxy](http://www.haproxy.org/)
*   [HAProxy 配置的四个基本部分](https://www.haproxy.com/blog/the-four-essential-sections-of-an-haproxy-configuration/)
*   [ha proxy ACL 简介](https://www.haproxy.com/blog/introduction-to-haproxy-acls/)
*   [Nginx](https://www.nginx.com/)

这篇文章的源代码分布在[“编码民兵:ASP.NET 核心——从 0 到过度杀戮”组织](https://github.com/AspNetCoreFromZeroToOverkill)的存储库中，标记为`episode027`。

*   [认证](https://github.com/AspNetCoreFromZeroToOverkill/Auth/tree/episode027)
*   [群组管理](https://github.com/AspNetCoreFromZeroToOverkill/GroupManagement/tree/episode027)
*   [web 前端](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode027)
*   [部署](https://github.com/AspNetCoreFromZeroToOverkill/Deployment/tree/episode027)

感谢分享和反馈！

谢谢你的来访，西阿兹！