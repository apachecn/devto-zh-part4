# 一个简单的 Laravel + Docker starter repo

> 原文：<https://dev.to/christopherarter/a-no-fuss-laravel-docker-starter-repo-2and>

这个启动回购诞生于让 docker 与 Laravel 合作的挫败感。希望这能帮你解决一些头疼的问题！

首先在这里克隆一下回购:[https://github . com/Christopher arter/Quick-Laravel-Docker-Starter](https://github.com/christopherarter/Quick-Laravel-Docker-Starter)

先决条件:

1.  **PHP 7.1(最低)**
2.  **作曲** [安装指南](https://getcomposer.org/doc/00-intro.md)
3.  **Docker** [安装指南](https://docs.docker.com/v17.09/engine/installation/)
4.  **坞站组成** [安装指南](https://docs.docker.com/compose/install/)

### 开始使用

1.  克隆此 repo 并导航到 repo 文件夹。运行`composer create-project --prefer-dist laravel/laravel my-app`
2.  运行`mv my-app/* my-app/.* ./ && rm -rf my-app`
3.  将`.env`中的值更改为以下值:

```
DB_CONNECTION=mysql
DB_HOST=laravel_db
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel_user
DB_PASSWORD=myStrongPassword1234 
```

最后一步，运行`docker-compose up`

当您运行`docker ps`时，您应该看到您的服务在`http://localhost:9000`本地运行

**要运行与数据库交互的迁移和命令**您需要在 laravel web app 容器内。

运行`docker ps`并获取 laravel web 容器的 id。接下来，运行`docker exec -it <container-id> /bin/bash`，你应该在`/var/www/app`。现在，您可以运行 artisan 命令与数据库进行交互。

### 用数据库工具连接到数据库

要使用 MySQL Workbench、DB Beaver 或 TablePlus 等工具连接该数据库，您可以使用您在`.env`文件中指定的`3305`端口、用户名和密码来访问它。