# 在 Windows 上使用 SQL Server 和 XAMPP / WAMP 建立一个 Laravel 项目

> 原文：<https://dev.to/mr_steelze/setting-up-a-laravel-project-with-sql-server-and-xampp-wamp-on-windows-3n7k>

昨天我花了大部分时间试图将一个现有的 Laravel 项目连接到 MsSQL。我犯了一些错误，使用了不同的教程，但我最终得到了它的设置。

##### **注意:** *本文假设您的系统上已经正确安装了 SQL Server、xampp 或 wamp 和 laravel。*

首先，我们创建一个新的 Laravel 安装，当它完成时，我们可以在编辑器中打开该文件夹。

```
laravel new mssql 
```

Enter fullscreen mode Exit fullscreen mode

打开 config 文件夹中的 database.php 文件并进行更改。

```
 /*
    |--------------------------------------------------------------------------
    | Default Database Connection Name
    |--------------------------------------------------------------------------
    |
    | Here you may specify which of the database connections below you wish
    | to use as your default connection for all database work. Of course
    | you may use many connections at once using the Database library.
    |
    */
    //from
    'default' => env('DB_CONNECTION', 'mysql'),
    //to
    'default' => env('DB_CONNECTION', 'sqlsrv'), 
```

Enter fullscreen mode Exit fullscreen mode

这并不重要，因为我们仍然要更新。env 文件，但嘿，我只是想这样做:)。

接下来，我们需要更新我们的。环境文件数据库凭证

```
DB_CONNECTION=sqlsrv
DB_HOST=127.0.0.1
DB_PORT=1433
DB_DATABASE=laravel
DB_USERNAME= #SQL Server username
DB_PASSWORD= #SQL Server password 
```

Enter fullscreen mode Exit fullscreen mode

让我们在终端中使用 migrate artisan 命令来运行我们的迁移。

```
 php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

我们应该得到下面的错误。这意味着我们需要为 SQL Server 下载微软 PHP 驱动程序。*(如果您的迁移成功运行，则无需进一步设置)*。

```
 Illuminate\Database\QueryException: could not find driver 
(SQL: select * from sysobjects where type = 'U' and name = migrations) 
```

Enter fullscreen mode Exit fullscreen mode

你可以从[微软商店](https://www.microsoft.com/en-us/download/details.aspx?id=20098)或者[这个 GitHub 页面](https://github.com/Microsoft/msphpsql/releases)下载合适的驱动。我使用了后者，这样我可以很容易地下载我的 PHP 版本(Windows-7.2.zip)的驱动程序。*(我用的是 PHP 7.2)* 。

提取 zip 文件的内容，复制 php_pdo_sqlsrv_7x_ts.dll 和 php_sqlsrv_7x_ts.dll 文件。

**在 WAP 上**

*   将文件粘贴到 C:\wamp64\bin\php\php7.x\ext
*   打开 C:\wamp64\bin\php\php7.x 文件夹中的 php.ini 文件，并添加以下几行

```
 extension=php_pdo_sqlsrv_7x_ts.dll
 extension=php_sqlsrv_7x_ts.dll 
```

Enter fullscreen mode Exit fullscreen mode

*   打开 C:\ wamp 64 \ bin \ Apache \ Apache 2.4 . xx \ bin 文件夹中的 php.ini 文件，并添加以下几行

```
 extension=php_pdo_sqlsrv_7x_ts.dll
 extension=php_sqlsrv_7x_ts.dll 
```

Enter fullscreen mode Exit fullscreen mode

*   重新启动 WAP 服务器。

**在 XAMP 上**

*   将文件粘贴到 C:\xampp\php\ext
*   打开 C:\xampp\php 文件夹中的 php.ini 文件，并添加以下几行

```
 extension=php_pdo_sqlsrv_7x_ts.dll
 extension=php_sqlsrv_7x_ts.dll 
```

Enter fullscreen mode Exit fullscreen mode

*   重新启动 xamppserver。

在我们的终端中运行迁移命令。

```
 php artisan migrate 
```

Enter fullscreen mode Exit fullscreen mode

现在我们应该能够运行我们的迁移了。

```
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table (0.01 seconds)
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table (0.01 seconds) 
```

Enter fullscreen mode Exit fullscreen mode

***加成:*** *(我在 SQL Server 2012 上顺便说一下)使用`unsignedBigInteger()`会抛出错误，改为使用`bigInteger()`。更多关于这个[这里](https://laraveldaily.com/be-careful-laravel-5-8-added-bigincrements-as-defaults/)。*

***更新:*** *您可能需要下载 [Microsoft ODBC 驱动程序](https://go.microsoft.com/fwlink/?LinkId=163712)来修复此问题-Illuminate \ Database \ query exception:SQLSTATE[IMSSP]:此扩展需要 Microsoft ODBC Driver for SQL Server 才能与 SQL Server 通信。访问以下 URL 下载用于 x64 的 SQL Server 的 ODBC 驱动程序:[https://go.microsoft.com/fwlink/?LinkId=163712](https://go.microsoft.com/fwlink/?LinkId=163712)T9】*

**都是乡亲们！
快乐编码🖖**