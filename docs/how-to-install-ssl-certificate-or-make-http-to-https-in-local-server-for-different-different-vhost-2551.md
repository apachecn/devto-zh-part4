# 如何在本地服务器上为不同的 vhost 安装 ssl 证书(或使 http 成为 https)？

> 原文：<https://dev.to/kisorniru/how-to-install-ssl-certificate-or-make-http-to-https-in-local-server-for-different-different-vhost-2551>

大多数时候，我们在本地服务器上创建虚拟主机来运行我们的开发项目，这在我们的行业中是非常普遍的做法。当我们安装 PHP，Apache，MySql 来创建我们的本地服务器时，大多数情况下我们忘记了 ssl。简而言之，出于开发目的，我们不关心它。但是，有时这种无知会扼杀你的一天，就像你想通过浏览器访问网络摄像头，而你正在尝试使用 Chrome！电子工程师...如果你不知道如何把它变成你自己的本地主机，你可能会对自己生气！

在这里，我将试着做一个演示。我会做一个 vhost，但是按照这个方法，你可以想做多少就做多少。`I should mention here that I tested it for Apache not sure about nginx or others.`

### 我们的目标

*   [http://localhost . local](http://localhost.local)T4】=[https://localhost . local](https://localhost.local)

### 我们的系统配置

*   ubuntu 版本> = 14.04
*   php 版本> = 5.6。*

### 我们开始吧

*   首先在你喜欢的位置创建一个文件夹，对我来说是`/home/siddique/ssl`。`ssl`是我新创建的文件夹，我将在其中存储所有证书。你可以把它存在你电脑的任何地方，我的电脑在`/home/siddique`。
*   将文件夹设置为 read+write+executable `sudo chmod -R 777 /home/siddique/ssl`,这样当我们运行创建证书命令时，它就可以很容易地做到这一点。
*   现在，我们开始吧

```
$ sudo openssl req -x509 -days 365 -newkey rsa:2048 -keyout /home/siddique/ssl/localhost.key -out /home/siddique/ssl/localhost.crt
  Enter PEM pass phrase: 123456 [type your own password]
  Verifying - Enter PEM pass phrase: 123456 [retype your own password]
  Country Name []: BD [type your own country code]
  State Name []: Dhaka [type your own state]
  Locality Name []: Bangladeshi [type your own locality]
  Organization Name []: company-name [type your own company]
  Common Name []: localhost [type your own common name]
  Email Address []: admin@localhost.com [type your own email] 
```

*   现在打开你的虚拟主机

```
$ sudo gedit /etc/apache2/sites-available/localhost.conf 
```

*   和编辑

```
<VirtualHost *:443>

    ServerName localhost.local
    ServerAdmin admin@localhost.com

    ServerAlias www.localhost.local
    DocumentRoot /var/www/html/localhost.local

    SSLEngine on
    SSLCertificateFile "/home/siddique/ssl/localhost.crt"
    SSLCertificateKeyFile "/home/siddique/ssl/localhost.key"

    <Directory /var/www/html/localhost.local/>
        Options Indexes FollowSymLinks MultiViews
        AllowOverride All
        Order allow,deny
        allow from all
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/localhost_error.log
    CustomLog ${APACHE_LOG_DIR}/localhost_access.log combined

</VirtualHost> 
```

*   虚拟主机端口必须是`443`，这是针对 ssl 端口的。你必须确认你的虚拟主机中的代码

```
 SSLEngine on
   SSLCertificateFile "/home/siddique/ssl/localhost.crt"
   SSLCertificateKeyFile "/home/siddique/ssl/localhost.key" 
```

*   现在通过以下方式启用站点

```
$ sudo a2ensite /etc/apache2/sites-available/localhost.conf 
```

*   检查您的 apache ssl 模块是否正在您的本地服务器上运行，如果没有，那么为您的

```
$ sudo a2enmod ssl 
```

*   快好了，还有一件事要做。现在重启你的服务器。当你重启你的服务器时，你必须输入 ssl 密码和你的 sudo 密码。ssl 密码将与您之前设置的密码相同。

```
$ sudo service apache2 restart
  Enter PEM pass phrase: 123456 [type your own password] 
```

### 搞定！

*   现在，打开您的浏览器并浏览`https://localhost.local`
*   首次访问时，您可能需要在浏览器中检查`Proceed to localhost.local (unsafe)`。

### 就这么定了，没有更多的说话，他他...再见

### Github

*   [github:https-SSL-Apache-vhost](https://github.com/kisorniru/https-ssl-virtual-host-apache)