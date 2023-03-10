# 创建 MySQL 数据目录的 ZFS 映像

> 原文：<https://dev.to/rgeraldporter/creating-a-zfs-image-of-a-mysql-data-directory-dad>

***备注**:我在 2013 年左右写了这篇文档，作为 [Weever Apps](https://weeverapps.com/) 的内部文档，用于设置 RAID-Z 作为快速备份 MySQL 的方法。由于我们不再使用这个设置，文档也不会再被使用，我觉得把它发布出来给那些可能会发现它有一些用处的人是个好主意。对 MySQL 使用 RAID-Z 是令人惊奇的，它从未让我们失望过，并且允许每小时进行基于快照的备份，没有停机时间。不过，我们后来转向了 AWS RDS 实例。*

这将是多篇文章的一部分，这些文章将允许你为 MySQL 建立一个 RAID-Z 设置。或者说，至少以前可以！虽然这是面向 Azure 的，但我确信它在任何地方都适用。

随着我在接下来的几天里添加更多的部分，我会在这里添加链接。

# 文章系列

1.  [在 CentOS 上安装 ZFS](https://dev.to/rgeraldporter/installing-zfs-on-centos-2fkl)
2.  [使用 CentOS 上的 ZFS 为 MySQL 创建 RAID-Z 驱动器](https://dev.to/rgeraldporter/creating-a-raid-z-drive-with-zfs-on-centos-3484)
3.  [设置用于 MySQL 主-主复制器模式的 RAID-Z](https://dev.to/rgeraldporter/setting-up-raid-z-for-use-in-a-mysql-master-master-replicator-pattern-4eme)
4.  **创建 MySQL 数据目录的 ZFS 映像**

# 指令

为备份创建 ZFS 映像非常简单。

首先，ZFS 快照必须存在。如果还没有，请创建一个。

如果这是针对 MySQL 的，并且 MySQL 中的表使用 MyISAM，那么请确保暂时关闭服务器，或者暂时锁定表。根据我的经验，这些锁只是延迟写入直到解锁，在 ZFS，快照惊人地快。

```
FLUSH TABLES WITH READ LOCK; 
```

这个例子将假设您正在获取 MySQL 数据目录，它将存储在一个名为`sqlstorage`的容器中，文件系统为`data`。如果您的设置名称不同，请进行调整。

```
zfs snapshot sqlstorage/data@now 
```

现在我们可以解锁表或再次启动 MySQL 服务器。

```
UNLOCK TABLES; 
```

我们只需要一个命令就可以将这个快照发送到一个文件中。根据文件系统的大小，这可能需要一段时间。

```
zfs send sqlstorage/data@now >/tmp/sqlstorage-backup.zfs 
```

某样东西严重过时了吗？这对你有用吗？请发表评论，以帮助其他可能使用此指导的人。