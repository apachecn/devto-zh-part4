# 在 Docker 中用 MSMTP 替换 SSMTP

> 原文：<https://dev.to/owendavies/replacing-ssmtp-with-msmtp-in-docker-1kmm>

试图为我已经运行了几个月的 Docker 文件构建一个新的映像，却遇到了这个错误:

```
Package ssmtp is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'ssmtp' has no installation candidate 
```

Enter fullscreen mode Exit fullscreen mode

看起来 [sSMTP](https://wiki.debian.org/sSMTP) 不再被维护，并且自 2019 年 3 月 19 日起成为孤儿。 [MSMTP](https://wiki.debian.org/msmtp) 是建议的替代品。所以让我们开始替换吧。

# 计划

1.  在我们的 Docker 映像中安装 mSMTP
2.  将 mSMTP 配置到我们的 SMTP 服务器(在本例中是 Office 365)
3.  告诉 PHP 使用 mSMTP 而不是 sSMTP 发送电子邮件
4.  发送测试电子邮件
5.  最终来源

## 1。在我们的 Docker 映像中安装 mSMTP

我们将从 *php:7.30-apache* docker 图像
开始

```
FROM php:7.3-apache 
```

Enter fullscreen mode Exit fullscreen mode

让我们用 apt-get 安装 msmtp，然后整理:

```
apt-get update && apt-get install ssmtp -y && \
rm -rf /var/lib/apt/lists/* 
```

Enter fullscreen mode Exit fullscreen mode

## 2。将 mSMTP 配置到我们的 SMTP 服务器(在本例中是 Office 365)

我们需要为 msmtp 设置一个配置文件，以获得 smtp 细节。msmtp 配置位置在/etc/中

让我们创建一个名为 msmtprc 的新文件，并添加以下配置:

```
account default
host smtp.office365.com
port 587
tls on
tls_starttls on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
tls_certcheck on
auth on
user user@domain.com
password "YourAwesomeStr0ngP4zzw0rd"
from "user@domain.com"
logfile /var/log/msmtp.log 
```

Enter fullscreen mode Exit fullscreen mode

这是针对 Office 365 的，把 smtp 细节改成 gmail 或者自己的 smtp 服务器。

### 将配置复制到我们的 Docker 映像中

```
COPY msmtprc /etc/msmtprc 
```

Enter fullscreen mode Exit fullscreen mode

### 使 msmtprc 配置文件对其所有者可读/写

```
RUN chmod 600 /etc/msmtprc 
```

Enter fullscreen mode Exit fullscreen mode

## 3。告诉 PHP 使用 mSMTP 而不是 sSMTP 发送电子邮件

现在我们有了 mSMTP 设置，我们需要告诉 PHP 使用它。

### 您可以从我们刚刚创建的 docker 映像中获得默认 php.ini 文件的副本。它位于*/usr/local/etc/PHP/PHP . ini-production*

```
docker cp <CONTAINER_ID>:/usr/local/etc/php/php.ini.production . 
```

Enter fullscreen mode Exit fullscreen mode

### 打开 php.ini 文件，对 sendmail_path 变量进行如下修改:

```
sendmail_path = /usr/bin/msmtp -t 
```

Enter fullscreen mode Exit fullscreen mode

### 将您的 php.ini 文件复制到 Docker image php config 位置:

```
COPY php.ini $PHP_INI_DIR/php.ini 
```

Enter fullscreen mode Exit fullscreen mode

## 4。发送测试电子邮件

有了所有这些设置，我们现在应该能够发送电子邮件了。

### 1。建立 Docker 形象

```
docker build . -t php-msmtp-setup:latest 
```

Enter fullscreen mode Exit fullscreen mode

### 2。运行容器

```
docker run -d -p 80:80/tcp php-msmtp-setup:latest 
```

Enter fullscreen mode Exit fullscreen mode

### 3。登录到容器

#### 运行下面的命令来查找我们新容器的容器 ID

```
docker container ls 
```

Enter fullscreen mode Exit fullscreen mode

#### 运行以下命令登录到容器

```
docker exec -ti <CONTAINER_ID> /bin/bash 
```

Enter fullscreen mode Exit fullscreen mode

### 4。使用 msmtp 直接发送电子邮件

```
echo -e "Subject: Test Mail\r\n\r\nThis is a test mail, let me know if this works" |msmtp --debug --from from@yourdomain.com -t to@someone.com 
```

Enter fullscreen mode Exit fullscreen mode

### 5。用 php 发送电子邮件

```
php -r "mail('to@domain.com','Test Mail from PHP', 'This is a test mail from PHP, let me know if this works');" 
```

Enter fullscreen mode Exit fullscreen mode

## 5。最终来源

### 我们的*码头工人的*文件

```
FROM php:7.3-apache

RUN apt-get update && apt-get install msmtp -y && \
    rm -rf /var/lib/apt/lists/*

COPY msmtprc /etc/msmtprc
RUN chmod 600 /etc/msmtprc

COPY php.ini $PHP_INI_DIR/php.ini

EXPOSE 80 
```

Enter fullscreen mode Exit fullscreen mode

### 对我们的 *php.ini* 文件的修改

```
sendmail_path = /usr/bin/msmtp -t 
```

Enter fullscreen mode Exit fullscreen mode

### 我们的 *msmtprc* 文件

```
account default
host smtp.office365.com
port 587
tls on
tls_starttls on
tls_trust_file /etc/ssl/certs/ca-certificates.crt
tls_certcheck on
auth on
user user@domain.com
password "YourAwesomeStr0ngP4zzw0rd"
from "user@domain.com"
logfile /var/log/msmtp.log 
```

Enter fullscreen mode Exit fullscreen mode