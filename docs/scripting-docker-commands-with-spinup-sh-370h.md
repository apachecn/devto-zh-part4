# 使用 Spinup.sh 编写 Docker 命令脚本

> 原文：<https://dev.to/quinncuatro/scripting-docker-commands-with-spinup-sh-370h>

在第三天，我加入了我的 DevOps-y 朋友们关于容器之上抽象的自然进展的简介:

> 您通常从 docker run CLI 命令开始，并根据需要逐步升级到具有更多抽象层的工具。接下来是 Docker-compose，接着是用 Bash 脚本自动化几个命令，最后是 Kubernetes。

我还与您分享了一种在 Bash 脚本中处理开关的更好的方法。今天，我将向您展示我是如何从运行自己的 Docker 命令转变为运行一个带有少量标志的 shell 脚本的。

* * *

在学习如何使用 Docker 以及如何在项目中使用它的最初几个月，我每天都要运行大量的 Docker CLI 命令。然后我开始让 Docker Compose 帮我管理东西。然而，对于我正在处理的一些边缘案例来说，即使这样也开始成为一种负担。

回想我与 DevOps 领域的其他朋友的对话，我记得他们都告诉我，您将会遇到想要自动化您的工作流的某个点...所以我开始钻研 Bash 脚本。

我知道我会希望我的脚本做一些不同的事情:

*   启动我所有的容器基础设施
    *   一个版本，每个基础设施(开发)都有明确的命令
    *   一个版本在 docker-compose 下运行(生产)
*   拆除我的所有容器基础设施(拆除)

我做的第一件事是确保我的`docker-compose.yaml`文件已经建好。这将是我的真实来源——如果运行`docker-compose up -d`使一切正常工作，那么这个脚本的其余部分将基于该文件中所写的内容。

```
version: "3.7"
services:
  app:
    container_name: hquinn-app
    image: "hquinn_app:latest"
    networks:
      - hquinn-net
    ports:
      - "8080:8080"
    restart: always
    volumes:
      - type: volume
        source: hquinn_app_home
        target: /var/www/html

networks:
  hquinn-net:

volumes:
  hquinn_app_home: 
```

Enter fullscreen mode Exit fullscreen mode

> 我觉得我应该指出这只是一个示例项目。不是真实的图像、容器或项目。以此为模板插入您自己的信息。是很好的训练锻炼！

运行`docker-compose up -d`似乎适用于这种 yaml 配置。很好！现在我们可以继续创建 Bash 脚本了。我们将称之为`spinup.sh`。

让我们从设置昨天学到的改进的 switch 语句开始。我们将包括四个标志(帮助、开发、生产、拆卸)以及一个错误捕获器。

```
#!/bin/bash

while getopts ":hdpt" opt; do
  case ${opt} in h )
      printf "USAGE: ./spinup.sh [OPTION]... \n\n" 
      printf "-h for HELP, -d for DEV, -p for PROD, or -t for TEARDOWN \n\n"  
      exit 1
      ;;
    d )
      exit 1
      ;;
    p )
      exit 1
      ;;
    t )
      exit 1
      ;;
    \? )
      echo "Invalid option: %s" "$OPTARG" 1>&2
      exit 1
      ;;
  esac
done shift $((OPTIND -1))

printf "USAGE: ./spinup.sh [OPTION]... \n\n" 
printf "-h for HELP, -d for DEV, -p for PROD, or -t for TEARDOWN \n\n" 
exit 1
;; 
```

Enter fullscreen mode Exit fullscreen mode

坚实。这个开关将使插入我们想要为每个标志运行的命令变得非常容易。

生产可能是最容易的，因为我们将依赖于已经构建好的`docker-compose.yaml`文件。让我们将这些命令填入`p )`案例:

```
p )
  # Rebuild image
  docker-compose build

  # Spin up container
  docker-compose up -d

  exit 1
  ;; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们实际上只是让这个 bash 脚本运行我们自己运行的相同命令来启动我们的容器、卷和网络。我们只是将不同的作业分成不同的标志，这样我们就可以利用相同的脚本来完成许多不同的任务。

我们的 dev 案例不会有太大的不同。我们只是手动创建一个网络并运行一个长的`docker run`命令来启动我们的容器:

```
d )
  # Rebuild image hquinn_app
  docker-compose build --no-cache

  # Create hquinn-net bridge network for container(s) to communicate
  docker network create --driver bridge hquinn-net

  # Spin up hquinn-app container
  docker run -d --name hquinn-app --restart always -p 8080:80 -v hquinn_app_home:/var/www/html --network hquinn-net hquinn_app:latest

  exit 1
  ;; 
```

Enter fullscreen mode Exit fullscreen mode

> Henry，你的 dev 和 prod 版本之间的实际区别是什么？

伟大的问题，读者！这就是乐趣的一部分(痛苦？)开始学习容器。有许多不同的方法来处理相同的任务，您可以在进行过程中学习最佳实践。

当我最初编写这个脚本时，我正在从事 ColdFusion、Informix 和 MySQL 项目。由于它在交给我之前最初构建的方式，我们需要运行不同的命令集来旋转它，这取决于我们是在本地运行它进行开发，还是在生产中运行它供法官实际使用。

随着我对 Docker 的深入研究，我从各种渠道了解到了一些显而易见的事情:

> 容器的一个主要特点是你的代码在任何地方都应该运行一致。同样的容器，只是运行在不同的引擎上。

也就是说，我应该在任何地方运行相同的命令来运行相同的容器。虽然我没有，但我仍然被整件事所困扰。

从那以后，我对这个脚本进行了一些删减。不过，我仍然喜欢在我的`d )`案例中使用较长的命令。它允许我快速测试一些东西，我可以建立我的基础设施，然后我可以在我的`docker-compose.yaml`文件中固化，然后我可以在生产环境中运行。这是容器的另一个租户，我们可以将我们的基础设施视为代码。一旦我们的`docker-compose.yaml`被调整到我们喜欢的程度，我们就可以将它签入版本控制，并且知道它永远是安全的。

现在,`t )`案意味着摧毁我们所有的基础设施。终止容器，并删除容器、映像、卷和网络。这样，我们可以从头开始，测试我们对基础架构所做的新更改。

我们将使用多个`if/then`模块来完成这个任务:

```
# If hquinn-app container is running, turn it off.
running_app_container=`docker ps | grep hquinn-app | wc -l`
if [ $running_app_container -gt "0" ]
then docker kill hquinn-app
fi 
```

Enter fullscreen mode Exit fullscreen mode

对于这个特定的块，我们将名为`running_app_container`的变量设置为`docker ps | grep hquinn-app | wc -l`的输出。这意味着如果容器`hquinn-app`启动并运行，`running_app_container`被设置为该命令返回的行数。

然后`if/then`块检查以确保控制变量大于 0。如果为真，它运行命令`docker kill hquinn-app`终止容器。

我们将使用一系列这些块来管理我们的容器、映像、卷和网络。

让我们看看完整的`spinup.sh`脚本，所有的部分都插入了:

```
#!/bin/bash

while getopts ":hdpt" opt; do
  case ${opt} in h )
      printf "USAGE: ./spinup.sh [OPTION]... \n\n" 
      printf "-h for HELP, -d for DEV, -p for PROD, or -t for TEARDOWN \n\n"  
      exit 1
      ;;
    d )
      # Rebuild image hquinn_app
      docker-compose build --no-cache

      # Create hquinn-net bridge network for container(s) to communicate
      docker network create --driver bridge hquinn-net

      # Spin up hquinn-app container
      docker run -d --name hquinn-app --restart always -p 8080:80 -v hquinn_app_home:/var/www/html --network hquinn-net hquinn_app:latest

      exit 1
      ;;
    p )
      # Rebuild image
      docker-compose build

      # Spin up container
      docker-compose up -d

      exit 1
      ;;
    t )
      # If hquinn-app container is running, turn it off.
      running_app_container=`docker ps | grep hquinn-app | wc -l`
      if [ $running_app_container -gt "0" ]
      then docker kill hquinn-app
      fi

      # If turned off hquinn-app container exists, remove it.
      existing_app_container=`docker ps -a | grep hquinn-app | grep Exit | wc -l`
      if [ $existing_app_container -gt "0" ]
      then docker rm hquinn-app
      fi

      # If image for hquinn_app exists, remove it.
      existing_app_image=`docker images | grep hquinn_app | wc -l`
      if [ $existing_app_image -gt "0" ]
      then docker rmi hquinn_app
      fi

      # If hquinn_app_home volume exists, remove it.
      existing_app_volume=`docker volume ls | grep hquinn_app_home | wc -l`
      if [ $existing_app_volume -gt "0" ]
      then docker volume rm hquinn_app_home
      fi

      # If hquinn-net network exists, remove it.
      existing_hquinnnet_network=`docker network ls | grep hquinn-net | wc -l`
      if [ $existing_hquinnnet_network -gt "0" ]
      then docker network rm hquinn-net
      fi exit 1
      ;;
    \? )
      printf "Invalid option: %s" "$OPTARG" 1>&2
      exit 1
      ;;
  esac
done shift $((OPTIND -1))

printf "USAGE: ./spinup.sh [OPTION]... \n\n" 
printf "-h for HELP, -d for DEV, -p for PROD, or -t for TEARDOWN \n\n" 
exit 1
;; 
```

Enter fullscreen mode Exit fullscreen mode

总之，这看起来很紧张。如果你需要更复杂的东西，你可以添加更多的命令。您也可以添加更多的标志来处理更多的边缘情况。

这个剧本(以及其他一些类似的剧本)真的帮助我度过了我在法院工作的最后六个月。然而，对于我现在正在做的项目，我需要保持生产力的这些脚本的数量将是一个负担。我们需要更多的力量和对我们所做事情的更多控制。

因此，我深入到库伯内特斯。

我没有忘记这件事。我开始钻研我买的书。就 Kubernetes 的艰难历程而言， [Christian Corbin](https://dev.to/christiancorbin) 指出该教程可能已经过时了。为此，我想我会放弃 K8s，当我需要一些实际操作时，只关注我买的书和 [Kubernetes.io](https://kubernetes.io/docs/tutorials/) 。

DevOps 是关于迭代和改进过程的。当更好的机会出现时，我很乐意改变这里的一切！

* * *

这是一个假日周末，我要去缅因州。时间到`spinDOWN.sh`。

/rimshot

我会在休假期间尽量多写一些，尽管你可能要到下周才能收到我的来信。

保持冷静。

[https://Henry needs . coffee](https://henryneeds.coffee)
[博客](https://henryneeds.coffee/blog)
[LinkedIn](https://linkedin.com/in/henryquinniv)
[Twitter](https://twitter.com/quinncuatro)