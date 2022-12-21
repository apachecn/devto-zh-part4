# 将 NextCloud 数据备份到 Dropbox

> 原文：<https://dev.to/shubho/backup-nextcloud-data-to-dropbox-59kc>

Dropbox 是我家主要的云同步提供商。我们所有的个人文件，家庭照片，代码项目等。住在那里。我和我妻子共有的文件夹在我们的帐户之间共享。其他一切都是独立的。我们还将数据备份到外部硬盘上。我个人的 MacBook 也用 Backblaze。由于几年前的一次数据丢失事件，我们过度使用了我们的备份策略。

在我们的家庭网络中，Debian 系统上也有 Nextcloud。它足以作为我们的媒体中心。我想为 Nextcloud 安装创建一个备份系统。此时我不想使用 S3 或其他任何东西，因为我们的 Nextcloud 实例不是一个巨大的数据猪。因此，我使用现有的 Dropbox 实例来实现这一点。Nextcloud 允许 Dropbox 用作外部存储。但我使用本地硬盘作为 Nextcloud 的存储空间，然后将它同步到 Dropbox 文件夹中。

## 投件箱的初始设置

1.  在主机上(安装了 Nextcloud 的地方)，安装 Dropbox。此过程会因您的操作系统而异。
2.  在 Dropbox 里创建一个文件夹“NEXTCLOUD-BKP”。如果需要，将其设为共享文件夹。
3.  使用选择性同步功能删除除新创建的文件夹之外的所有其他文件夹。由于我的 Debian 系统的主要用途是 Nextcloud，所以我只同步了 Dropbox 的备份文件夹，而没有同步任何其他数据。
4.  这一步是特定于我的设置。我的 Nextcloud 实例使用 Apache web 服务器，并作为 *www-data* 用户运行。但是我的 Dropbox 是作为主登录用户安装的。为了使我能够复制 *www-data* 用户拥有的数据，将登录用户添加到 *www-data* 组。`usermod -a -G www-data <LOGGED_IN_USER>`。将“NEXTCLOUD-BKP”文件夹的权限设为 g+rwx。`chmod g+rwx ~/Dropbox/NEXTCLOUD-BKP`。确保 Dropbox 和“NEXTCLOUD-BKP”文件夹设置了“x”位，使 *www-data* 用户能够复制到其子文件夹。[https://askubuntu . com/questions/455000/group-permissions-allow-but-get-permission-denied](https://askubuntu.com/questions/455000/group-permissions-allow-but-still-get-permission-denied)

## 在 Nextcloud 上启用维护模式

这将锁定现有登录用户的会话，以防止数据库中出现不一致。它还会阻止新的登录。`php $NEXTCLOUD_PATH/occ maintenance:mode --on`。

## 使用 rsync 复制 Nextcloud 数据

你可以拷贝整个 Nextcloud 文件夹，也可以只拷贝 data，config 和 themes 文件夹。

```
rsync -Aavx $NEXTCLOUD_PATH/data ~/Dropbox/NEXTCLOUD-BKP
rsync -Aavx $NEXTCLOUD_PATH/config ~/Dropbox/NEXTCLOUD-BKP
rsync -Aavx $NEXTCLOUD_PATH/themes ~/Dropbox/NEXTCLOUD-BKP 
```

## 备份 Nextcloud 数据库

我的 Nextcloud 数据库是 MariaDB。如果您使用 PostgreSQL 或 SQLite，那么这一步对您来说会有所不同。

```
# Database name is nextcloud
/usr/bin/mysqldump --defaults-extra-file=$MYSQL_CREDENTIALS_FILE --single-transaction --databases nextcloud > ~/Dropbox/NEXTCLOUD-BKP/database/nc-db-bkp_`date +"%Y-%m-%d-%H-%M"`.bak 
```

`$MYSQL_CREDENTIALS_FILE`为下一个云数据库提供用户名和密码。【https://dev.mysql.com/doc/refman/5.5/en/option-files.html】[是数据库的名称。](https://dev.mysql.com/doc/refman/5.5/en/option-files.html)

## 更新创建/更新的备份文件夹的权限

这一步取决于您的设置，可能完全是可选的。因为我有不同的用户运行 nextcloud，所以我需要这样做来让 Dropbox 读取新创建的文件。

```
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/data
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/config
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/themes
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/database 
```

因为我的安装是在我的家庭网络中，并且在防火墙后面，只打开需要的端口，所以我觉得这样做是安全的。YMMV。

## 禁用 Nextcloud 上的维护模式

完成上述所有步骤后，禁用维护模式。`php $NEXTCLOUD_PATH/occ maintenance:mode --on`。

## 设置一个 cron

我每天晚上 9 点运行一次脚本。`0 21 * * * nextcloud-backup.sh`。该脚本必须作为 *www-data* 用户或运行 nextcloud 进程的用户运行。这是完整的脚本。

```
#!/usr/bin/env bash

# Can set the below three as environment variables or uncomment to set actual paths
NEXTCLOUD_PATH=<NEXTCLOUD_INSTALLATION_PATH>
DROPBOX_NEXTCLOUD_PATH=<PATH_TO_NEXTCLOUD_BACKUP_FOLDER_IN_DROPBOX>
MYSQL_CREDENTIALS_FILE=<MYSQL_CREDENTIALS_FILE_CONF_FORMAT>

PHP_PATH=`which php`
MYSQLDUMP_PATH=`which mysqldump`
# Enable Maintenance mode
$PHP_PATH $NEXTCLOUD_PATH/occ maintenance:mode --on

# Backup data, config and themes from nextcloud directory.
# Can sync the entire nextcloud directory if needed
rsync -Aavx $NEXTCLOUD_PATH/data $DROPBOX_NEXTCLOUD_PATH
rsync -Aavx $NEXTCLOUD_PATH/config $DROPBOX_NEXTCLOUD_PATH
rsync -Aavx $NEXTCLOUD_PATH/themes $DROPBOX_NEXTCLOUD_PATH

# Backup MySQL
$MYSQLDUMP_PATH --defaults-extra-file=$MYSQL_CREDENTIALS_FILE --single-transaction --databases nextcloud > $DROPBOX_NEXTCLOUD_PATH/database/nc-db-bkp_`date +"%Y-%m-%d-%H-%M"`.bak

# Update permissions of the backup path to 777 (Because the user running nextcloud is different.)
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/data
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/config
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/themes
chmod -R 777 $DROPBOX_NEXTCLOUD_PATH/database

# Disable Maintenance mode
$PHP_PATH $NEXTCLOUD_PATH/occ maintenance:mode --off 
```

这为我提供了一个很好的设置，我的 Nextcloud 数据被备份到 Dropbox。请注意，`mysqldump`不是增量的。因此，如果需要，您可能需要删除旧文件。我只保留了 5 天的备份，因为实际的数据文件夹被`rsync`完全覆盖。您可以通过创建一个档案并保存而不是实际的副本来更新`rsync`命令。

*最初发表于 [Shubho。dev](https://www.shubho.dev/blog/backup-nextcloud-data-to-dropbox)T3】*

特色图片来自 Pixabay 的德克·伍特斯