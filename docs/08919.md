# 使用 docker-sync 将 Mac 响应时间缩短一半

> 原文：<https://dev.to/kovah/cut-your-docker-for-mac-response-times-in-half-with-docker-sync-1e8j>

我真的很喜欢码头和集装箱化的概念。我已经一年多没碰过我的 MAMP 开发设置了，几乎不用我的本地 PHP 命令行界面。但是有一个问题，而且一直都有:性能。Laravel 应用程序的响应时间为 1 秒，大型 Wordpress 堆栈的响应时间为 3-7 秒是很常见的。谢天谢地，这个问题有一个解决方案，不需要改变你的整个技术栈:docker-sync。

## 什么是 docker-sync？

Docker for Mac(和 for Windows)性能问题的根源在于 Docker 和 OS 之间的 OS 文件系统层。在 Linux 上，Docker 可以直接从文件系统挂载文件和文件夹，而在 Mac 上，Docker 必须将请求传递给操作系统，操作系统负责将文件写入磁盘。在 macOS 的情况下，OSXFS 是房间里的大象。虽然这听起来没什么大不了的，但确实如此。如果你有 500 个需要读取的源文件，那么即使是一毫秒的差异也能让你的整个应用程序延迟半秒。

幸运的是，如果您将文件放入卷中，Docker 能够使用本机挂载，然后由 Linux 内核处理。这正是 docker-sync 所做的。它会创建一个包含所有应用程序源文件的卷，并将其提供给应用程序，应用程序可以非常快速地读写该卷。由于卷通常不与外界(即您的文件系统)绑定，docker-sync 实现了不同的工具，这些工具负责将容器内部与您的主机文件系统同步。这允许您在编辑器中编辑任何文件，它们会被同步到宗卷中，并且您的应用程序可以访问它。

## docker-sync 基准测试

我对 docker-sync 进行了基准测试，因为我想看看硬数字，而不是猜测请求是否更快。以下两个应用基于 Laravel 和 Wordpress，而 Bitnami 的[图片用于运行这两个应用。这里使用的主要度量是 TTFB，或第一个字节的*时间*，以毫秒为单位。我连续 10 次打开这两个应用程序的页面，没有特定的页面缓存。这两个应用程序都使用 PHP 7.3 运行。](https://blog.kovah.de/en/5gw1x8-a-drop-in-docker-stack-for-php-app/)

| 应用 | 坞站同步 | 福建话 | 最大 | 中位数 | 差异 |
| --- | --- | --- | --- | --- | --- |
| Laravel 5.8 | 离开 | 1035 毫秒 | 2158 毫秒 | 1386 毫秒 |  |
|  | 在 | 234 毫秒 | 303 毫秒 | 251 毫秒 | **-81%** |
| Wordpress 5.2 | 离开 | 2467 毫秒 | 3942 毫秒 | 2722 毫秒 |  |
|  | 在 | 881 毫秒 | 1589 毫秒 | 1271 毫秒 | **-53%** |

## 如何使用 docker-sync

我很确定你已经上瘾了，是吧？节省高达 80%的响应时间听起来很荒谬，但是...是真的。那么，我们现在如何设置 docker-sync 呢？

### 1。安装工具

docker-sync 是用 Ruby 编写的，所以你可以很容易地安装它，即使你没有升级你的 Ruby 版本(v2.3 目前是 macOS 附带的)。要安装它，运行以下命令:

```
gem install --user-install docker-sync
# or globally via
sudo gem install docker-sync 
```

Enter fullscreen mode Exit fullscreen mode

### 2。向项目中添加一个新的 docker-sync.yml 文件

这个配置文件告诉 docker-sync 存储和同步哪些文件，使用哪些模式，等等。这里有一个基本的配置文件，你可以直接放入你的根文件夹:

```
version: "2"
syncs:
  your-app-files:
    notify_terminal: true
    src: './'
    sync_excludes: ['.git', '.idea', 'node_modules'] 
```

Enter fullscreen mode Exit fullscreen mode

首先，在`syncs`选项下，指定要创建的卷。请注意，卷的名称(此处为`your-app-files`)在您的整个机器上必须是唯一的。`src`选项定义了默认情况下应该将哪些文件复制到卷中，您可以使用`sync_excludes`选项排除特定的目录或文件。我在这里排除了 Git、PhpStorm 和 node_modules 文件夹。git 和 IDE 文件夹都与应用程序本身无关，所以容器中不需要它们。Node_modules 被排除在外，因为常规 PHP 应用程序也不需要它们，因此可以删除它们，尤其是因为在同步 4596895 个文件和文件夹之前可能需要很长时间...

您可以在官方文档中找到卷的其他配置参数。

### 3。添加坞站-合成-dev.yml 文件

使用当前版本的 Docker Compose，您可以覆盖以前 docker-compose.yml 文件的某些配置。这非常方便，因为我们可以原样使用当前的 docker-compose.yml 文件，无需任何修改。如果你觉得有必要不使用 docker-sync，你可以用常规的`docker-compose up`命令启动你的应用程序。

下面是我使用的示例文件:

```
version: "2"
services:

  php:
    volumes:
      - your-app-files:/app:nocopy

  nginx:
    volumes:
      - your-app-files:/app:nocopy

volumes:
  your-app-files:
    external: true 
```

Enter fullscreen mode Exit fullscreen mode

这个文件告诉 Docker Compose 不要使用 docker-compose.yml 文件中的常规挂载，而是将`your-app-files`卷挂载到容器中。注意，您必须将卷添加到所有需要访问文件的容器中，在本例中是 PHP 和 nginx。基本就是这样。

### 4。启动 docker-sync 堆栈

使用 docker-sync 启动堆栈实际上有两种方式:

1.  通过使用`docker-sync-stack`或
2.  通过使用`docker-sync`和`docker-compose`。

#### 坞站同步堆栈

```
docker-sync-stack start 
```

Enter fullscreen mode Exit fullscreen mode

该命令将首先启动 docker-sync，它创建卷并准备好供使用，然后通过调用
`docker-compose -f docker-compose.yml -f docker-compose-dev.yml up`运行容器，这将使用两个 docker-compose 配置文件启动 Docker Compose。

这里的缺点是，sync 和 Docker 栈都将在前台运行。我个人不喜欢这样，因为你经常需要为一个应用程序打开多个终端窗口，但这很快也很高效。

要停止堆叠，按下`CMD` + `C`。

#### 坞站同步+坞站-复合

```
docker-sync start
# wait until the command finishes creating the volume, then:
docker-compose -f docker-compose.yml -f docker-compose-dev.yml up -d 
```

Enter fullscreen mode Exit fullscreen mode

这是我目前使用的方法，以后我可能会为此编写一个 bash 函数或别名。你可以继续在码头工作，我推荐 [Kitematic](https://kitematic.com/) 用于集装箱的快速日志查看。(PS: Kitematic 内置于 Docker for Mac 安装包中。)

### 如何解决权限问题

如果你像我一样遇到权限问题，Laravel 不能写日志，Wordpress 不能存储上传，你可能需要指定另一个用户 ID。对于我当前使用 Bitnami 容器的 Docker 栈，我遇到了问题，因为 docker-sync be default 使用根用户来存储所有文件，而 PHP FPM 作为`daemon`用户运行。这将导致保存文件时发生冲突。为了防止这种情况，您必须将`sync_userid: '1'`添加到 docker-sync.yml 配置文件中，其中`1`是容器内守护进程用户的用户 ID。

添加了那行代码后，所有问题都立即消失了，我可以进行设置了。

## 结论

让整个堆栈运行起来需要相当长的时间。我发现文档不是那么容易理解，但是我能够通过查看项目维护者自己提供的各种[例子](https://github.com/EugenMayer/docker-sync/tree/master/example)来建立一个干净的堆栈。在花了额外的时间解决了权限问题后，我能够使用 docker-sync 堆栈运行我的应用程序，并获得了巨大的生产力提升。现在页面加载速度如此之快，这个小工具带来的改变令人难以置信。

非常感谢维护者 Eugen Mayer 和所有贡献者发布了这个令人敬畏的工具。

* * *

*这篇文章最初发表在[博客上。](https://blog.kovah.de/en/2019/docker-sync/)kovah . de*