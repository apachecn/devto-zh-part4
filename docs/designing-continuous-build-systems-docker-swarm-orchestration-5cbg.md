# 设计连续构建系统:Docker 群体编排

> 原文：<https://dev.to/tryexceptpass/designing-continuous-build-systems-docker-swarm-orchestration-5cbg>

构建代码有时就像执行脚本一样简单。但是一个全功能的构建系统需要更多的支持基础设施来同时处理多个构建请求、管理计算资源、分发工件等。

在我们讨论了构建事件的最后一章之后，[连续构建](https://tryexceptpass.org/article/continuous-builds-1/)系列的下一次迭代将介绍如何在 Docker Swarm 中启动一个容器来运行构建并测试它。

## 什么是 Docker 蜂群

当在[群体模式](https://docs.docker.com/engine/swarm/)下运行 Docker 引擎时，你有效地创建了一个集群。Docker 将管理许多计算节点和它们的资源，调度运行在容器中的工作。

它处理跨节点的扩展，同时保持整体集群状态，这样您可以调整集群中运行的工作容器的数量，在节点离线时自动进行故障转移，等等。

它还构建了必要的网络挂钩，以便容器可以跨多个主机节点相互通信。它实现了负载平衡、滚动更新和许多其他您期望从集群技术中获得的功能。

### 集群设置

当计算主机构成 Docker 群的一部分时，它们可以以管理器模式运行，也可以作为常规的工作节点运行。这些节点能够按照管理器的指示来托管容器。

一个群可以有多个管理器，管理器本身也可以托管容器。他们的工作是跟踪集群的状态，并根据需要在节点间旋转容器。这允许跨集群的冗余，以便您可以释放一个或多个管理器或节点，并保持基本操作运行。关于这个的更多细节可以在官方的 Docker Swarm 文档中找到。

要创建一个 swarm，您需要在您的第一个管理器(也是您的第一个节点)上运行以下命令。

```
docker swarm init 
```

前面的命令会告诉你在每个节点上运行什么来加入这个群体。通常看起来是这样的:

```
docker swarm join --token SOME-TOKEN SOME_IP:SOME_PORT 
```

默认情况下，docker 守护进程监听位于`/var/run/docker.sock`的`unix`套接字。这对于本地访问来说很好，但是如果你需要远程访问，你必须显式地启用`tcp`套接字。

### 启用远程访问

Docker Swarm 为管理服务提供了一个很好的 API，但是对于我们的特定用例，我们需要一个在 Swarm 级别不可用的特性，并且需要对节点进行单独访问。部分原因是因为我们在一个特殊情况下使用 Swarm，而它并不是为这个特殊情况而构建的:运行一个一次性的短期容器——稍后将详细介绍。

您必须在您的节点守护进程上启用远程访问，以便直接连接到它们。这样做似乎会因 Linux 版本、发行版和 docker 配置文件的位置而有所不同。然而，主要目标是相同的:您必须在守护进程服务执行中添加一个`-H tcp://IP_ADDRESS:2375`选项，其中`IP_ADDRESS`是它监听的接口。

你会发现大多数例子都将它设置为`0.0.0.0`，这样任何人都可以连接到它，但是我建议将它限制在一个特定的地址，以获得更好的安全性——更多内容见下文。

我在一个 Ubuntu 映像上，它使用了`/lib/systemd/system/docker.service`中的文件来定义守护进程选项。您只需找到以`ExecStart=...`开头或包含`-H`的行，并添加前面提到的额外的-H 选项。

不要忘记，在做出更改后，您必须重新加载守护程序配置并重启 docker:

```
sudo systemctl daemon-reload
sudo service docker restart 
```

我见过其他在`/etc/default/docker`下跟踪这些设置的发行版，还有一个在`/etc/systemd/system/docker.service.d/`中使用一个文件。你应该在谷歌上搜索与你的操作系统风格相匹配的`docker daemon enable tcp`或`docker daemon enable remote api`。

### 安全隐患

鉴于您可以使用 Docker 做的事情的性质，有必要指出为远程访问启用 TCP 套接字是一个非常严重的安全风险。它基本上向远程代码执行开放了您的系统，因为任何人都可以连接到该套接字并启动或停止容器、查看日志、修改网络资源等。

为了减轻这种情况，您需要启用证书验证和 TCP 套接字。这使得守护程序验证潜在客户端使用的 HTTPS 证书是否由预定义的证书颁发机构(CA)签名。

在将客户端证书分发到执行编排(通常是您的构建服务)的计算系统之前，您需要创建证书颁发机构并签署所有客户端证书。

关于如何生成证书、执行签名和启用验证选项的步骤可在 Docker 文档中找到，用于保护守护程序。

### 服务、任务和容器

在群集模式下运行时，Docker 术语会有一些变化。你不再只关心容器和图像，还关心`tasks`和`services`。

服务定义了组成集群中运行的应用程序的所有部分。这些片段是任务，每个任务是一个容器的定义。

例如，如果您有一个运行 Flask API 的应用程序 ABC，您希望在两个节点之间实现负载平衡，那么您可以用两个任务定义一个 ABC 服务。swarm 负责保持它们在两个节点上运行(即使集群中有更多的节点),并且还配置网络，以便无论连接到哪个节点，都可以通过同一个端口获得服务。

要运行的任务数量是复制策略的一部分，swarm 使用该策略来确定要在集群中保持运行的任务副本的数量。你不仅可以将它设置为一个特定的数字，还可以在全局模式下配置它，在集群的每个节点上运行任务的副本。

这些概念在原理上很简单，但是当你以后试图做更复杂的事情时，就会变得棘手。所以我建议你查看一下[服务文档](https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/)以获得更多关于它如何工作的信息。

使用这个术语来描述我们的用例:对于每个新的构建请求，您将在 swarm 中运行一个新的服务，该服务包含一个任务实例和一个执行构建的容器。群调度器将负责在任何可用的节点上提供它。容器应该在工作完成后删除自己。

### 替代品

如前所述，Docker Swarm 及其概念旨在维护集群内长期运行的复制服务。但是我们有一个非常特殊的例子，每次构建都执行单拷贝的 ephimeral 服务。

我们不关心高可用性或负载平衡特性，我们希望它具有容器调度功能。

虽然做起来很简单，但因为它不是为此而构建的，所以会让人觉得我们是在把事情强加在一起。所以另一个选择是构建我们自己的调度器(或者使用现有的调度器)并让它在容器内执行工作。

对于类似于 Celery 或 [Dramatiq](https://dramatiq.io/) 的现有任务系统来说，这并不难，它们使用 RabbitMQ 这样的工作队列来分发容器管理任务。

同样，您可以为相同的目标重用分布式计算系统。为此，我已经成功部署了 Dask。它甚至简化了一些工作流程，并使其他工作流程成为可能。

我知道我以前说过很多次了，但我还是要再说一遍。像软件工程(和生活)中的大多数选择一样，您总是将一组问题换成另一组问题。在这种情况下，您将工作流的复杂性换成了基础架构和维护的复杂性，因为您现在必须保持工作线程运行并监听队列，以及随着软件的发展处理这些线程的更新。这是通过使用 Docker Swarm 为您完全抽象出来的。

## 用 Python 编排

Docker 人员维护的官方 Python 库是 [docker](https://docker-py.readthedocs.io/en/stable/) 模块。它包装了所有主要的构造，并且易于使用。我已经利用它有一段时间了。

该库与 docker 守护进程 REST API 进行交互。守护程序的命令接口使用资源 URL 上的 HTTP 动词来传输 JSON 数据。例如:列出容器是对`/containers/json`的访问，创建卷是对`/volumes/create`的提交，等等。

如果您感兴趣，请访问 [Docker API 参考文献](https://docs.docker.com/engine/api/latest/)了解更多详细信息。

### apiclient vs dock client

`docker`模块本身暴露了与守护进程通信的两个“层”。它们表现为不同的客户类别:`APIClient`和`DockerClient`。前者是直接围绕接口端点的低级包装器，而后者是客户机之上的面向对象的抽象层。

出于我们今天的目的，我们将能够坚持使用`DockerClient`的实例来执行所有操作。去下级很少，但有时是必须的。这两个接口在之前分享的链接中都有很好的记录。

创建客户端非常简单。用`pip install docker`安装好模块后，可以导入客户端类，实例化它，不需要任何参数。默认情况下，它将连接到前面提到的 unix 套接字。

```
from docker import DockerClient
dock = DockerClient() 
```

`DockerClient`类遵循一个通用的“client.resource.command”架构，这使得它使用起来很直观。例如:你可以用`client.containers.list()`列出集装箱，或者用`client.images.get('python:3-slim')`查看图像细节。

每个资源对象都有通用动作的方法，如`list()`、`create()`或`get()`，以及特定于资源本身的方法，如容器的`exec_run()`。

所有属性都可以通过字典形式的`.attrs`属性获得，`reload()`方法获取资源的最新信息并刷新实例。

为了实现我们的目标，您需要为每个构建创建一个服务，找到执行其任务的节点，对该任务内的容器进行一些更改，并启动容器。

## 执行脚本

配置一个执行构建的群服务只是成功的一半。另一半是按照我们在前面章节中定义的指令编写代码，以构建、测试、记录结果和分发工件。这就是我所说的执行脚本。

我们将在以后的文章中讨论脚本本身如何工作的细节。现在，知道它是每个构建容器在启动时运行的命令就足够了。这是相关的，因为它带来了我们必须处理的另一个问题:执行脚本是用 Python 编写的，但是构建容器不需要安装 Python。

我设计并实现了需要 Python 执行的持续集成系统，也设计并实现了不需要 Python 执行的持续集成系统。一个给使用系统的开发人员增加了复杂性和约束，另一个给系统的维护人员增加了复杂性和约束。

如果您只构建 Python 代码是一个众所周知的事实，那么这并不重要，因为正在构建的代码库中使用的 docker 映像已经安装了 Python。

如果不是这样，那么您可能会看到构建时间和复杂性的大幅增加，甚至可能会限制受支持的映像，因为开发人员必须将 Python 安装到容器中作为他们构建的一部分。

这些天来，我的选择是将执行脚本打包，使其可以在任何 docker 映像中运行。我在以前的一篇关于将 Python 模块打包成可执行文件的文章中记录了我的尝试。那里包括了如何制作一个包的细节。

## 构建和执行测试

准备好所有的材料后，是时候深入研究创建新服务并运行构建的代码了。如本系列前面章节中所定义的，以下步骤假设:

*   正在构建的存储库在其根目录下有一个带有构建指令的 YAML 文件。
*   这个配置文件包含一个`image`指令，它定义了用于构建的 Docker 映像。
*   还有一个`environment`指令，用户可以在其中设置环境变量。
*   处理构建事件的 webhook 函数已经检索了构建配置信息，并将其存储在一个名为`config`的字典中，并在一个`pr` dict 中提供了 pull 请求信息。

### 创建服务

在制作服务的时候，你需要考虑给它取什么名字，在搜索 swarm 的时候如何找到它。

服务名必须是唯一的，并且为了简化基础设施维护，它们应该是描述性的。我更喜欢用一个后缀`-{repo_owner}-{repo_name}-{pr_number}-{timestamp}`。这些名字也有字符串大小的限制，所以注意不要太有创意。

因为您不希望同一个 pull 请求有重复的构建，所以您还需要能够以编程方式在集群中搜索正在运行的构建。换句话说，如果我正在为一个给定的 pull 请求执行一个构建，如果我在同一个 PR 中提交了新代码，那么允许构建完成是没有意义的。你不仅在浪费资源，而且即使构建成功完成，也没有用，因为它已经过时了。

为了处理这种情况，我使用了`labels`。一个服务可以有一个或多个标签，标签上有关于它是什么和它在做什么的元数据。Docker API 还提供了基于这些元数据进行过滤的方法。

下面的代码利用这些函数来确定构建是否已经在运行，并在创建新服务之前停止它。

```
import logging
import docker

...

def execute(pr, action=None, docker_node_port=None):
    ...

    # Get environment variables defined in the config
    environment = config['environment'] if 'environment' in config and isinstance(config['environment'], dict) else {}

    # Get network ports definition from the config
    ports = docker.types.EndpointSpec(ports=config['ports']) if 'ports' in config and isinstance(config['ports'], dict) else None

    environment.update({
        'FORGE_INSTALL_ID': forge.install_id,
        'FORGE_ACTION': 'execute' if action is None else action,
        'FORGE_PULL_REQUEST': pr['number'],
        'FORGE_OWNER': owner,
        'FORGE_REPO': repo,
        'FORGE_SHA': sha,
        'FORGE_STATUS_URL': pr['statuses_url'],
        'FORGE_COMMIT_COUNT': str(pr['commits'])
    })
    logging.debug(f'Container environment\n{environment}')

    # Connect to the Docker daemon
    dock = docker.DockerClient()

    # Stop any builds already running on the same pr
    for service in dock.services.list(filters={'label': [f"forge.repo={owner}/{repo}", f"forge.pull_request={pr['number']}"]}):
        logging.info(f"Found service {service.name} already running for this PR")

        # Remove the service
        service.remove()

    # Create the execution service
    service_name = f"forge-{owner}-{repo}-{pr['number']}-{datetime.now().strftime('%Y%m%dT%H%M%S')}"
    logging.info(f"Creating execution service {service_name}...")

    service = dock.services.create(
        config['image'],
        command=f"/forgexec",
        name=service_name,
        env=[f'{k}={v}' for k, v in environment.items()],
        restart_policy=docker.types.RestartPolicy('none'),
        labels={
            'forge.repo': f'{owner}/{repo}',
            'forge.pull_request': str(pr['number']),
        }
    ) 
```

注意，在创建服务之前，我们不仅要获取构建配置中定义的环境变量，还要添加额外的内容来描述我们正在采取的操作。它们传递关于存储库的相关信息，并将请求构建到执行脚本中。

正如你所看到的，我们使用`.services.list()`来获得当前运行在 swarm 中的服务列表，这些服务使用我们之前描述的标签进行过滤。如果一个服务存在，调用`service.remove()`也会删除它的容器。

创建服务是对`.services.create()`的调用，在这里我们传递:

*   服务所基于的容器映像——在我们的构建配置中定义。
*   启动时要执行的命令，这是我们的执行脚本的名称——在本例中为`forgexec`。
*   服务名称。
*   环境变量被定义为格式为`NAME=VALUE`的字符串列表，所以我们使用列表理解从环境字典中转换它们。
*   重启策略是 Docker 用来定义在主机重启时如何处理容器的术语。在我们的例子中，我们不希望它们自动上线，所以我们将其设置为`none`。
*   带有我们之前描述的元数据的标签。

### 获取任务信息

一旦 swarm 创建了服务，它需要几秒钟的时间来初始化它的任务，并提供运行它的节点和容器。因此，我们通过检查分配给服务的任务总数来等待它可用:

```
 # Wait for service, task and container to initialize
    while 1:
        if len(service.tasks()) > 0:
            task = service.tasks()[0]
            if 'ContainerStatus' in task['Status'] and 'ContainerID' in task['Status']['ContainerStatus']:
                break
        time.sleep(1) 
```

构建服务中只有一个任务，所以我们可以假设它是列表中的第一个。每个`task`都是一个字典，包含描述运行它的容器的属性。

这里有两个延迟:一个是在任务被分配之前，另一个是在容器开始执行任务之前。因此，在继续之前，有必要等到容器信息在任务细节中可用。

### 将执行脚本复制到容器中

如前所述，您需要将我们打包的执行脚本复制到每个构建容器中，以便它能够启动——相当于一个`docker cp`。

将数据复制到容器中需要对文件进行压缩，所以在事件服务器脚本的最开始，我们使用`tarfile` Python 模块:
创建了一个`tar.gz`文件

```
if __name__ == '__main__':
    # Setup the execution script tarfile that copies into containers
    with tarfile.open('forgexec.tar.gz', 'w:gz') as tar:
        tar.add('forgexec.dist/forgexec', 'forgexec') 
```

这意味着我们有一个`forgexec.tar.gz`文件可以用 docker 模块提供的`container.put_archive()`函数进行传输。每次 webhook 事件服务器启动时都这样做，并覆盖任何现有的文件，以确保您没有使用过时的代码。

将文件转移到容器中需要我们直接连接到 swarm 节点。docker 服务级别没有接口来帮助我们做到这一点。这就是为什么我们必须更早地启用远程访问。

首先，我们从任务中获得关于 docker 节点的信息，然后我们创建一个新的`DockerClient`实例，它连接到节点:

```
 node = dock.nodes.get(task['NodeID'])
    nodeclient = docker.DockerClient(f"{node.attrs['Description']['Hostname']}:{docker_node_port}") 
```

这一次，实例化使用节点监听的 tcp 端口(在集群设置期间配置)和节点的主机名。根据您的网络和 DNS 设置，您可能希望使用`socket`模块来帮助进行域名解析。类似于`socket.gethostbyname(node.attrs['Description']['Hostname'])`的东西可能已经足够好了。

此时我们可以直接访问容器，将文件复制到其中并启动它:

```
 # Get container object
    container = nodeclient.containers.get(task['Status']['ContainerStatus']['ContainerID'])

    # Copy the file
    with open('forgexec.tar.gz', 'rb') as f:
        container.put_archive(path='/', data=f.read())

    # Start the container
    container.start() 
```

## 拼凑

下面是我们新的`execute()`函数，它合并了来自 [webhook 事件处理](https://dev.to/aricle/continuous-builds-webhooks/)章节的代码:

```
def execute(pr, action=None, docker_node_port=None):
    """Kick off .forge.yml test actions inside a docker container"""

    logging.info(f"Attempting to run {'' if action is None else action} tests for PR #{pr['number']}")

    owner = pr['head']['repo']['owner']['login']
    repo = pr['head']['repo']['name']
    sha = pr['head']['sha']

    # Select the forge for this user
    forge = forges[owner]

    # Get build info
    config = get_build_config(owner, repo, sha)

    if config is None or config.get('image') is None or config.get('execute') is None:
        logging.info('Unable to find or parse the .forge.yml configuration')
        return

    # Get environment variables defined in the config
    environment = config['environment'] if 'environment' in config and isinstance(config['environment'], dict) else {}

    # Get network ports definition from the config
    ports = docker.types.EndpointSpec(ports=config['ports']) if 'ports' in config and isinstance(config['ports'], dict) else None

    environment.update({
        'FORGE_INSTALL_ID': forge.install_id,
        'FORGE_ACTION': 'execute' if action is None else action,
        'FORGE_PULL_REQUEST': pr['number'],
        'FORGE_OWNER': owner,
        'FORGE_REPO': repo,
        'FORGE_SHA': sha,
        'FORGE_STATUS_URL': pr['statuses_url'],
        'FORGE_COMMIT_COUNT': str(pr['commits'])
    })
    logging.debug(f'Container environment\n{environment}')

    # Connect to the Docker daemon
    dock = docker.DockerClient(docker_host)

    # Stop any builds already running on the same pr
    for service in dock.services.list(filters={'label': [f"forge.repo={owner}/{repo}", f"forge.pull_request={pr['number']}"]}):
        logging.info(f"Found service {service.name} already running for this PR")

        # Remove the service
        service.remove()

    # Create the execution service
    service_name = f"forge-{owner}-{repo}-{pr['number']}-{datetime.now().strftime('%Y%m%dT%H%M%S')}"
    logging.info(f"Creating execution service {service_name}...")

    service = dock.services.create(
        config['image'],
        command=f"/forgexec",
        name=service_name,
        env=[f'{k}={v}' for k, v in environment.items()],
        restart_policy=docker.types.RestartPolicy('none'),
        # mounts=[],
        labels={
            'forge.repo': f'{owner}/{repo}',
            'forge.pull_request': str(pr['number']),
        }
    )

    # Wait for service, task and container to initialize
    while 1:
        if len(service.tasks()) > 0:
            task = service.tasks()[0]
            if 'ContainerStatus' in task['Status'] and 'ContainerID' in task['Status']['ContainerStatus']:
                break
        time.sleep(1)

    node = dock.nodes.get(task['NodeID'])
    nodeclient = docker.DockerClient(f"{node.attrs['Description']['Hostname']}:{docker_node_port}")
    container = nodeclient.containers.get(task['Status']['ContainerStatus']['ContainerID'])

    with open('forgexec.tar.gz', 'rb') as f:
        container.put_archive(path='/', data=f.read())

    container.start() 
```

## 接下来是什么？

本文向您详细介绍了使用 Docker Swarm 在集群内部构建代码所需的配置计算。除了前面关于处理存储库事件和定义执行构建所需的指令的章节之外，您已经准备好进入下一个章节，讨论执行脚本本身。您需要为管道中的不同阶段配置构建环境、运行命令和执行测试。

* * *

## 了解更多！

订阅[tryexceptpass.org 邮件列表](https://tinyurl.com/tryexceptpass-signup)获取更多关于 Python、Docker、开源以及我们在企业软件工程方面的经验的内容。