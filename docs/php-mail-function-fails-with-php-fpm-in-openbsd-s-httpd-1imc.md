# PHP mail()函数在 OpenBSD 的 httpd 中因 PHP-FPM 而失败

> 原文：<https://dev.to/nabbisen/php-mail-function-fails-with-php-fpm-in-openbsd-s-httpd-1imc>

**封面图片由 [Kdsphotos](https://pixabay.com/users/Kdsphotos-3037209/) 原创，经过精心编辑。*

## 简介

我在 [OpenBSD](https://www.openbsd.org/) 中通过 [PHP-FPM](https://php-fpm.org/) 使用 [PHP](https://www.php.net/) [`mail()`](https://www.php.net/manual/en/function.mail.php) 函数时遇到了严重的麻烦。失败的原因很简单；`/bin/sh`在 [`chroot`之后的环境中并不存在](https://man.openbsd.org/httpd.conf#chroot)通过 PHP-FPM！

#### 我的案例

我已经使用了[联系表 7](https://contactform7.com) ，这是 [WordPress](https://wordpress.org/) 的插件之一，可以让访问者发送请求。
迁移服务器后，提交表单总是失败。我调试了一下，在浏览器控制台发现了错误信息，是这样的 URL:https://$ FQDN/WP-JSON/contact-form-7/v1/contact-forms/28/feedback。上面写着，" {"code":"rest_no_route "，" message":"URL [...]，“数据”:{“状态”:404}}”。

## 正文

#### 环境

*   操作系统:OpenBSD 6.5
*   Web: OpenBSD 的 [httpd](https://man.openbsd.org/httpd.8)
*   应用程序:PHP 7.3 通过 PHP-FPM

#### 背景

我的 php 代码是这样的:

```
$mail = mail($to, $subject, $message, $headers);
if ($mail) {
    ... 
```

Enter fullscreen mode Exit fullscreen mode

它失败了，我设法得到的所有错误对我来说都很难理解。
比如把 [`error_get_last()`](https://www.php.net/manual/en/function.error-get-last.php) 放在`mail()`后面只返回`1`。另外，我找不到任何有用的日志。

我反复检查了 PHP 核心配置文件，又名`/etc/php-7.3.ini`和`/etc/php-fpm.ini`，看起来是正确的。
然后我迷路了...

经过一段时间的挣扎，我找到了这个的 Joel Carnat 的文章，并感谢它得到了解决方案！

在 OpenBSD 的 PHP-FPM 以及 httpd 中，`chroot`默认为在`/etc/php-fpm.conf`中定义的`/var/www`。
在这种情况下，为了执行 PHP `mail()`功能，需要可执行文件`/var/www/bin/sh`。

#### 如何解决

我的解决方案只是将`/bin/sh`复制到`chroot` :
之后的环境中

```
#  cp -p /bin/sh /var/www/bin/ 
```

Enter fullscreen mode Exit fullscreen mode

做完后，我用浏览器打开网站，然后一封邮件成功发送:)

## 结论

OpenBSD 的 httpd 和 PHP-FPM 使用`chroot`，默认为`/var/www`。
因此，作为开发 app 的幕后推手，`/var/www`下的环境值得关注。

感谢您的阅读。
快乐计算。