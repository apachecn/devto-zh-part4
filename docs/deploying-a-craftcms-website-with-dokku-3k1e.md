# 使用 Dokku 部署 CraftCMS 网站

> 原文：<https://dev.to/mitchartemis/deploying-a-craftcms-website-with-dokku-3k1e>

我最近用 Craft CMS 建立了我的个人博客，并想把它放在我自己的服务器上。为了充分利用我自己的服务器，我倾向于使用 Dokku 运行 web 应用程序。这篇博客文章详细介绍了为您自己的项目这样做的过程。

## 什么是 Dokku？

Dokku 是一个由 Docker 支持的小型 PaaS 服务，您可以在自己的服务器上运行。可以把它想象成一个自托管的命令行 Heroku。

当您希望以低廉的价格在一台服务器上托管多个小型项目时，这是非常好的选择。虽然你不太可能在价格上击败 Heroku 的免费层，但对于小项目来说，它绝对胜过 9 美元层。你的网站在空闲时不会进入睡眠状态，你可以设置 SSL(也很容易)，如上所述，你可以在同一个服务器上托管多个网站。

总的来说，Dokku 相对于标准服务器的优势在于，如果你的项目使用相同依赖项的不同版本，例如一个项目在 PHP 7.3 上，另一个在 7.2 上，Dokku(和 Docker)使这变得非常容易。

Dokku 的其他优势包括:

*   类似 Heroku 的 git 推送部署。
*   与 Heroku buildpacks 兼容。
*   易于设置和链接数据库、存储、缓存等到您的应用程序。
*   入门要求很低——一个$5 的 DO 实例对于多个小型应用程序来说就足够了。

更多信息和安装请参见 [Dokku 指南](//dokku.viewdocs.io/dokku/)。本教程的其余部分假设您已经在服务器上安装了 Dokku，并且能够 SSH 到服务器来运行命令。此外，Git repo 中有一个 CraftCMS 应用程序可以部署。

## 什么是 CraftCMS？

如果你正在阅读这篇文章，你很可能已经知道什么是 CraftCMS。如果你偶然看到这篇文章，但不知道:CraftCMS 是一个非常棒的内容管理系统。它是用现代工具构建的，包括 PHP 7、Twig 和 Yii 框架。制作插件很容易，而且对于快速搭建管理区域非常有效。

您真正需要关注的是将您的设计集成到前端。即便如此，使用 Twig，这也是一种非常愉快的开发体验。

## 准备好您的 CraftCMS 项目

更新您的`composer.json`文件并添加以下内容。

```
 require: {
+    "php": "~7.2.0",
+    ext-gd: *,
    // ...
 },
  extra: {
+    heroku: {
+      "document-root": "web",
+      compile: [
+        chmod 755 storage
+      ]
+    }
  } 
```

我们需要指定 PHP 版本，这样 Dokku 就知道使用哪个版本。CraftCMS 还需要 GD 库或 Imagick 来进行图像编辑。

将文档根目录设置为`web`，并为处理上传的任何目录设置正确的权限。

在应用程序的根目录下添加一个名为`Procfile`的新文件。

```
web: vendor/bin/heroku-php-nginx -C nginx.conf.d/nginx.conf web 
```

这让我们可以添加自己定制的 nginx 配置。

将 nginx 配置文件添加到`nginx.conf.d/nginx.conf`

```
 index index.html index.htm index.php;

    location ^~ /admin {
        try_files $uri $uri/ /index.php?$query_string;
    }
    location ^~ /cpresources {
        try_files $uri $uri/ /index.php?$query_string;
    }
  location / {

    gzip on;
    gzip_min_length  1100;
    gzip_buffers  4 32k;
    gzip_types    text/css text/javascript text/xml text/plain text/x-component application/javascript application/x-javascript application/json application/xml  application/rss+xml font/truetype application/x-font-ttf font/opentype application/vnd.ms-fontobject image/svg+xml;
    gzip_vary on;
    gzip_comp_level  6;

        try_files $uri $uri/ /index.php?$query_string;
  }

  error_page 400 401 402 403 405 406 407 408 409 410 411 412 413 414 415 416 417 418 420 422 423 424 426 428 429 431 444 449 450 451 /400-error.html;
  location /400-error.html {
    root /var/lib/dokku/data/nginx-vhosts/dokku-errors;
    internal;
  }

  error_page 404 /404-error.html;
  location /404-error.html {
    root /var/lib/dokku/data/nginx-vhosts/dokku-errors;
    internal;
  }

  error_page 500 501 502 503 504 505 506 507 508 509 510 511 /500-error.html;
  location /500-error.html {
    root /var/lib/dokku/data/nginx-vhosts/dokku-errors;
    internal; 
```

现在要告诉 Dokku 使用哪个构建包，在项目根目录下创建一个`.buildpacks`文件，内容如下。

```
https://github.com/heroku/heroku-buildpack-php 
```

对于手工艺方面的事情来说，差不多就是这样了。我还建议为媒体上传设置 S3 或谷歌云存储，这消除了担心持久存储的痛苦。

## 配置 Dokku

在您的服务器上，使用您选择的域名
创建应用程序

```
dokku apps:create <yourdomainname.com> 
```

建立一个 Postgres 数据库(或者 MySQL，如果你喜欢的话),并将其链接到应用程序

```
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git postgres
dokku postgres:create <databasename>
dokku postgres:link <databasename> <yourdomainname.com> 
```

运行`dokku postgres:info databasename`来获取数据库凭证。我们需要解析以下格式的 URL，并为每个格式添加环境变量。

```
dokku postgres:info <databasename>
# Dsn: postgres://<USERNAME>:<PASSWORD>@<SERVER>:5432/<DATABASE>
dokku config:set <yourdomainname.com> ENVIRONMENT=production
dokku config:set <yourdomainname.com> SECURITY_KEY=YOUR_CRAFT_SECURITY_KEY
dokku config:set <yourdomainname.com> DB_DRIVER=pgsql
dokku config:set <yourdomainname.com> DB_SERVER=<databasename>
dokku config:set <yourdomainname.com> DB_USER=<USERNAME>
dokku config:set <yourdomainname.com> DB_PASSWORD=<PASSWORD>
dokku config:set <yourdomainname.com> DB_DATABASE=<DATABASE>
dokku config:set <yourdomainname.com> DB_PORT=5432
dokku config:set <yourdomainname.com> DB_SCHEMA=public
dokku config:set <yourdomainname.com> DB_TABLE_PREFIX= 
```

请注意，您应该仔细检查这些 env 变量是从您的手工配置文件中调用的。

如果您需要在服务器上永久存储(例如，没有 S3 或 GCS)，那么您可以使用`storage:mount`进行设置。

```
# optional permanent storage set up
dokku storage:mount <yourdomainname.com>/var/lib/dokku/data/storage/<anyuniqueidentifier>:/app/storage 
```

## 首先部署

如果您已经在本地设置了内容和用户，并希望将其迁移到生产环境中。最好的办法是使用诸如 [TablePlus](https://tableplus.com) 或 [Sequel Pro](https://sequelpro.com/) 之类的工具直接连接到数据库(使用从上述命令中找到的 Postgres 凭证，并使用 SSH 直接连接到它)。转储本地表和内容，并将数据库导入远程数据库。仔细检查您的`sites`表，看看您的域是否被硬编码到了`baseUrl`。如果是，则将其更新到活动域。

如果您不想这样做，而是想从头开始，您可以使用 SSH 进入应用程序，并在第一次部署后从应用程序中运行`./craft setup`。你可以用`dokku run <yourdomainname.com> bash`然后用`cd`进入`app`目录。

在您的本地项目中，您需要添加一个新的 git 远程位置，其中包含您的服务器的凭证。之后你就可以推了！

```
git remote add dokku dokku@serverip:domainname
git push dokku master 
```

希望如果一切顺利，当你尝试访问网站时，你应该看到 Craft 的设置页面(或者你的网站，如果你已经导入了数据库的话)。这是假设您已经将您的域指向服务器。

## 同步更新

如果像我一样，您喜欢限制 live 站点上的设置更改，以便它们与您的 git repo 保持同步，请确保您的`config/app.php`具有以下设置。

```
 '*' => [
  // ...
  'useProjectConfigFile' => true,
  // ...
],
'production' => [
  // ...
  'allowAdminChanges' => false,
  // ...
] 
```

您还需要添加这个 Dokku 挂钩，以便自动同步项目 YAML 文件。这可以防止您的网站在等待您从管理员那里同步设置时，每次您推送更新时都出现故障。

```
//  app.json  {  "scripts":  {  "dokku":  {  "postdeploy":  "./craft project-config/sync"  }  }  } 
```

就是这样！保存、提交、推送和更新应该能够正常工作！

## SSL

如果您使用的是 CloudFlare，那么 SSL 应该可以开箱即用。如果没有，多库有设置 AutoSSL 的命令，你可以在这里找到:[http://dokku.viewdocs.io/dokku/configuration/ssl/](http://dokku.viewdocs.io/dokku/configuration/ssl/)

## 关闭思绪

我花了相当长的时间来正确地设置它。现在我有了它的配置，用 Dokku 部署更新变得非常容易。我已经用谷歌媒体云存储运行了几个星期，没有出现任何问题。我希望这个指南能帮助那些也想做类似设置的人！