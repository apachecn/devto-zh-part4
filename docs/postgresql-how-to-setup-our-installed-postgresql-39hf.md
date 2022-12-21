# 如何设置我们已安装的 PostgreSQL

> 原文：<https://dev.to/miku86/postgresql-how-to-setup-our-installed-postgresql-39hf>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 PostgreSQL](https://dev.to/miku86/postgresql-what-is-it-how-to-install-it-16hk) 。

现在我们想学习如何设置我们安装的 PostgreSQL。

* * *

## 创建新的 PostgreSQL 数据库集群

initdb 必须作为拥有服务器进程的用户运行，因此建议创建一个新用户并使用它登录。

```
# login as user
sudo -iu [username] 
```

Enter fullscreen mode Exit fullscreen mode

initdb 创建一个新的 PostgreSQL 数据库集群(由单个服务器实例管理的数据库集合)。

```
initdb -D /var/lib/postgres/data 
```

Enter fullscreen mode Exit fullscreen mode

如果你得到了`permission denied`，你可以在 [initdb 文档](https://www.postgresql.org/docs/current/app-initdb.html)中找到解决方案

* * *

## 启动 PostgreSQL 服务器

如果你不知道如何启动一个系统服务，[看看这个](https://tableplus.com/blog/2018/10/how-to-start-stop-restart-postgresql-server.html)。

拱门的例子:

```
systemctl start postgresql 
```

Enter fullscreen mode Exit fullscreen mode

如果你遇到一些问题，[阅读这个](https://www.postgresql.org/docs/current/server-start.html)。

* * *

## 创建角色(=数据库用户)

现在我们需要一个能做一些操作的角色。

不要忘记以您之前创建的用户身份登录:

```
# login as user
sudo -iu [username] 
```

Enter fullscreen mode Exit fullscreen mode

创建用户:

```
createuser --interactive 
```

Enter fullscreen mode Exit fullscreen mode

[创建用户文档](https://www.postgresql.org/docs/11/app-createuser.html)

* * *

## 创建数据库

不，我们需要一个数据库，它属于我们的新用户

```
createdb -O [username] [dbname] 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 连接数据库外壳

我们刚刚创建了一个用户和一个数据库。

现在我们可以连接到数据库外壳:

```
psql -d [dbname] 
```

Enter fullscreen mode Exit fullscreen mode

[psql](https://www.postgresql.org/docs/current/app-psql.html) 是 PostgreSQL 的一个基于终端的前端，可以输入查询，发布给 PostgreSQL，查看查询结果。

* * *

## 下一部分

我们将编写第一个命令来获取一些数据。

* * *

## 进一步阅读

[PostgreSQL 主页](https://www.postgresql.org/)
[PostgreSQL 文档](https://www.postgresql.org/docs/current/index.html)
[SQL 语法](https://en.wikipedia.org/wiki/SQL_syntax)

* * *

## 提问

*   你最喜欢的 SQL 数据库是什么？