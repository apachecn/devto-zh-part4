# 在 Docker 容器中使用 SQL Server 执行基本任务

> 原文：<https://dev.to/michaeljolley/using-sql-server-in-docker-containers-for-basic-tasks-dk>

如果你曾经使用过 Windows 系统的机器，你可能经历过 Winpocalypse。你知道……当你需要完全重新安装 Windows 直到重新格式化硬盘的时候。一切都没了。(希望你事先做了备份。)

几个月前，我体验了 Winpocalypse 2019，此后我一直对我在机器上重新安装的内容非常谨慎。在决定是否重新安装时，常见的应用程序都经过了严格的审查。一个大目标:SQL Server。即使是 express & developer editions 也很臃肿，会在我的本地机器上引入攻击媒介。

首先，让我澄清一下，我很早就安装了 SQL Management Studio (SSMS)。我经常用它来访问和监控各种数据库。问题是，我需要一个完整的本地运行的数据库引擎吗？答案是否定的。我总是在解决现实问题时学习得最好，所以让我们在 2019 年解决如何免费安装 SQL 的问题。

## 真实世界用例

我们有一个为我们的一个客户在 Azure 托管的开发 SQL 数据库。偶尔，他们会向我们发送生产数据库的备份，以恢复我们的拷贝，从而获得更新、干净的数据。正常的过程是加载它。BAK 文件到 Azure 存储，并备份恢复到 SQL Azure。但是，此备份包含一个自定义 SQL 登录，这将阻止 SQL Azure 恢复备份。

解决办法？在本地恢复备份，删除登录，创建新的备份，然后再次恢复。但是如何在没有安装 SQL 数据库引擎的情况下恢复备份呢？

## 码头工人，码头工人，码头工人

微软已经为 SQL Server 发布了几个 Docker 镜像，包括一个运行在 Linux 上的镜像。因为我的 Docker for Windows 是为 Linux 容器配置的，这似乎是一个很好的起点。首先，我需要找到一个我可以提取的图像。我的谷歌搜索引擎出现了几个选项，但我决定选择`mcr.microsoft.com/mssql/server:2017-latest`。

### Docker 运行

让我们通过命令让一切运行起来，然后详细说明每个参数的用途。

```
docker run -e 'ACCEPT_EULA=Y' `
-e 'SA_PASSWORD=<YourStrong!Passw0rd>' `
-p 1433:1433 `
-v <C:/HostPath>:/var/opts/mssql/data `
--name sql1 `
-d mcr.microsoft.com/mssql/server:2017-latest 
```

Enter fullscreen mode Exit fullscreen mode

该命令将基于最新的 SQL Server 2017 Linux 容器映像创建并运行 Docker 容器。现在，您可以通过 SSMS 连接到运行在这个容器中的服务器。用 127.0.0.1，1433 加上 SA 和你的密码就行了。您创建的任何数据库将被保存到在`-v`命令中指定的主机路径。

当你使用完容器后，删除它并清理掉你不想要的图片。就像你没有在你的机器上安装 SQL Server 就使用了它，并且在卸载后没有残留的垃圾。

### 获取命令的细节

#### -e '接受 _EULA=Y '

此标志只接受 SQL Server EULA。这里没什么可看的。往前走。

#### -e ' SA _ PASSWORD =<your strong！密码>

当然，您会希望用一个实际的强密码替换`<YourStrong!Passw0rd>`，但是这只是为 sa 登录设置初始密码。

#### ——第 1433 页

格式为:，它将容器端口映射到主机上的特定端口。在我的例子中，我将 1433 (SQL 的默认端口)映射到主机 1433。我可以很容易地将它映射到其他东西(例如`-p 5050:1433`)。这里重要的是，无论主机端使用哪个端口，我都将在 SSMS 使用该端口进行连接。要使用非默认端口，输入 SSMS 的服务器地址作为`127.0.0.1,<port you="" chose="">`。是的，这是个逗号。别问了。:)

#### -v<C:/HostPath>:/var/opts/MSSQL/data

将主机上的路径映射到容器中的路径，格式为:。在这种情况下，在容器中创建的任何数据库都可以作为 mdf/ldf 文件在主机上的该目录中进行访问。
您可以排除这个参数，然后使用`docker cp`将&中的文件复制到容器之外。但是，重要的是要记住，如果您将数据存储在一个容器中并销毁了它，这些数据就会丢失。

#### –名称 sql1

容器的易记名称。如果没有指定，您将必须通过它的 id 来引用容器。

#### -维 mcr.microsoft.com/mssql/server:2017-latest

指定从哪个图像构建容器。

## 打倒 SQL Server，SQL Server 万岁

这很容易吗？既然您可以在容器中启动 SQL Server 实例，并在完成后销毁它，那么您为什么还要在开发机器上手动安装 SQL 引擎呢？不，真的，让我知道一些在容器中运行 SQL 可能不适用的用例。