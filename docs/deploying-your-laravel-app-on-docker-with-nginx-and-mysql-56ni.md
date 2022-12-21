# 使用 NGINX 和 MySQL 在 Docker 上部署您的 Laravel 应用程序

> 原文：<https://dev.to/baliachbryan/deploying-your-laravel-app-on-docker-with-nginx-and-mysql-56ni>

原帖也上传到这里:[Docker 上的 Laravel 申请](https://balysnotes.com/single_post/Brian_Baliach/051032437815811656)

如果你是 django 开发者，你可以在这里找到真正全面的教程: [django on Docker](https://lewiskori.com/post/deploying-a-python-django-application-using-docker)

## 现在可以吗？

Docker，镇上的新警长，他的新金靴让每个人都很兴奋。为什么？因为 Docker 是开发的福音，因为它确保了开发和生产周期的一致性，从而使您的环境标准化

我想你和我们一样对 docker 感到兴奋，因此解释了为什么你偶然发现了这篇文章。老实说，docker 首先，没有任何先验知识是一场噩梦，就像任何其他没有先验知识的新概念一样，但一旦你掌握了它，它将为你节省大量的时间，否则你会在部署期间投资。

这篇文章主要是为那些可能对 docker 知之甚少或一无所知的初学者而写的。这篇文章背后的动机在于，我无法在网上找到任何全面的 Laravel Docker 教程。我最终只能通过连续阅读多篇博客文章，并将所有这些结合成一个大规模的综合步骤来完成这项任务，我将在这篇文章中尝试记录这些步骤。

## 现在放到好东西上..

在开始之前，我假设您已经准备好了 Laravel 应用程序。如果你没有，你可以跳到 [Laravel 文档页面](https://laravel.com/docs/5.7/)为自己开发一个应用，然后回到这里继续阅读。

我还假设您的机器上已经安装了 Docker。如果没有，您有以下选择:

1.  Windows 10 Pro 用户: [Docker 桌面](https://docs.docker.com/docker-for-windows/install/)
2.  低于 Windows 10 Pro 的 Windows 10 版本用户: [Docker 工具箱](https://docs.docker.com/toolbox/toolbox_install_windows/)。这是因为 Docker 桌面系统的要求。Docker Toolbox 利用了 VirtualBox 的功能。
3.  Linux 用户: [Docker CentOS](https://docs.docker.com/install/linux/docker-ce/centos/) 。您可以从屏幕上的侧边菜单中选择您的 Linux 发行版(如果不是 CentOS ),然后按照这些发行版特定的说明进行操作。

## 第一步:创建你的“docker-compose”文件

什么是 docker 合成文件？这是一个定义了所有 docker 容器的文件，所有这些容器都可以通过运行一个相对简单的命令生成，比如:

```
docker-compose -f docker-compose.prod.yml up --build 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们将建立一个开发环境(它也可以用在您的生产环境中，有一些小的变化，我将在我的下一篇文章中记录:-))

在根目录下创建一个文件，命名为:`docker-compose.yml`

您将在该文件的后续步骤中定义容器。

在 docker-compose 文件中，我们定义了三个容器:mysql、nginx 和我们的 laravel 应用程序。

### 因此，首先，我们的 laravel 应用程序容器将被定义如下:

```
version: '2'

services:

  #  The Application
  app:
    container_name: laravel_app
    build:
      context: ./
      dockerfile: development/app.dockerfile
    volumes:
      - ./storage:/var/www/storage
    env_file: '.env.prod'
    environment:
      - "DB_HOST=database"
      - "REDIS_HOST=cache" 
```

Enter fullscreen mode Exit fullscreen mode

**以上代码概述:**

1.  **版本** -根据您的选择随意更改
2.  **container_name** -您将使用这个名称来指代您的容器。也就是说，如果你想关闭你的容器，你可以用这个名字来指代它。也可以根据自己的选择随意更改。
3.  **build** -用于从 Dockerfile 文件构建一个图像。具有以下附加选项:

    *   Docker 使用这个上下文(基本上就是你的 laravel 文件所在的位置)来引用其中的任何文件。在这种情况下。/引用 laravel 根文件夹，假设 docker-compose 文件存储在您的 laravel 根文件夹中。
    *   *dockerfile* : docker 镜像是从 Dockerfiles 构建的，docker files 通常包含应该在容器内部运行的附加命令。在本例中，我们使用 dockerfile 来构建我们的**应用程序**容器。还要注意，我们使用了`development/app.dockerfile`。这意味着我们的 docker 文件位于 laravel 应用程序根目录下的“开发”文件夹中。
4.  **卷**——docker 容器使用卷在主机和正在运行的 docker 容器之间共享文件。完整冒号的左边代表我们的主机，右边代表我们的 docker 容器。在这种情况下，我们在 laravel 应用程序的存储文件夹中共享所有数据，docker 容器安装在 */var/www/storage*

5.  这定义了我们的 laravel。env 文件，在我们的例子中是 env.prod，我们将使用它来输入 docker 容器特定的环境变量，我们将在本文后面看到。

6.  **环境**——这定义了将在我们的 docker 机器上设置的环境变量。在这种情况下，如果我们可以在我们的 linux 容器中执行一个 bash 命令，并引用我们在这里定义的环境变量，即`echo $DB_HOST`将打印出:**数据库**

### 我们的 NGINX 容器将被定义如下:

```
# The Web Server
  web:
    container_name: nginx_server
    build:
      context: ./
      dockerfile: development/web.dockerfile
    volumes:
      - ./storage/logs/:/var/log/nginx
    ports:
      - 8990:80 
```

Enter fullscreen mode Exit fullscreen mode

**以上代码概述:**

1.  **container _ name**——同样，您的容器的名称，您可以选择更改。
2.  **构建** -定义同上。在这里你可以看到我们将这个容器的 dockerfile 定义为 web.dockerfile。
3.  **体积** -定义同上。在这里，我们将 laravel 的日志文件夹与 nginx 的日志文件夹共享。
4.  **ports**——这里，我们定义了 docker 容器将监听的主机上的端口，以及 docker 在容器部署期间创建的虚拟网络上的端口。通过理解冒号的左边定义主机，因此主机上的端口，冒号的右边定义 docker 容器，因此 docker 容器上的端口，可以容易地理解这一点。

### 我们的 MySQL 容器将被定义如下:

```
# The Database
  database:
    container_name: mysql_database
    image: mysql:5.7
    volumes:
      - dbdata:/var/lib/mysql
    environment:
      - "MYSQL_DATABASE=Baly"
      - "MYSQL_USER=phpmyadmin"
      - "MYSQL_PASSWORD=phpmyadmin"
      - "MYSQL_ROOT_PASSWORD=finallyJDBC2017."
    ports:
      - 8991:3306 
```

Enter fullscreen mode Exit fullscreen mode

**以上代码概述:**

1.  **集装箱名称** -参考上文。
2.  **image**——在这种情况下，我们没有定义一个 dockerfile 来构建我们的容器，而是定义了一个 image。因此，我们的 docker 容器将从我们在本例中定义的图像`mysql5:7`中构建。您可以将这个 mysql 版本与您正在开发的版本进行切换。请记住，关于您的 laravel 应用程序，最新版本的 mysql 可能无法与您的 laravel 应用程序一起工作。这是因为最新版本的 mysql 使用了一种不同的认证技术，MySQL 或 pdo php 扩展可能都不支持这种技术。因此，当调用`mysql:latest`而不是`mysql:5.7`时要小心。
3.  **卷**——仍然是相同的概念，除了现在我们已经从我们的主机定义了`dbdata`，它将映射到 docker 容器上的`/var/lib/mysql`。
4.  **环境**——与上面定义的概念相同，只是在这种情况下，我们的 mysql 数据库将使用我们设置的变量进行初始化。因此，我们的容器在构建之后，将自动拥有一个名为`database`的数据库，一个名为`secret`的用户，由密码`secret`和根密码`secret_root`标识。你可以随意改变这些。我们在我们的`env.prod`文件中定义这些设置，以便不使我们当前的`.env`文件设置与我们的容器文件设置冲突。
5.  **端口**——同上，除了我们的 mysql 容器将监听主机上的端口 8991 和容器网络上的 3306 (mysql 的默认端口)。

### 定义您的命名卷

将以下内容复制粘贴到 docker-compose.yml 文件中:

```
volumes:
  dbdata: 
```

Enter fullscreen mode Exit fullscreen mode

确保您需要在 docker-compose.yml 文件中保留缩进，以确保 docker-compose 正确读取它。最后，您的 docker-compose 文件应该如下所示:

```
version: '2'

services:

  #  The Application
  app:
    container_name: laravel_app
    build:
      context: ./
      dockerfile: development/app.dockerfile
    volumes:
      - ./storage:/var/www/storage
    env_file: '.env.prod'
    environment:
      - "DB_HOST=database"
      - "REDIS_HOST=cache"

  # The Web Server
  web:
    container_name: nginx_server
    build:
      context: ./
      dockerfile: development/web.dockerfile
    volumes:
      - ./storage/logs/:/var/log/nginx
    ports:
      - 8990:80

  # The Database
  database:
    container_name: mysql_database
    image: mysql:5.7
    volumes:
      - dbdata:/var/lib/mysql
    environment:
      - "MYSQL_DATABASE=Baly"
      - "MYSQL_USER=phpmyadmin"
      - "MYSQL_PASSWORD=phpmyadmin"
      - "MYSQL_ROOT_PASSWORD=finallyJDBC2017."
    ports:
      - 8991:3306

    # redis
  cache:
    image: redis:3.0-alpine

volumes:
  dbdata: 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:定义我们的 Dockerfiles。

在这一步中，我们为刚刚在 docker-compose 文件中定义的容器定义 docker 文件。这些 docker 文件将代表我们希望在 docker 容器中运行的一系列命令。

### 定义我们的‘app’docker file(laravel _ app)

在你的 laravel 应用的根目录下创建一个文件夹，命名为`development`。在您刚刚创建的文件夹中，创建一个文件并将其命名为`app.dockerfile`(是的，没有任何扩展名)。打开这个文件，将下面的代码复制粘贴到里面:

```
FROM php:7.2-fpm

COPY composer.lock composer.json /var/www/

COPY database /var/www/database

WORKDIR /var/www

RUN apt-get update && apt-get -y install git && apt-get -y install zip

RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('SHA384', 'composer-setup.php') === 'a5c698ffe4b8e849a443b120cd5ba38043260d5c4023dbf93e1558871f1f07f58274fc6f4c93bcfd858c6bd0775cd8d1') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && php composer.phar install --no-dev --no-scripts \
    && rm composer.phar

COPY . /var/www

RUN chown -R www-data:www-data \
        /var/www/storage \
        /var/www/bootstrap/cache

RUN  apt-get install -y libmcrypt-dev \
        libmagickwand-dev --no-install-recommends \
        && pecl install mcrypt-1.0.2 \
        && docker-php-ext-install pdo_mysql \
        && docker-php-ext-enable mcrypt

RUN mv .env.prod .env

RUN php artisan optimize 
```

Enter fullscreen mode Exit fullscreen mode

**以上代码概述**

1.  **From PHP:7.2-fpm**——这意味着将从一个图像构建我们的容器，`php:7.2-fpm`。此外，您可以更改这个版本以满足您的开发环境需求。

2.  **复制**——在第一个复制命令中，我们将`composer.lock`和`composer.json`从我们的根文件夹(在我们的主机中)复制到 docker 容器中的`/var/www/`。在第二个复制命令中，我们将主机中的`database`文件夹复制到 docker 容器中的`/var/www/database`文件夹。这是因为，第一，当我们下载依赖项时，我们希望确保我们在开发环境中使用的依赖项(在 composer.json 中)将反映在容器中；第二，我们可以在 docker 容器中访问我们的迁移文件，以防我们可能需要运行`migrate`命令。

3.  **工作目录**——我们将工作目录设置为`/var/www`，这意味着我们不必`cd`到这个文件夹(移动到这个文件夹)，以防我们需要运行 bash 命令。

4.  **运行**——在这里，我们安装 laravel 需要的所有依赖项，包括 composer 和 composer 需要的依赖项。请注意`if(hash_file('SHA384'...`行。这里定义的哈希值会随着每次更新而改变，因此如果您的安装程序失败并显示消息:**安装程序损坏**，请考虑从: [Get Hash Value](https://getcomposer.org/download/) 获取正确的哈希值。

5.  **复制。/var/www** -此时，我们将所有文件夹内容复制到 docker 容器中的`/var/www`文件夹中。

6.  **运行**——在最后的运行命令中，我们清空了应用程序缓存和其他缓存，并安装了 mysql 驱动程序，laravel 用它来连接数据库。之后，我们将我们的`.env.prod`文件重命名为`.env`,因为这个文件将包含特定于 docker 容器环境的正确的环境变量，因此应该由 laravel 使用。我们运行`php artisan optimize`来删除`.env`文件的缓存版本。

请注意，没有必要从我们的根文件夹(如供应商文件夹)中复制所有内容，docker 提供了一个`.dockerignore`文件，它的工作方式非常类似于`.gitignore`文件。我们的 dockeringore 文件将如下所示:

```
.git
.idea
.env
node_modules
vendor
storage/framework/cache/**
storage/framework/sessions/**
storage/framework/views/**
development 
```

Enter fullscreen mode Exit fullscreen mode

将此文件保存在与 app.dockerfile(开发文件夹)相同的文件夹中。

对于您的`.env.prod`文件，复制粘贴您的`.env`文件，并将其重命名为`.env.prod`。在数据库设置中，更改`DB_HOST`以匹配您的 **mysql** 容器的名称，更改密码以匹配您在`docker-compose.yml`文件中定义的内容。如果您遵循了我的所有步骤，没有做任何更改，那么您的. env.prod 文件应该如下所示:

```
DB_CONNECTION=mysql
DB_HOST=mysql_database
DB_PORT=3306
DB_DATABASE=Baly
DB_USERNAME=phpmyadmin
DB_PASSWORD=phpmyadmin 
```

Enter fullscreen mode Exit fullscreen mode

### 定义我们的‘web’docker 文件

在刚刚创建的同一个文件夹(开发文件夹)中创建一个`web.dockerfile`。将以下内容复制粘贴到 dockerfile:

```
FROM nginx:1.10-alpine

ADD development/vhost.conf /etc/nginx/conf.d/default.conf

COPY public /var/www/public 
```

Enter fullscreen mode Exit fullscreen mode

**以上代码概述**

我们从图像:`nginx:1.10-alpine`构建 dockerfile。然后，我们将 nginx 的`default.conf`文件替换为我们稍后创建的`vhost.conf`文件。

我们还将 laravel 应用程序的公共目录复制到 nginx 的公共目录，这将为我们所有的公共资产提供服务。

**在同一个目录下创建一个 vhost.conf 文件(开发)并复制粘贴到其中:**

```
server {
    listen 80;
    index index.php index.html;
    root /var/www/public;
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;
    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`php-fpm`容器将监听端口 9000，因此`app:9000`

## 快到了...

因此，要进行反检查，您需要已经有以下文件:

1.  **根文件夹** - `docker-compose.yml`和`.env.prod`
2.  **开发文件夹**:
    *   `.dockerignore`
    *   `app.dockerfile`
    *   `web.dockerfile`
    *   `vhost.conf`

### 如果是这样，那么你就差不多完成了，但是首先，一些先决条件:

如果你使用的是 Windows 上的 Docker Toolbox，而你的 laravel 应用程序文件夹不在`C:/users`文件夹中，你将很难在你的主机和 Docker 容器之间共享卷。这是因为当您的 docker 机器启动时，任何其他不是`C:/users`的文件夹都不会被 virtual box 挂载。因此，要解决这个问题，首先运行:
来停止正在运行的 docker 机器

```
docker-machine stop 
```

Enter fullscreen mode Exit fullscreen mode

然后打开 virtualbox，右击名为**默认**的机器，点击**设置**。导航到**共享文件夹**点击它，添加一个新文件夹，定义你的 laravel 应用文件夹的位置。记得检查**自动安装**。然后，通过运行
来启动 docker 机器

```
docker-machine start default 
```

Enter fullscreen mode Exit fullscreen mode

## 鼓卷...

假设您已经做了所有正确的事情，继续运行下面的命令:

```
docker-compose up -d --build database && docker-compose up -d --build app && docker-compose up -d --build web 
```

Enter fullscreen mode Exit fullscreen mode

最后，通过执行:
进入 laravel_app 的 docker 容器

```
docker exec -it laravel_app bash 
```

Enter fullscreen mode Exit fullscreen mode

并执行以下 laravel 命令:

```
php artisan key:generate

php artisan config:cache

php artisan route:cache 
```

Enter fullscreen mode Exit fullscreen mode

确保您在 laravel 应用程序的根文件夹中运行这个命令。这个命令构建您的容器映像，并最终启动它们。如果一切按计划进行，您应该可以在:
访问您的容器内运行的 laravel 应用程序

```
0.0.0.0:8990 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用不同的端口，用您在 docker-compose.yml 文件中定义的端口替换`8990`。

另外，请注意，对于使用 Docker Toolbox 的用户，Docker 会创建一个虚拟网络，并为其分配一个 IP 地址。你可以通过搜索`docker quickstart terminal`并运行它来找到这个 IP 地址。分配的 IP 地址将显示在弹出的终端中，您可以通过进入:
访问您的 laravel 应用程序

```
your-docker-machine-ip:8990 
```

Enter fullscreen mode Exit fullscreen mode

这就是你要的，伙计们！您已经在 docker 上成功部署了您的 laravel 应用程序！请继续关注我的下一篇文章，我将描述如何在生产环境中部署您的 Laravel 应用程序。