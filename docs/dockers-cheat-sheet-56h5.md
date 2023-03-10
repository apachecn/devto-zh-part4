# 码头工人备忘单

> 原文：<https://dev.to/vikashagrawal/dockers-cheat-sheet-56h5>

# 命令:

**docker 运行 ubuntu** :第一次，它从 docker hub 下载 ubuntu 镜像并运行。
**docker ps** :它列出了所有正在运行的集装箱。
**docker PS–a**:列出所有容器，包括之前停止的图像。
**停靠站停止 container _ id/container _ name**:停止正在运行的停靠站。
**docker RM container _ id/container _ name**:删除已经停止的 docker，因此 docker PS–a 不会将其列出。这需要多个由空格分隔的容器标识/容器名称。
**docker exec conatiner _ name 命令**:将执行 docker conatiner_name 内部的命令。
如果运行 docker 时没有传递命令，它会立即退出。所以在我们执行“docker run centos”之后，命令“docker ps”不会列出任何活动的 docker。但是在我们执行“docker run -d centos sleep 2000”之后,“docker ps”这个命令会列出活动的 docker 2000 秒。然后 docker stop container _ id/container _ name 会停止这个 docker。从“docker ps -a”状态退出的代码值将解释关机模式。
**docker run image:tag**:它从 docker hub 中拉出特定的标签并运行。
Docker 以两种模式运行:

```
• Attach: default mode. In this mode, you can’t come out to your docker terminal, to come out to docker terminal, you need to open another terminal and stop this docker.
• Detach: to run docker in detached mode, you need to append –d to run “docker run –d ubuntu” and to attach it, run “docker attach conatiner_name” 
```

Enter fullscreen mode Exit fullscreen mode

**交互模式**:如果你的应用需要在交互模式下运行，你需要追加'-i '，例如" docker run-I image "
docker inspect " container _ id ":它会帮助你了解 docker 的所有细节，包括内部 ip。
docker 标签 imageid login-id/imagename
映射

```
• Port mapping: the system where docker is installed is called docker host. When the container is run, this particular docker gets internal ip, which is not accessible from outside. That means the particular application (e.g. web, mysql etc) can be accessible from within the docker host itself. To access the application from outside, the application port of this docker ip has to be mapped with the port of the docker host ip using “-p” command e.g. “docker run –p 3306:3306 mysql”
• Volume Mapping: any docker image, let’s say DB is run, it will have the volume present inside the docker container, once this particular docker container is stopped and removed, the data will also be lost. To save the data, volume mapping option with “-v” command can be used e.g. “docker run –v /data/:/var/lib mysql” 
```

Enter fullscreen mode Exit fullscreen mode

**docker images** :列出所有下载的图片。
**docker rmi ubuntu** :它移除 ubuntu 镜像，但是在移除之前，你需要确保无论是在运行状态(docker ps)还是退出状态(docker PS–a)都不存在 docker 的实例。
**docker pull ubuntu** :它只会从 docker hub 中拉出 docker 图片。

# DOCKERFILE

它包含两个部分:

```
• Command: FROM RUN COPY ENTRYPOINT
• Instructions: the commands to be passed to the “command”
building docker works on layer and if any of the steps fails, it starts building from the failed step. 
```

Enter fullscreen mode Exit fullscreen mode

**docker history image_name** :该命令将列出所有步骤的细节，包括尺寸。
定义 docker 文件的最佳方式:

```
• Run the docker image for the given OS (which will be input for FROM)
• Perform all the steps.
• Now open docker file and put the OS name with FROM tag (from step 1).
• Put all the steps from step 2 as individual “RUN”
• Save the docker file.
• Build the docker file as “docker build .”, but this will give the container id. Better to build with “docker build . Org-id/container-id” 
```

Enter fullscreen mode Exit fullscreen mode

# 坞站复合 YML 文件

Docker-compose.yml 文件:它用于模拟实际生产环境，我们可以在其中组合多个 Docker。
服务:
Web:
图片:【简单-Web app】
端口:
-【80:5000】
数据库:
图片:【MySQL】
卷:
-"/opt/data:/var/lib/MySQL "
命令:
o **docker-compose up** :调出应用程序。
o**docker-compose stop**:停止所有应用
o**docker-compose down**:移除所有容器。

# 码头工人蜂拥而来

它提供了管理工作者/节点的管理器。Manager 将部署 docker-compose.yml 文件，根据 deploy 下的说明，它将拥有与之关联的节点。

# 网络:

桥北/西:

```
o   Docker run ubuntu
o   by default, all the docker images run in a bridge network.
o   In this, each container can access the other container using internal ip.
o   For the container to be accessible outside the host, port has to be mapped.
o   The ips will be in the range of 172.17.0.X 
```

Enter fullscreen mode Exit fullscreen mode

无

```
o   Docker run ubuntu –network=none
o   It's not accessible from any container. 
```

Enter fullscreen mode Exit fullscreen mode

主持人:

```
o   Docker run ubuntu –network=host
o   The port, if any, runs on the host network.
o   Means any images, which runs on any port, can be run only once. 
```

Enter fullscreen mode Exit fullscreen mode

用户定义的网络

```
o   User can define the network like 182.18.0.X
o   Docker network create –driver bridge –subnet 182.18.0.0./16 custom-isolated-network 
```

Enter fullscreen mode Exit fullscreen mode

# CMD vs ENTRYPOINT:

CMD 是 docker 调用 run 时追加的 once(就像 docker run Ubuntu sleep 10)
。entry point 允许传递参数，所以如果您有 ENTRYPOINT=["sleep"] 并调用 docker 以运行和传递 10，则这 10 将是 sleep
的参数。如上所述，当 docker 被调用以运行时，CMD 被追加，因此 CMD 可用于提供默认值，这意味着如果 DOCKERFILE 具有 ENTRYPOINT ["sleep"]和 CMD ["5"]，则如果没有传递参数，则 sleep 5 将被执行，如果传递了任何参数，则该特定值将覆盖 5。
您也可以使用“docker run–ENTRYPOINT sleep 2”覆盖 entry point

# YAML

像 XML 和 JSON 一样，YAML 也是数据表示。里面有字典，除了地图什么都没有。
字典可以包含另一个字典。
可以有一个字典列表，用'-'表示。
:在字典中，:'后面应该有一个空格。
按键前的空格起着重要的作用。
·任何多余的空间都可能导致编译失败或 hirearchy 发生变化。
“字典无序”意味着值与顺序无关。
香蕉:
热量:105
脂肪:0.4 千克
果肉:27 克
香蕉:
热量:105
脂肪:0.4 千克
果肉:27 克
表示两种香蕉相同。
列表为订购项目:
水果:

*   柑橘
*   苹果
*   香蕉水果:
*   柑橘
*   香蕉
*   这两种水果是不同的。yaml 文件中以' # '开头的任何一行都被视为注释。