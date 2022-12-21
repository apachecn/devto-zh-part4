# 如何调试 PHP CLI-artisan 命令或单元测试

> 原文：<https://dev.to/daniel_werner/how-to-debug-php-cli-artisan-commands-or-unit-tests-14in>

## 简介

如果我们用 TDD 方法开发一个应用程序(这是我们应该做的！)，我们编写了许多在命令行中运行的代码。此外，当我们创建任务或 artisan 命令时，在命令行中调试代码会使我们的生活更容易。在这篇文章中，我将展示如何用 PhpStorm 和 Homestead 设置 cli 调试。

## 先决条件

您需要为 php cli 安装并启用 Xdebug 扩展。请注意，我将在路径中使用 php 7.2，但是您可能需要用您的 php 版本替换它。

## Xdebug 配置

打开 xdebug 配置文件:sudo nano/etc/PHP/7.2/CLI/conf . d/20-xdebug . ini

将以下配置放入文件:

```
zend_extension=xdebug.so
xdebug.remote_autostart = 1
xdebug.remote_enable = 1
xdebug.remote_connect_back = 0
xdebug.remote_port = 9000
xdebug.max_nesting_level = 512
xdebug.remote_host = 192.168.10.1
xdebug.idekey = homestead_cli 
```

上面有两个重要的配置键，可能会根据您的配置而改变:

**xdebug . remote _ host**–这是主机的 IP 地址(在 virtualbox 网络上)

**xdebug . idekey**–这是一个自定义键，也应该在 PhpStorm 中使用

## PhpStorm 配置

打开调试配置并添加新的 PHP 远程调试配置，设置服务器(如果您需要关于如何创建新服务器的指导，请查看我以前关于调试的帖子，这里:[https://42 coders . com/how-to-set-up-debugging-with-PHP storm-and-homestead/](https://dev.to/dv42coders/how-to-set-up-debugging-with-phpstorm-and-homestead-484g))，以及 IDE 键，它应该与您在 xdebug 配置中设置的相同:

[![](img/498dd5bb18e337def15decaf6a9cb01b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K3Rt-MY5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://42coders.com/wp-content/uploads/2019/06/phpstorm_cli_debug-1024x643.png)

像往常一样，通过单击右上角的绿色 bug 按钮启动调试会话，在代码中设置断点。当您启动命令行脚本时，PhpStorm 自动连接到 xdebug，您可以调试您的代码。

调试愉快！

帖子[如何调试 PHP CLI-artisan 命令或单元测试](https://42coders.com/how-to-debug-php-cli-artisan-commands-or-unit-tests/)最早出现在 [42 Coders](https://42coders.com) 上。