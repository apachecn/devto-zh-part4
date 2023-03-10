# 在 CentOS7 上设置 Let ' s Encrypt(Amazon Linux 2)

> 原文：<https://dev.to/katzueno/setting-up-let-s-encrypt-on-amazon-linux-2-57l7>

这篇笔记解释了如何在 Amazon Linux 2 上安装 Let's Encrypt(运行一个
PHP+MySQL CMS，concrete5)。

我在亚马逊 Linux 2 上测试过。但它可能在任何 CentOS7 和 RHLE7 上都能工作。

# 第 0 步:初始设置

运行我的 concrete5 Ansible 脚本来设置基本的 web 服务器。
[https://github.com/concrete5cojp/ansible-c5-ma](https://github.com/concrete5cojp/ansible-c5-ma)

这个脚本将安装所有必要的 repo 和中间件来运行 PHP web 应用程序。

运筹学

安装 epel repo。

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm 
```

Enter fullscreen mode Exit fullscreen mode

然后，确保您已经完成了 80 端口的配置和可用性。

# 使用自动配置

## 步骤 1:安装确定机器人

```
$ sudo yum install certbot python2-certbot-nginx # Nginx server
$ sudo yum install certbot python2-certbot-apache # Apache server 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:安装加密证书

```
sudo certbot 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:设置 cron 自动续订

```
$ sudo vi /etc/crontab
# Let's Encrypt Renewal - Nginx
39 11,23  *  *  * root /usr/bin/certbot renew --no-self-upgrade --renew-hook "systemctl restart nginx"
# Let's Encrypt Renewal - Apache
39 11,23  *  *  * root /usr/bin/certbot renew --no-self-upgrade --renew-hook "service httpd restart" 
```

Enter fullscreen mode Exit fullscreen mode

# 手动方法

## 为该域颁发证书

```
$ sudo certbot certonly \
     --manual \
     --manual-public-ip-logging-ok \
     -d EXAMPLE.com \
     -d *.EXAMPLE.com \
     -m nospam@EXAMPLE.com \
     --agree-tos \
     --debug 
```

Enter fullscreen mode Exit fullscreen mode

获取密钥的完整路径

## 设置 web 服务器配置

### Nginx 配置

```
 ssl_certificate         /etc/letsencrypt/live/EXAMPLE.com/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/EXAMPLE.com/privkey.pem;
    include                 /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam             /etc/letsencrypt/ssl-dhparams.pem; 
```

Enter fullscreen mode Exit fullscreen mode

### 阿帕奇配置

```
SSLCertificateFile /etc/letsencrypt/live/DOMAIN/cert.pem
SSLCertificateKeyFile /etc/letsencrypt/live/DOMAIN/privkey.pem
SSLCertificateChainFile /etc/letsencrypt/live/DOMAIN/chain.pem
SSLCACertificateFile /etc/letsencrypt/live/DOMAIN/fullchain.pem 
```

Enter fullscreen mode Exit fullscreen mode

## Cron 设置

```
$ sudo vi /etc/crontab
# Let's Encrypt Renewal
39 11,23  *  *  * root /usr/bin/certbot renew --no-self-upgrade --renew-hook "service nginx reload -s" 
```

Enter fullscreen mode Exit fullscreen mode

# 当你失败时

## 清除所有咱们加密的设置

```
sudo rm -R /opt/eff.org/certbot 
```

Enter fullscreen mode Exit fullscreen mode

## 确保在默认服务器上设置 SSL(用于带缓存的动态站点)

即使您只需要 SSL 访问额外的虚拟主机服务器，您也可能希望在您的默认服务器上设置 SSL 设置，以便当人们试图通过 IP 访问您的服务器时，它将返回空白请求。

如果你使用 WordPress 或 Concrete CMS 这样的 CMS，这是防止缓存中毒的重要策略。

如果你在 Nginx 错误日志中发现以下错误，并且一直无法访问 SSL，那么你没有在 Nginx 的默认服务器配置中设置正确的 443 配置。

```
no "ssl_certificate" is defined in server listening on SSL port while SSL handshaking, client: ***.***.***.***, server: 0.0.0.0:443 
```

Enter fullscreen mode Exit fullscreen mode

```
server {
    listen       80 default_server;
    listen       [::]:80;
    listen       443 ssl http2 default_server;
    listen       [::]:443 ssl http2;

    server_name  EXAMPLE.com;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains";

    charset      utf-8;

    access_log   /var/log/nginx/dummy_access.log main;
    error_log    /var/log/nginx/dummy_error.log warn;

    root         /var/www/html;

    ssl_certificate         /etc/letsencrypt/live/EXAMPLE.com/fullchain.pem;
    ssl_certificate_key     /etc/letsencrypt/live/EXAMPLE.com/privkey.pem;
    include                 /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam             /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        index  index.html;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 如何删除未使用的和/或旧的证书

```
# Show which certificates are installed & get the certificate names to delete
$ sudo certbot certificates

# Delete the certificate
$ sudo certbot delete --cert-name example.com-0001 
```

Enter fullscreen mode Exit fullscreen mode

# 引用

这是一篇好文章。
[http://nopipi.hatenablog.com/entry/2019/01/08/013654](http://nopipi.hatenablog.com/entry/2019/01/08/013654)

[https://higherhope.net/?p=2710](https://higherhope.net/?p=2710)

[https://qiita.com/HeRo/items/f9eb8d8a08d4d5b63ee9](https://qiita.com/HeRo/items/f9eb8d8a08d4d5b63ee9)

[https://qiita . com/mysterious Macy/items/f 2636447 C1 ff 390 ce 21](https://qiita.com/MysteriousMonky/items/f26316447c1ff390ce21)

[https://worklog.be/archives/3352](https://worklog.be/archives/3352)