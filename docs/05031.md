# 加速开发的有用的 Docker shell 别名

> 原文：<https://dev.to/kovah/useful-docker-shell-aliases-to-speed-up-develipment-3ck5>

我一直和多克一起工作。抛弃我的 MAMP 堆栈，选择 Docker 是我做过的最方便、最有用的决定之一。如果你对 Docker 感兴趣，你应该看看我目前的
[Docker 栈](https://blog.kovah.de/en/5gw1x8-a-drop-in-docker-stack-for-php-app/)，它描述了我如何使用 Docker 进行
PHP 项目。

## 有用的外壳别名

如果你使用 Docker，你必须发出许多 shell 命令来启动、停止和管理容器。大多数命令通常是针对 Docker Compose 的，Docker Compose 是 Docker 的管理层。为了改进对 shell 的使用，我创建了几个别名来缩短执行最常见操作所需的输入时间。

**集装箱管理**

```
# Start the docker-compose stack in the current directory
alias dcu="docker-compose up -d"

# Start the docker-compose stack in the current directory and rebuild the images
alias dcub="docker-compose up -d --build"

# Stop, delete (down) or restart the docker-compose stack in the current directory
alias dcs="docker-compose stop"
alias dcd="docker-compose down"
alias dcr="docker-compose restart"

# Show the logs for the docker-compose stack in the current directory
# May be extended with the service name to get service-specific logs, like
# 'dcl php' to get the logs of the php container
alias dcl="docker-compose logs"

# Quickly run the docker exec command like this: 'dex container-name bash'
alias dex="docker exec -it" 
```

Enter fullscreen mode Exit fullscreen mode

**全系统管理**

```
# 'docker ps' displays the currently running containers
alias dps="docker ps"

# This command is a neat shell pipeline to stop all running containers no matter
# where you are and without knowing any container names
alias dsa="docker ps -q | awk '{print $1}' | xargs -o docker stop" 
```

Enter fullscreen mode Exit fullscreen mode

这些是我用来加速开发的当前别名。随意扩展它们，请通过 Twitter 与我分享。

* * *

*这篇文章最初发表在[博客上。](https://blog.kovah.de/en/2019/useful-docker-shell-aliases/)kovah . de*