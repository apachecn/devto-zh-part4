# MySQL/MariaDB 的简单 Laravel DB 备份

> 原文：<https://dev.to/chyn_km/simple-laravel-db-backups-for-mysql-mariadb-22mj>

数据库(DB)保存与 web 应用程序相关的所有关键信息。因此，数据库备份是必要的。我将发布一个简单的代码片段，适用于 Laravel 应用程序，将您的数据库备份到 [AWS S3](https://aws.amazon.com/s3/) 。我假设您使用 EC2 机器来托管 Laravel 应用程序。

因为配置和设置 S3 存储桶超出了本文的范围，所以我将概述一些步骤以供参考

1.  首先创建一个私有的 S3 存储桶(例如: *db-backups* )。
2.  **为存储桶**启用版本控制，以便覆盖仍将保留备份。
3.  完成创建存储桶并记下 ARN(例如:*arn:AWS:S3:::d b-backups/**)。
4.  在不选择任何现有策略的情况下，创建一个具有编程写访问权限的 IAM 用户(例如: *s3user* )。
5.  记下 API 访问/密钥和用户的 ARN(例如:*arn:AWS:iam::8 xx 45 xxxx 57:user/S3 user*)。
6.  在 IAM 用户的*权限*选项卡下，添加以下内容作为内联策略。

    ```
    {
        "Version":"2012-10-17",
        "Statement":[
           {
              "Effect":"Allow",
              "Action":[
                 "s3:PutObject"
              ],
              "Resource":[
                 "arn:aws:s3:::db-backups/*"
              ]
           }
        ]
    } 
    ```

7.  在 S3 时段策略中，添加以下内容:

```
 {
       "Version": "2012-10-17",
       "Statement": [
          {
             "Sid": "statement1",
             "Effect": "Allow",
             "Principal": {
                "AWS": "arn:aws:iam::8XX45XXXX57:user/s3user"
             },
             "Action": [
                 "s3:PutObject"
             ],
             "Resource": [
                "arn:aws:s3:::db-backups/*"
             ]
          }
       ]
    } 
```

务必更新*用户*、*斗* ARN 的。该 IAM 用户将只能写入存储桶。出于安全原因，不授予其他权限。

回到主题，Laravel 中的数据库备份。如果您使用 RDS 作为数据库存储，EC2 机器将不会有`mysqldump`命令。因此，请使用(Ubuntu 发行版)
安装它们

`sudo apt-get install mysql-client`

安装完成后，在 shell 中执行以下命令，注意路径
`which mysqldump`(例如:*/usr/bin/MySQL dump*)&
`which gzip`(例如: */bin/gzip* )

更新 Laravel 应用程序
的`.env`文件变量

```
AWS_ACCESS_KEY_ID=XXXXXXX
AWS_SECRET_ACCESS_KEY=XXXXXXX
AWS_DEFAULT_REGION=eu-central-1
DB_AWS_BUCKET=db-backups

MYSQLDUMP_EXE=/usr/bin/mysqldump
GZIP_EXE=/bin/gzip 
```

通读 [Laravel 文档](https://laravel.com/docs/5.8/filesystem)并为 S3 访问安装软件包。在`config/filesystems.php`文件中新建一个*盘*，例如:

```
 's3db' => [
            'driver' => 's3',
            'key' => env('AWS_ACCESS_KEY_ID'),
            'secret' => env('AWS_SECRET_ACCESS_KEY'),
            'region' => env('AWS_DEFAULT_REGION'),
            'bucket' => env('DB_AWS_BUCKET'),
        ], 
```

为了利用 [Laravel 配置缓存](https://laravel.com/docs/5.8/configuration#configuration-caching)，我创建了一个名为`config/env.php`的文件，在其中配置了自定义的环境变量。它类似于`config/app.php`，除了它保存应用程序中新引入的变量。下面是一个例子:

```
<?php 

return [
    'mysqldump_exe' => env('MYSQLDUMP_EXE'),
    'gzip_exe' => env('GZIP_EXE'),
]; 
```

我更喜欢在`app/Classes`目录下创建自定义类。您可以随意使用您的文件，但是要相应地更改以下文件的名称空间。创建一个文件`app/Classes/DbBackup.php`。代码是不言自明的。

```
<?php

namespace App\Classes;

use Illuminate\Support\Facades\Storage;

class DbBackup
{
    private $fileName, $tmpFile, $gzipFile;

    /**
     * Initialise the variables
     *
     * @return void
     */
    public function __construct()
    {
        $this->fileName = config('database.connections.mysql.database').'-'.date('Y-m-d').'.sql';
        $this->tmpFile = '/tmp/'.$this->fileName;
        $this->gzipFile = $this->tmpFile.'.gz';
    }

    /**
     * Create the backup
     *
     * @return object this
     */
    protected function createBackup()
    {
        exec(config('env.mysqldump_exe').
            ' --user='.config('database.connections.mysql.username').
            ' --host='.config('database.connections.mysql.host').
            ' --password='.config('database.connections.mysql.password').
            ' --databases '.config('database.connections.mysql.database').
            ' > '.$this->tmpFile);

        exec(config('env.gzip_exe').' '.$this->tmpFile);

        return $this;
    }

    /**
     * Upload to S3
     *
     * @return object this
     */
    protected function uploadToS3()
    {
        Storage::disk('s3db')
            ->put($this->fileName.'.gz', fopen($this->gzipFile, 'r'));

        return $this;
    }

    /**
     * Delete the temp file
     *
     * @return object this
     */
    protected function removeBackup()
    {
        sleep(2);
        unlink($this->gzipFile);
    }

    /**
     * Method which calls all sub functions
     *
     * @return void
     */
    public function init()
    {
        $this->createBackup()
            ->uploadToS3()
            ->removeBackup();
    }

} 
```

现在，我们要求仅在生产系统中每天运行数据库备份。因此更新`app/Console/Kernel.php`中的方法。

```
 protected function schedule(Schedule $schedule)
    {
        // this code will create the automatic DB backups.
        $schedule->call('App\Classes\DbBackup@init')
            ->daily()
            ->environments(['production']);
    } 
```

只有在配置了 [Laravel 调度程序](https://laravel.com/docs/5.8/scheduling#introduction)的情况下，上述代码才会运行。

就这样，伙计们！每天午夜将创建数据库备份并保存到 S3。