# 带有系统的自动启动 docker 容器

> 原文：<https://dev.to/suntong/autostart-docker-container-with-systemd-5aod>

在 Docker 容器设置并运行后，您可能需要能够在系统重启或崩溃时自动启动其中一些容器。这篇文章涵盖了这两种情况。

我找到的最好的文章是

**自动启动 Docker 容器**

[https://mehmandarov . com/Start-Docker-Containers-Automatically/](https://mehmandarov.com/start-docker-containers-automatically/)

我会在这里转贴。我会说我以后应该多转贴，因为从现在开始，我在以前的帖子中引用但没有转贴到这里的下面这篇文章，再也无法访问了:

**使用 Squid 和 Docker-Compose 设置 Web 代理**

[https://hydra sky . com/Linux/administration/Setting-Up-we B- Proxy-Using-Squid-And-Docker-Compose/](https://hydrasky.com/linux/administration/setting-up-web-proxy-using-squid-and-docker-compose/)

它可能只是一个暂时的东西，但是，当它不可用的时候说到需要它...不管怎样，

要在 docker 容器自身崩溃时重启它，请使用 Docker 提供的[重启策略](https://docs.docker.com/engine/admin/start-containers-automatically/)。可以设置它们来控制容器是在退出时自动启动，还是在 Docker 重启时自动启动。

`$ docker run -dit --restart always my-docker-image`

NB，IMHO，被 google 首发的内容，名为《如何自动启动 Docker 容器》来自 codeburst，是...errr...垃圾——原谅我找不到一个更礼貌的词来形容它。它的命令行甚至没有正确的语法，它不假思索地从 doc 中复制了 **`unless-stopped`** 的`--restart`策略。

现在，关于用`systemd`在系统重启时自动启动 docker 容器，我将照原样复制[start-docker-containers-automatically](https://mehmandarov.com/start-docker-containers-automatically/)文章:

### 创建服务文件

要创建一个将由`systemd` ( `systemctl`命令)使用的服务文件，我们首先需要获得您的容器名。这可以通过在 shell 中运行以下命令来完成:

`$ docker ps -a`

输出看起来会像这样。从列表中选择正确的容器，并在最后一列中记下其名称。在这个例子中，我们将使用`mywiki`容器。

```
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS                    PORTS                NAMES
573193cf1d5e        hypriot/rpi-busybox-httpd   "/bin/busybox http..."   2 days ago          Exited (0) 5 hours ago                         mytest
e85753d57a67        easypi/dokuwiki-arm         "/bin/sh -c 'php-f..."   1 days ago          Up 23 hours               0.0.0.0:80->80/tcp   mywiki
Now, we will need to create a file (choose an appropriate file name for the service): 
```

Enter fullscreen mode Exit fullscreen mode

`$ sudo nano /etc/systemd/system/docker-dokuwiki.service`

将以下内容粘贴到文件中。设置一个合适的`Description`，并确保更新`ExecStart`和`ExecStop` :
中的容器名称

```
[Unit]
Description=DokuWiki Container
Requires=docker.service
After=docker.service

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a mywiki
ExecStop=/usr/bin/docker stop -t 2 mywiki

[Install]
WantedBy=local.target 
```

Enter fullscreen mode Exit fullscreen mode

关于上面的脚本，请注意以下几点:

1.  这个文件被称为[单元文件](https://www.freedesktop.org/software/systemd/man/systemd.unit.html)的`systemd`。
2.  确保在这些路段内没有任何额外的线闸，如`Unit`或`Service`。
3.  用于`ExecStart`的 Docker 命令中的`-a`选项确保其运行在连接模式，即连接 STDOUT/STDERR 并转发信号。
4.  用于`ExecStop`的 Docker 命令中的`-t`选项指定了在杀死容器之前等待它停止的秒数。

### 激活服务

在激活我们已经创建的服务之前，我们需要重新加载单元文件。您还需要在对单元文件进行任何修改时运行该命令:

`$ sudo systemctl daemon-reload`

要激活服务，请运行以下命令(记住要更改服务名称):

`$ sudo systemctl start docker-dokuwiki.service`
T1】

要禁用该服务，请运行以下命令(记住要更改服务名称):

`$ sudo systemctl stop docker-dokuwiki.service`
T1】

更改将在重新启动时生效:

`$ sudo reboot`

现在您应该有一个容器，它将在服务器重启、Docker 重启或崩溃时启动。*恭喜你！*