# Laravel 6 并进行授权

> 原文：<https://dev.to/santhoshj/laravel-6-and-make-auth-4aln>

本周二，拉勒维尔迎来了英雄般的欢迎。当我开始试着去看看新的是什么样的时候，我立刻被撞到了墙上。

```
php artisan make:auth 
```

在 Laravel 6 中，Make Auth 命令不再可用。但是，不用担心。我们有更好的解决方案。

Laravel 开发了一个新的包——Laravel UI，它将应用程序的 UI 部分分离出来。这可以帮助开发人员创建仅服务器应用程序(API 主机)或仅控制台应用程序，而无需附加额外的 UI 负担。

Laravel UI 可以用 composer 命令安装:

```
composer create-project laravel/laravel blog
cd blog
composer require laravel/ui 
```

一旦这个包安装成功，它会给出一些新的 artisan 命令。

```
➜ php artisan ui --help
Description:
  Swap the front-end scaffolding for the application

Usage:
  ui [options] [--] <type>

Arguments:
  type                   The preset type (bootstrap, vue, react)

Options:
      --auth             Install authentication UI scaffolding
      --option[=OPTION]  Pass an option to the preset command (multiple values allowed)
  -h, --help             Display this help message
  -q, --quiet            Do not output any message
  -V, --version          Display this application version
      --ansi             Force ANSI output
      --no-ansi          Disable ANSI output
  -n, --no-interaction   Do not ask any interactive question
      --env[=ENV]        The environment the command should run under
  -v|vv|vvv, --verbose   Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug 
```

UI 可以用 VueJS 或 React 启动。

```
php artisan ui vue 
php artisan ui react 
```

要创建身份验证框架，请在 ui 命令中添加- auth。

```
php artisan ui:auth
php artisan ui:auth --views 
```

运行第一个命令为身份验证设置路由和框架，第二个命令设置视图。

娱乐时间到了！让我们回去工作吧...！