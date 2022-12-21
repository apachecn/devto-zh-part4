# OpenBSD 上的 PHP 错误:“env: php:没有这样的文件或目录”

> 原文：<https://dev.to/nabbisen/php-error-on-openbsd-env-php-no-such-file-or-directory-1f5p>

**封面图片由 [13smok](https://pixabay.com/users/13smok-5135280/) 原创，经过精心编辑。*

* * *

在 [OpenBSD](https://www.openbsd.org/) 中，安装 [PHP](https://www.php.net/) :
时没有创建一个名为`php`的符号链接

```
# pkg_add php 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
$  ls -1 /usr/local/bin/php*
/usr/local/bin/php-7.3
/usr/local/bin/php-config-7.3
/usr/local/bin/phpize-7.3 
```

Enter fullscreen mode Exit fullscreen mode

没关系，因为`php-X.X`在大多数情况下都工作得很好:

```
$  php-7.3 /usr/local/libexec/composer.phar require drush/drush 
```

Enter fullscreen mode Exit fullscreen mode

`$PATH` missing `php`然而，有时候一些 PHP 模块会出现意外错误，比如 [Drush](https://www.drush.org/) ，一个用于 [Drupal](https://www.drupal.org/) CMS:
的命令行 shell

```
$  drush updatedb
env: php: No such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

它试图运行`php`但被错过了！

在这种情况下，创建符号链接很有帮助:

```
#  ln -s /usr/local/bin/php-7.3 /usr/local/bin/php 
```

Enter fullscreen mode Exit fullscreen mode

然后错误将被解决:

```
$  drush updatedb
 [success] No pending updates. 
```

Enter fullscreen mode Exit fullscreen mode

随后根据需要移除额外的符号链接:

```
#  rm /usr/local/bin/php 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的阅读。
快乐计算:)