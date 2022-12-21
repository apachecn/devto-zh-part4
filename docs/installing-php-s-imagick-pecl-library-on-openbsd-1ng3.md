# 在 OpenBSD 上安装 PHP 的 Imagick (PECL 库)

> 原文：<https://dev.to/nabbisen/installing-php-s-imagick-pecl-library-on-openbsd-1ng3>

## 简介

你好:)
这篇帖子是关于如何在我最喜欢的操作系统 [OpenBSD](https://www.openbsd.org/) 上安装 [Imagick (PECL 库)](https://pecl.php.net/package/imagick)[PECL](https://pecl.php.net/)库。

## 正文

令人高兴的是，提供了官方包:

```
#  pkg_add pecl73-imagick 
```

Enter fullscreen mode Exit fullscreen mode

结果是:

```
quirks-3.124 signed on 2019-04-15T12:10:16Z
pecl73-imagick-3.4.3p3: ok 
```

Enter fullscreen mode Exit fullscreen mode

另外，它的`.ini`文件是自动创建的:

```
$  ls /etc/php-7.3.sample/
... imagick.ini ... 
```

Enter fullscreen mode Exit fullscreen mode

创建一个符号链接以激活扩展:

```
#  ln -sf /etc/php-7.3.sample/imagick.ini /etc/php-7.3/ 
```

Enter fullscreen mode Exit fullscreen mode

最后，你要做的就是重启 [PHP-FPM](https://php-fpm.org/) 服务器:

```
#  rcctl restart php73_fpm 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

PECL 是指惠普扩展社区图书馆。
Imagick 是一款功能强大的开源图像操作器 [ImageMagick](https://www.imagemagick.org/) 的接口:

> Imagick 是一个本地 php 扩展，使用 ImageMagick API 创建和修改图像。此扩展需要 ImageMagick 版本 6.5.3-10+和 PHP 5.4.0+。

感谢您的阅读。
快乐计算:)