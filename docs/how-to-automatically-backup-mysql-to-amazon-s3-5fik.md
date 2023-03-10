# 如何自动将 MYSQL 备份到亚马逊 S3？

> 原文：<https://dev.to/supunkavinda/how-to-automatically-backup-mysql-to-amazon-s3-5fik>

大多数公司都会处理数据丢失的问题。这就是为什么我们应该随时备份数据库以便从中恢复。我不会过多谈论数据丢失，如果你需要了解更多关于数据丢失和备份的重要性，请阅读[这篇](https://www.datacenterknowledge.com/uptime/your-server-died-and-your-backups-are-gone-heres-what-do-next)。

如果你需要激励自己创建备份， [**阅读 reddit 上的这个**](https://www.reddit.com/r/cscareerquestions/comments/6ez8ag/accidentally_destroyed_production_database_on/) 讨论。

# 为什么选择亚马逊 S3？

到今天为止，我把我的 mysql 备份保存在服务器上。但是，我认为是时候使用云存储来保存备份了。为什么？原因是，如果您面临数据库和备份都被删除的问题，您将需要在其他地方有一个备份。**云**是最好的地方。

[![Cloud](img/5b8cae22bfd996ec54c6193a4388acf0.png)](https://i.giphy.com/media/3o7TKNOYAv36eKJJra/giphy.gif)

在云服务中，我选择亚马逊 S3，因为他们强大的 CLI 工具和可靠性。(此外，如果你是亚马逊服务的新手，亚马逊 S3 很容易理解)

# 我们的目标

这是我们要做的。

*   将 MYSQL 转储到一个文件夹中。(`/var/local/backups`)
*   使用 AWS CLI 将该文件夹与 S3 文件夹同步
*   最后，我们将创建一个 shell 脚本，在 cron jobs 的帮助下自动化这个过程。

# 转储 MYSQL

我用`mysqldump`转储 MYSQL 数据库，很好用。

```
mysqldump -u [user] -p[password] my_database > /where/to/dump 
```

Enter fullscreen mode Exit fullscreen mode

# 建立亚马逊 S3

*   如果还没有，创建一个 [AWS 帐户](https://aws.amazon.com/console/)并登录控制台。
*   然后，去亚马逊 S3。
*   创建具有默认配置的存储桶。(我给它取名`company-backups`。使用公司名称或您的姓名使其独一无二，因为它要求全球独一无二)
*   在桶内创建一个文件夹。(`company-backups/mysql`)

现在你完成了！让我们设置 AWS CLI

# 设置 AWS CLI

正如我前面提到的，AWS CLI 是一个强大的工具。它允许我们在我们的服务器和亚马逊 S3 之间同步文件夹。

[这里是设置 AWS CLI 的指南](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)。

安装 AWS CLI 后，确保它正在工作。

```
aws --version 
```

Enter fullscreen mode Exit fullscreen mode

这应该显示类似于，`aws-cli/1.16.170 Python/3.7.3 Linux/4.14.123-111.109.amzn2.x86_64 botocore/1.12.160`

然后，[用您的凭证配置 AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) 。

# 测试同步

让我们在创建 shell 脚本之前做一些测试。

运行下面的命令，将数据库转储到主目录中名为`backups`的临时目录中进行测试。(确保在提示时正确输入密码)

```
mysqldump -u [user] -p my_database > backups/my_database.sql 
```

Enter fullscreen mode Exit fullscreen mode

让我们试试同步。

```
aws s3 sync backups s3://company-backups/mysql/ 
```

Enter fullscreen mode Exit fullscreen mode

现在，访问 S3 控制台，检查文件是否上传到文件夹。如果是的话，太好了！

# 编写 Shell 脚本

这是有趣的部分！让我们编写 shell 脚本来完成以下工作。

*   转储数据库
*   与 S3 同步

```
#!/bin/bash

# 1 to 7
DAY_OF_WEEK=$(date +%u)

# database username
USER="myusername"

# database user's password
PASSWORD="mypassword"

# the directory to save the dumped .sql files
DUMP_DIR="/var/local/backups"

# list of databases to dump
DATABASES="database_1 database_2 database_3" 

# where to sync in S3
S3_SYNC_URL="s3://company-backups/mysql"

# loop through each database and dump
for db in $DATABASES; do echo "Dumping database: $db"
    # dump the database
    # filename ~= /var/local/backups/database_1.1.sql
    mysqldump -u $USER -p$PASSWORD --databases $db > $DUMP_DIR/$db.$DAY_OF_WEEK.sql
done

# sync the dumb directory with the directory in the s3 bucket
aws s3 sync $DUMP_DIR $S3_SYNC_URL 
```

Enter fullscreen mode Exit fullscreen mode

我在 bash 脚本中添加了注释，以便更好地理解每一步。

将上面的文件保存为任意目录中的`backup-databases.sh`(对于这类脚本，我使用`/var/www/shell`),然后运行脚本。

```
/bin/bash /path/to/backup-databases.sh 
```

Enter fullscreen mode Exit fullscreen mode

并且，检查是否一切正常。你必须看到被抛弃的人。S3 文件夹中的 sql 文件。如果是，你赢了！

# 设置 Cron 作业

那么，您打算每天调用上面的命令来备份您的数据库吗？

不，克朗在那里。

运行这个:

```
crontab -e 
```

Enter fullscreen mode Exit fullscreen mode

将此添加到文件
的末尾

```
# back up databases
00 00 * * * /bin/bash /path/to/backup-databases.sh 
```

Enter fullscreen mode Exit fullscreen mode

保存并退出。

现在，我们的脚本将在每天午夜运行脚本。您将有 7 个备份每个数据库，在您的服务器以及亚马逊 S3。

如果你有任何问题，欢迎在下面评论。

感谢您的阅读！