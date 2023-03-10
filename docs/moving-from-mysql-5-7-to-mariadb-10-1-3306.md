# 从 MySQL 5.7 迁移到 MariaDB 10.1

> 原文：<https://dev.to/mcloide/moving-from-mysql-5-7-to-mariadb-10-1-3306>

### 警告

```
Please perform these tests first on a testing environment or a virtual machine.

```

我从来没有真正需要关注 MariaDB，直到它成为一个可能和可行的选择。技术上的决定是显而易见的，转移到 MySQL 的新版本，继续使用它，未知的 Oracle 是所有者，或者转移到 Maria db——MySQL 的知名社区替代品。

经过大约一个小时的阅读，我决定做所谓的从 MySQL 到 MariaDB 的神奇之举，这就是我所学到的。

接下来的步骤都是在安装了 MySQL 5.7.13 版本的 CentOS 7 上执行的，并使用以下文章作为参考:[https://mariadb.com/kb/en/mariadb/yum/](https://mariadb.com/kb/en/mariadb/yum/)

要执行的第一步是显而易见的:

*   所有数据库的 mysqldump
*   /var/lib/mysql 的备份
*   /etc/my.cnf 的备份

下一步相当令人讨厌，因为它需要的不仅仅是简单的 yum remove。

```
    sudo yum remove mysql mysql-server mysql-common

```

默认情况下，这应该会删除 MySQL 中的所有引用，但是，因为如果仍然有引用，MariaDB 将不会通过安装检查，所以运行以下命令:

```
    sudo yum list installed | grep -i mysql

```

它会给你一个这样的列表:

```
    mysql-community-client.x86_64 5.7.13–1.el7 @mysql57-community
    mysql-community-common.x86_64 5.7.13–1.el7 @mysql57-community
    mysql-community-libs.x86_64 5.7.13–1.el7 @mysql57-community
    mysql-community-libs-compat.x86_64 5.7.13–1.el7 @mysql57-community
    mysql-community-server.x86_64 5.7.13–1.el7 @mysql57-community
    mysql57-community-release.noarch el7–7 @/mysql57-community-release-el7–7.noarch

```

除了像 PHP-MySQL 这样的库之外，其余的都必须删除。这可以通过简单地运行:

```
    mysql-community-client.x86_64 5.7.13–1.el7

```

如果你读过任何一篇介绍如何从 MySQL 迁移到 MariaDB，直到 MySQL 5.6 和 MariaDB 10.0 的文章，你需要做的唯一一件事就是创建 yum repo 并安装 MariaDB，但是这个选项并不太好用。

以下步骤是我为 MySQL 5.7 迁移正确安装 MariaDB 10.1 所执行的每一个步骤。

### 步骤 1 —创建百胜回购

```
    sudo vi /etc/yum.repos.d/MariaDB.repo

    [mariadb]
    name = MariaDB
    baseurl = http://yum.mariadb.org/10.1/centos7-amd64
    gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
    gpgcheck=1

```

### 步骤 2 —安装 rpm 键

```
    sudo rpm — import https://yum.mariadb.org/RPM-GPG-KEY-MariaDB

```

### 第三步——完全删除 MySQL 数据

是的，你没看错，删除/var/lib/mysql，这是唯一正确的方法。如果你在开始的时候还没有备份，那么，停下来，回到 MySQL。

```
    sudo rm -rf /var/lib/mysql

```

用那个文件夹安装 MariaDB 10.1，你会在 InnoDB 上发现一个明显的错误，不管你搜索多少次，你都不会找到一个好的解决方案。以下是它的完整的错误副本:

```
    [vagrant@localmachine ~]$ sudo journalctl -xe
    — Unit mariadb.service has failed.
    —
    — The result is failed.
    Jun 27 17:46:46 vagrant systemd[1]: Unit mariadb.service entered failed state.
    Jun 27 17:46:46 vagrant systemd[1]: mariadb.service failed.
    Jun 27 17:46:46 vagrant polkitd[656]: Unregistered Authentication Agent for unix-process:12731:154478 (system bus name :1.31, object path /org/freedesktop/PolicyKit1/AuthenticationAgent,
    Jun 27 17:47:25 vagrant sudo[12754]: vagrant : TTY=pts/0 ; PWD=/home/vagrant ; USER=root ; COMMAND=/bin/systemctl start mariadb.service
    Jun 27 17:47:25 vagrant polkitd[656]: Registered Authentication Agent for unix-process:12755:158423 (system bus name :1.33 [/usr/bin/pkttyagent — notify-fd 5 — fallback], object path /org
    Jun 27 17:47:25 vagrant systemd[1]: Starting MariaDB database server…
    — Subject: Unit mariadb.service has begun start-up
    — Defined-By: systemd
    — Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
    —
    — Unit mariadb.service has begun starting up.
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] /usr/sbin/mysqld (mysqld 10.1.14-MariaDB) starting as process 12760 …
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Using mutexes to ref count buffer pool pages
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: The InnoDB memory heap is disabled
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Mutexes and rw_locks use GCC atomic builtins
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Memory barrier is not used
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Compressed tables use zlib 1.2.7
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Using Linux native AIO
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Using SSE crc32 instructions
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Initializing buffer pool, size = 128.0M
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Completed initialization of buffer pool
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] InnoDB: Highest supported file format is Barracuda.
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: No valid checkpoint found.
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: If you are attempting downgrade from MySQL 5.7.9 or later,
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: please refer to http://dev.mysql.com/doc/refman/5.6/en/upgrading-downgrading.html
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: If this error appears when you are creating an InnoDB database,
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: the problem may be that during an earlier attempt you managed
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: to create the InnoDB data files, but log file creation failed.
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: If that is the case, please refer to
    Jun 27 17:47:26 vagrant mysqld[12760]: InnoDB: http://dev.mysql.com/doc/refman/5.6/en/error-creating-innodb.html
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [ERROR] Plugin ‘InnoDB’ init function returned error.
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [ERROR] Plugin ‘InnoDB’ registration as a STORAGE ENGINE failed.
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [Note] Plugin ‘FEEDBACK’ is disabled.
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [ERROR] Could not open mysql.plugin table. Some plugins may be not loaded
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [ERROR] Unknown/unsupported storage engine: InnoDB
    Jun 27 17:47:26 vagrant mysqld[12760]: 2016–06–27 17:47:26 140668548266112 [ERROR] Aborting
    Jun 27 17:47:26 vagrant systemd[1]: mariadb.service: main process exited, code=exited, status=1/FAILURE
    Jun 27 17:47:26 vagrant systemd[1]: Failed to start MariaDB database server.
    — Subject: Unit mariadb.service has failed
    — Defined-By: systemd
    — Support: http://lists.freedesktop.org/mailman/listinfo/systemd-devel
    —
    — Unit mariadb.service has failed.
    —
    — The result is failed.
    Jun 27 17:47:26 vagrant systemd[1]: Unit mariadb.service entered failed state.
    Jun 27 17:47:26 vagrant systemd[1]: mariadb.service failed.
    Jun 27 17:47:26 vagrant polkitd[656]: Unregistered Authentication Agent for unix-process:12755:158423 (system bus name :1.33, object path /org/freedesktop/PolicyKit1/AuthenticationAgent,
    Jun 27 17:47:40 vagrant sudo[12775]: vagrant : TTY=pts/0 ; PWD=/home/vagrant ; USER=root ; COMMAND=/bin/systemctl status mariadb.service
    Jun 27 17:48:10 vagrant sudo[12777]: vagrant : TTY=pts/0 ; PWD=/home/vagrant ; USER=root ; COMMAND=/bin/journalctl -xe
    lines 2790–2842/2842 (END)

```

### 步骤 4 —安装 Maria DB

现在该文件夹已经删除，并且您已经获得了所有的 SQL 转储，安装 MariaDB

```
    sudo yum install MariaDB-server MariaDB-client

```

### 最后一步—启动服务并恢复数据库

现在剩下的工作就是恢复服务并安装 SQL 转储。

```
    sudo systemctl start mariadb

```

这不是一个令人愉快的解决方案，但它是一个解决方案，直到他们像对 MariaDB 10.0 那样进行神奇的迁移

我做了几个小时的搜索和测试，直到我得出结论，至少在目前，没有直接简单的从 MySQL 5.7 到 MariaDB 10.1 的神奇迁移路径，但有一条路径。在此期间，我发现他们的 IRC 频道非常有用，你不会在那里找到像 MySQL 那样多的答案，让事情变得更困难的是，当搜索一些东西来帮助解决问题时，会有很多 MySQL 的答案。

希望这对我有同样的帮助。

#### 备注:

昨天做所有这些测试时，我错过了这个过程中的一个重要部分:[https://dev.mysql.com/doc/refman/5.5/en/upgrading.html](https://dev.mysql.com/doc/refman/5.5/en/upgrading.html)

我将再次进行测试，但是，根据我对 MariaDB 10.0 进行的另一次测试的结果，我认为在该过程和运行之前的那些步骤:

```
    mysql_upgrade -u  -p

```

在完全安装之后，应该可以修复在让魔术完全发挥作用之前描述的所有问题。

这篇文章最初发表在:[https://bit.ly/31SUCmu](https://bit.ly/31SUCmu)