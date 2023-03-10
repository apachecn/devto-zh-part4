# 使用 hombrew 安装 psql(11.5)，设置 PgAdmin 和 Postgres.app

> 原文：<https://dev.to/krtb/installing-psql-11-5-with-hombrew-setting-up-pgadmin-and-postgres-app-5gio>

# 大意

我一直在检查和清理我的系统，更新我安装在本地机器上的工具、框架和语言。

在这样做的时候，我意识到我从来没有通过包管理器 Homebrew 安装过 postgresql。因为到目前为止，我没有任何关键项目使用我的本地设置，所以我决定经历一次设置正确的痛苦，这样我就不必在将来想要轻松更新或删除任何包时担心它。

## 这个为什么

家酿就像 NPM 或 Rbvm 一样，它是一个软件包管理器，旨在帮助你轻松地更新、删除或添加软件包。

> Homebrew 是一个免费开源的软件包管理系统，它简化了在苹果 macOS 操作系统和 Linux 上安装软件的过程。这个名字意在暗示根据用户的喜好在 Mac 上构建软件的想法。最初由 Max Howell 编写的 package manager 在 Ruby on Rails 社区中广受欢迎，并因其可扩展性而赢得赞誉。[3] Homebrew 因其易用性[4]以及与命令行的集成而受到推荐。[5]家酿是软件自由保护协会的一个非盈利项目成员，完全由无报酬的志愿者管理。[6] -根据[维基百科](https://en.wikipedia.org/wiki/Homebrew_%28package_management_software%29)

## 该如何如何

首先，我需要弄清楚如何删除我的原始安装，这不是通过自制。

让我们备份一下

<figure>

[![](img/075d0606c4f07c1d540f44a6a38529ed.png)](https://i.giphy.com/media/3oFzlVGQcWiBp9Kcve/giphy.gif)

<figcaption>NSFPW: not safe for puppy work</figcaption>

</figure>

```
 🜲  pg_dump -d informo-backend_development -n public -f informee-backend.sql 
```

现在您有了一个单一数据库的备份。当我们需要将它导入回来时，我们可以稍后再考虑这个问题。但是现在，我们可以庆祝我们做对了一件事，万岁！

接下来，从机器上卸载 postgresql。我们应该使用命令
检查您安装了哪个版本的 psql

```
psql -I -version 
```

我的是 10.10，所以我运行了命令

```
open /Library/PostgreSQL/10.10/uninstall-postgresql.app 
```

但我得到一个错误，说我没有安装在那里。我走了一条捷径，使用了我的 [AppCleaner](https://freemacsoft.net/appcleaner/) 应用程序，在拖放我的应用程序后，它定位了所有文件所在的位置。顺便提一下，在深入了解是否有更好的选择后，我最终获得了 [CleanMyMac X](https://macpaw.com/) 的年度订阅。

安装前务必运行`brew update`，然后运行

```
brew install postgresql 
```

之后，您可以运行`which psql`来检查安装的版本。

## 问题日志

这是我遇到问题的地方

1)添加[回归应用](https://postgresapp.com/)

当我通过 homebrew 安装了最新的 psql 版本后下载应用程序时，我一直收到一个错误消息`Binaries not found`。

我认为这是像百万件事情，我删除了错误的核心文件，由于自制软件安装在错误的地方，等等。我想知道，如果我只是默认使用 noob 那样的安装程序，是否有可能在自制软件安装程序旁边运行应用程序。

在通过 stackoverflow 之类的工具进行了大量搜索之后，我发现这个
[是从 9.5 之前的版本升级而来的，指令不清楚#399](https://github.com/PostgresApp/PostgresApp/issues/399)

抛开这些抱怨，我意识到我必须点击侧边的标签并创建一个新的服务器。就是这样！很明显，它附带的默认服务器指向 psql -v 10，而我需要 11，这就创建了一个全新的服务器。

<figure>

[![ded](img/53153997b4c3933f2a542ee0795faafe.png)](https://i.giphy.com/media/NQRRqqkImJ3Da/giphy.gif)

<figcaption>ahhhh</figcaption>

</figure>

2)设置 [PgAdmin](https://www.pgadmin.org/)

问题的核心是，在安装后，打开一个新窗口，输入我的默认密码，我继续尝试添加我的服务器。谢天谢地，这篇 stackoverflow 帖子帮助了我 [pgAdmin 和 PostgreSQL:无法连接到服务器](https://stackoverflow.com/questions/16889400/pgadmin-and-postgresql-cant-connect-to-server)

但是每次我输入我的主机名和服务器名，我都会得到一个`connection refused`错误。

好的，让我们从刚开始工作的 Postgress 应用程序中打开数据库。

点击一个数据库，终端窗口打开。你在母体里。

现在我们键入

```
SHOW port;

 port 
------
 my_port_number_here
(1 row) 
```

太酷了。主机名怎么样？

```
SHOW listen_addresses;

 listen_addresses 
------------------
 localhost
(1 row) 
```

哦，我用的是我的机器名，而不是`localhost`...

<figure>

[![ded](img/f15afb03e89e744d10d2498e383789c4.png)](https://i.giphy.com/media/eBCnpuRGBhQGY/giphy.gif)

<figcaption>Don't mind me, just going around in circles with myself</figcaption>

</figure>

一旦我添加了正确的信息，数据库门打开了，我的数据库显示在页面上。

下面我添加了另外两个有助于设置的链接，[remove _ postgres _ on _ MAC _ OS . MD](https://gist.github.com/Atlas7/b1a40a2ffd85728b33e7)和[“如何在 macos 上启动 postgresql 服务器”](https://dataschool.com/learn-sql/how-to-start-a-postgresql-server-on-mac-os-x/)

照片由 [大卫·克洛德](https://unsplash.com/@davidclode?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from David Clode") 在 Unsplash 上拍摄