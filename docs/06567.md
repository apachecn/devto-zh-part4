# 解决 PostgreSQL 中的问题。(调试课程)

> 原文：<https://dev.to/ackers93/solving-issues-in-postgresql-lessons-in-debugging-2ggg>

上周，我高兴地把水泼到了电脑上，电脑很快就关机了。谢天谢地，我把它拿到了苹果商店，他们把它弄干了，最大的损失是屏幕，这意味着我现在不得不永远把它插在显示器上，但情况可能会更糟。回到工作岗位后，我准备开始工作，打开我的终端，试着运行我的 Rails 应用程序，结果出现了这个问题:

```
Started GET "/" for ::1 at 2019-08-21 09:15:43 -0700
could not connect to server: No such file or directory
    Is the server running locally and accepting
    connections on Unix domain socket "/tmp/.s.PGSQL.5432"?
 excluded from capture: DSN not set PG::ConnectionBad (could not connect to server: No such file or directory
    Is the server running locally and accepting
    connections on Unix domain socket "/tmp/.s.PGSQL.5432"?
): 
```

Enter fullscreen mode Exit fullscreen mode

由于 *PG* 是 Postgres 的缩写，我的第一个想法是 PostgreSQL(我的数据库)的工作方式有问题，所以在快速访问堆栈溢出以重新学习 Postgres 命令后，我想我应该尝试一下 ol“‘你有没有尝试过关闭并再次打开它？”于是我重启了 PostgreSQL，像这样，

```
$brew services restart postgresql
Stopping `postgresql`... (might take a while)
==> Successfully stopped `postgresql` (label: homebrew.mxcl.postgresql)
==> Successfully started `postgresql` (label: homebrew.mxcl.postgresql) 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这不起作用，我遇到了同样的错误。我回到堆栈溢出，并尝试了第一个 rwo 页的结果，都无济于事。于是我决定向我的雇主( [@andrewculver](https://dev.to/andrewculver) )寻求帮助，他提醒我以前遇到过的 PostgreSQL 问题，PostgreSQL 创建了一个`postmaster.pid`文件，当计算机经历硬关机时，该文件不会得到处理，这反过来会阻止 PostgreSQL 再次运行，因为它认为它已经在运行了。因此，为了让 PostgreSQL 重新启动并运行，需要删除`postmaster.pid`文件，因此我们执行了以下操作:

> 1.  Run `brew info postgresql`. You should see a reference to `pg_ctl -D >/usr/local/var/postgres start`. *Don't run that* , you just want to make sure the path is `/usr/local/var/postgres`. *Actually* does this:
> 2.  Run `cd /usr/local/var/postgres`
> 3.  Run `ls` and you should see a `postmaster.pid` file.
> 4.  Run `rm ./postmaster.pid`. There should be no problem deleting this file, but if you do, run `sudo rm ./postmaster.pid`.
> 
> 5.  Run `brew services start postgresql`.

虽然这可能对我之前遇到的问题有所帮助，但不幸的是，同样的错误消息出现了，所以是时候做一些更深入的挖掘了。

# 解

首先我们进入`cd /usr/local/var/log/`，一进去，我们就把内容列成这样。

```
$ ls postgres.log    redis.log 
```

Enter fullscreen mode Exit fullscreen mode

Next 希望检查一些最近的日志条目，但不显示所有内容，所以使用`tail`打印出最后 10 行。

```
$ tail ./postgres.log 
  Reason: image not found
dyld: Library not loaded: /usr/local/opt/icu4c/lib/libicui18n.63.dylib
  Referenced from: /usr/local/opt/postgresql/bin/postgres
  Reason: image not found
dyld: Library not loaded: /usr/local/opt/icu4c/lib/libicui18n.63.dylib
  Referenced from: /usr/local/opt/postgresql/bin/postgres
  Reason: image not found
dyld: Library not loaded: /usr/local/opt/icu4c/lib/libicui18n.63.dylib
  Referenced from: /usr/local/opt/postgresql/bin/postgres
  Reason: image not found 
```

Enter fullscreen mode Exit fullscreen mode

*我们终于看到了我们的问题！*post master . PID 不是问题，而是一个名为`icu4c`的 Unicode 和全球化动态库，它影响了我们的 PostgreSQL 流程。所以，我们用自制软件卸载了`postgresql`并重新安装了`icu4c` :

```
$ brew uninstall postgresql
Uninstalling /usr/local/Cellar/postgresql/11.2... (3,186 files, 35.4MB)
$ brew install icu4c
Updating Homebrew...

#I'm not going to past all the output because it goes on forever, but icu4c is reinstalled! 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们安装了 PostgreSQL 并重启了它的进程:

```
$ brew install postgresql
==> Installing dependencies for postgresql: openssl and readline
==> Installing postgresql dependency: openssl
==> Downloading https://homebrew.bintray.com/bottles/openssl-1.0.2s.mojave.bottle.tar.gz
==> Downloading from https://akamai.bintray.com/c4/c4a762d719c2be74ac686f1aafabb32f3c5d5ff3a98935c4925a1ddb9c750ee1?__gda__=exp=1566501434~hmac=b709fcc5ffd01de96a07acdb294be279
######################################################################## 100.0%

#Again, a lot more output then I'm going to post here, but it ends with these two options.

To have launchd start postgresql now and restart at login:
  brew services start postgresql
Or, if you don't want/need a background service you can just run:
  pg_ctl -D /usr/local/var/postgres start 
```

Enter fullscreen mode Exit fullscreen mode

第一个选项听起来像是我们想要的，所以我们就照做了。

原来如此！Andrew 说，多年来他每隔几个月就会遇到这种情况，所以我们想发布一些内容来帮助其他看到相同错误信息的人。即使错误链接的动态库不是您的具体问题，该日志文件的内容也可能会告诉您出了什么问题。如果你需要帮助调试这个问题，上面的步骤没有帮助，只需在下面的评论中发布你的日志文件的输出！事实上，即使你马上理解了错误信息，也请将它粘贴到评论中，这样我们就可以建立一个目录，列出 PostgreSQL 没有为不同的人自动重启的所有不同原因。它可能会帮助其他人！希望这能帮助其他有同样问题的人！如果你对解决这个问题的其他方法有任何意见或建议，我很乐意知道！