# 如何在全新安装的 Debian 10 上部署 Laravel 应用程序

> 原文：<https://dev.to/aarondunphy/how-to-deploy-a-laravel-application-on-a-fresh-install-of-debian-10-do9>

## 创建一个新用户，这样我们就不用 root 了

```
adduser admin
usermod -aG sudo admin
su admin 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 Apache

```
sudo apt update
sudo apt install apache2 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 PHP7.3

添加 PHP7.3 库

```
sudo apt install lsb-release apt-transport-https ca-certificates
sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
sudo echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php7.3.list 
```

Enter fullscreen mode Exit fullscreen mode

然后安装

```
sudo apt update
sudo apt install php7.3 
```

Enter fullscreen mode Exit fullscreen mode

验证 PHP 安装正确

```
php -v 
```

Enter fullscreen mode Exit fullscreen mode

安装常用的 PHP 扩展

```
sudo apt install php7.3-cli php7.3-fpm php7.3-json php7.3-pdo php7.3-mysql php7.3-zip php7.3-gd  php7.3-mbstring php7.3-curl php7.3-xml php7.3-bcmath php7.3-json php7.3-zip 
```

Enter fullscreen mode Exit fullscreen mode

如果需要其他扩展，可以用
安装

```
sudo apt install php7.3-<entension-name> 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 MariaDB

```
sudo apt install mariadb-server mariadb-client 
```

Enter fullscreen mode Exit fullscreen mode

然后保护数据库，设置强密码，移除匿名用户，禁用 root 远程登录，并移除测试数据库

```
sudo mysql_secure_installation 
```

Enter fullscreen mode Exit fullscreen mode

您现在可以连接到 MariaDB

```
mysql -u root -p 
```

Enter fullscreen mode Exit fullscreen mode

## 设置虚拟主机

创建应用程序所在的目录(将“example.com”替换为您的域名)

```
sudo mkdir -p /var/www/example.com/public 
```

Enter fullscreen mode Exit fullscreen mode

创建一个虚拟保存页面，以便我们可以测试虚拟主机配置是否正常工作

```
sudo nano /var/www/example.com/public/index.html 
```

Enter fullscreen mode Exit fullscreen mode

然后插入并保存

```
<html>
    <head>
        Example.com!
    </head>
    <body>
        <h1>Hello World</h1>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

创建一个基于名称的虚拟主机配置文件，以允许 Apache 为其提供服务

```
sudo nano /etc/apache2/sites-available/example.com.conf 
```

Enter fullscreen mode Exit fullscreen mode

然后插入并保存(用你自己的域名替换 example.com 的所有引用)

```
<VirtualHost *:80>

ServerAdmin example@me.com
ServerName example.com
ServerAlias www.example.com
DocumentRoot /var/www/example.com/public

<Directory /var/www/example.com>
    AllowOverride all
    Require all granted
</Directory>

ErrorLog ${APACHE_LOG_DIR}/example.com_error.log
CustomLog ${APACHE_LOG_DIR}/example.com_access.log combined

</VirtualHost> 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们用 Apache
启用配置文件

```
sudo a2ensite example.com 
```

Enter fullscreen mode Exit fullscreen mode

重启 Apache，让配置文件进入

```
sudo service apache2 restart 
```

Enter fullscreen mode Exit fullscreen mode

只要你的域名指向你的服务器的 IP 地址，你现在应该可以看到我们之前添加的虚拟 html 文件，例如-[http://example.com](http://example.com)

## 用 Git 克隆你的 Laravel 应用

Install Git

```
sudo apt install git 
```

Enter fullscreen mode Exit fullscreen mode

为您的服务器生成一个 ssh 密钥，以便与 GitHub
通信

```
ssh-keygen 
```

Enter fullscreen mode Exit fullscreen mode

复制你的服务器的 SSH 公钥，并将其添加到你的 GitHub 的存储库部署密钥中，可以在存储库设置下找到(我建议不要启用写访问)

```
cat ~/.ssh/id_rsa.pub 
```

Enter fullscreen mode Exit fullscreen mode

将目录更改为您的域

```
cd /var/www/example.com 
```

Enter fullscreen mode Exit fullscreen mode

从上面的一个说明中删除公共目录，使其完全为空

```
sudo rm -rf public/ 
```

Enter fullscreen mode Exit fullscreen mode

更新目录，使其归管理员用户所有，但可由 apache 访问

```
sudo chown -R server-user:www-data /var/www/example.com/ 
```

Enter fullscreen mode Exit fullscreen mode

使用 ssh 克隆您的存储库(您可以从 GitHub 上的存储库中获得这个)

```
git clone git@github.com:<YourUserName>/<YourRepository>.git . 
```

Enter fullscreen mode Exit fullscreen mode

克隆后再次更新文件/目录所有权，并确保 apache 可以读取/写入存储和缓存。

```
sudo chown -R server-user:www-data /var/www/example.com/
find /var/www/example.com/ -type f -exec chmod 664 {} \;    
find /var/www/example.com/ -type d -exec chmod 775 {} \;
chgrp -R www-data storage bootstrap/cache
chmod -R ug+rwx storage bootstrap/cache 
```

Enter fullscreen mode Exit fullscreen mode

## 安装作曲家

跟随他们的[公文](https://getcomposer.org/download/)

```
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');" 
```

Enter fullscreen mode Exit fullscreen mode

然后移动它，以便可以全局访问

```
sudo mv composer.phar /usr/local/bin/composer 
```

Enter fullscreen mode Exit fullscreen mode

从应用程序文件夹
中安装依赖项

```
cd /var/www/example.com
composer install 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 npm

```
sudo apt install npm 
```

Enter fullscreen mode Exit fullscreen mode

从应用程序文件夹
中安装依赖项并运行任务

```
cd /var/www/example.com
npm install npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

## 创建数据库

连接到 MariaDB

```
mysql -u root -p 
```

Enter fullscreen mode Exit fullscreen mode

创建数据库

```
create database my_db_name; 
```

Enter fullscreen mode Exit fullscreen mode

部署愉快！