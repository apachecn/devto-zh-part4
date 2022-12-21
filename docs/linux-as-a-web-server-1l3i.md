# 作为 web 服务器的 Linux

> 原文：<https://dev.to/tayyebi/linux-as-a-web-server-1l3i>

# 常见问题

> 这个帖子是给谁的？
> 
> > 那些需要一些分步说明的人，只需简单地配置一个 web 服务器，在一个服务器上托管多个网站。
> 
> 这就是 web 服务器配置的全部内容吗？
> 
> > 不，这只是给新手程序员和想更快赚钱的文字印刷者的镇定剂。
> 
> 糟蹋它！
> 
> > 最后，您将安装 Apache、MySQL、BIND、PHP 等...并将其配置为托管多个域，如`a.b.com`和`d.e.com`。
> 
> 在哪里学习万物理论？
> 
> > 用以下关键词谷歌一下就可以了: [LPIC](https://www.lpi.org/)

# 连接你的服务器！

使用`ssh <IP>`或`ssh <Domain>`命令连接到您的服务器。【example.com】(例如- >宋承宪)

# 安装 Apache

什么是阿帕奇？Apache 向全世界提供您的内容。它也可以帮助你用 PHP 创建动态网站，Wordpress 也是 PHP 的一部分。

要安装 Apache

1- `sudo yum install httpd mod_ssl`

2- `sudo /usr/sbin/apachectl start`

如果出现消息:`Starting httpd: httpd: Could not reliably determine the server's fully qualified domain name, using 127.0.0.1 for ServerName`，使用`vim`或您希望的任何编辑器编辑配置文件(步骤 3)。

3- `sudo vim /etc/httpd/conf/httpd.conf`

并将下面一行从注释中删除:

4- `#ServerName www.example.com:80` = > `ServerName MyServerName`(其中 *MyServerName* 是自己的服务器名！)

允许 Apache 进入防火墙。如您所知，Apache 默认监听端口 HTTP 80。

5.1- `sudo iptables -I INPUT -p tcp --dport 80 -j ACCEPT`

5.2- `sudo service iptables save`

重新启动 Apache:

6- `sudo /usr/sbin/apachectl restart`

测试您的安装:

7- `curl 127.0.0.1`

如你所知，允许 Apache 访问你的代码是非常重要的！

8- `sudo chcon -t httpd_sys_rw_content_t /var/www/html/mysite -R`

# 了解更多关于防火墙的信息

[https://www . digital ocean . com/community/tutorials/how-to-set-a-firewall-using-firewalld-on-centos-7](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-using-firewalld-on-centos-7)

# 安装 PHP

安装并启用 EPEL 系统信息库:

1- `sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm`

安装并启用 Remi 库

2- `sudo yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm`

安装 *yum 配置管理器*

3- `yum install yum-utils`

安装 PHP 模块，程序员用它来减轻他们的头痛

4- `yum install php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo`

测试 PHP 的命令行界面(CLI)

5- `php -v`

# 想创建一个二级系统管理员用户吗？(苏多尔盖伊)

创建用户

1- ‍‍‍ `a‍dduser theusername`(你说*用户名*)

选择密码

2- `passwd theusername`(你说*用户名*)

就像学校一样，任何人都是一个群体。因此，您必须向`wheel`添加新用户，这是一个超级用户级别组:

3- `usermod -aG wheel theusername`

切换用户

4- `su - username`

# 禁用用户登录(也许是邮箱或 FTP 账号)

1-编辑`/etc/passwd`

2-将外壳更改为`/bin/nologin`

# 了解有关配置 FTP 服务器的更多信息:

[https://linuxize . com/post/how-to-setup-FTP-server-with-vsftpd-on-centos-7/](https://linuxize.com/post/how-to-setup-ftp-server-with-vsftpd-on-centos-7/)

# 嘿服务器！把一切都记在心里；甚至“清洁你的心灵”

编辑`~/.bashrc`

1- `vim ~/.bashrc`

2-将以下几行放在文件的末尾:

```
export HISTCONTROL=ignoredups:erasedups  
shopt -s histappend
export PROMPT_COMMAND="${PROMPT_COMMAND:+$PROMPT_COMMAND$'\n'}history -a; history -c; history -r"
export HISTTIMEFORMAT='%F %T ' 
```

3-测试它:`history`

# 如何安装 MySQL

安装`wget`，对于繁重的下载，它比`curl`更方便。

1- `yum install wget`

下载软件包:

2- `wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm`

添加包:

3- `sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm`

更新包管理器:

4- `yum update`

安装软件包:

5- `sudo yum install mysql-server`

启动守护程序:

6- `sudo systemctl start mysqld`

配置安装:

7- `sudo mysql_secure_installation`

测试安装:

8- `mysql -u root -p`(哪个*根*是根用户名)

创建一个 MySQL 用户:

9- mysql> `create user 'testuser'@'localhost' identified by 'mysecretpass';`(用户名为 *testuser* ，密码为*my cretpass*)

向用户授予权限:

10- mysql> `grant all on databasename.* to 'testuser' identified by 'mysecretpass';`(用数据库名替换*数据库名*

# 你叫什么名字？

如果`ifconfig`不起作用:

用途:`ip addr show`

# 更改*主机名*

配置文件位于`/etc/sysconfig/network`中。所以在编辑器中打开它，找到相关的行:

`HOSTNAME=myserver.domain.com`

您还可以在`/etc/hosts`文件中更改与您的服务器的主 IP 地址相关联的主机。

测试命令:`hostname`

小心地重新启动网络(您可能会失去与远程服务器的连接！):`/etc/init.d/network restart`

# 绑定 DNS(域名服务器:mycustomcomputername.com 背后的魔法)

您可以使用 DNS checkers 在线检查每个域的传播。例如->[https://dnschecker.org/](https://dnschecker.org/)T3】还有一种极客的方式:`nslookup domain.com`

安装绑定

1- `sudo yum install bind bind-utils -y`

配置 BIND(记住，您可以在这里定义您的区域，但是最好保持标准。

2- `sudo cat /etc/named.conf`

查找包含的文件，并按如下所述编辑文件:

3- `sudo vi /etc/named.rfc1912.zones`

在文件中定义您的区域，如下所示:

4-

```
zone "mydomain.ir" IN {
    type master;
    file "mydomain.ir.zone";
    allow-transfer { none; };
};

zone "mycustomer1domain.com" IN {
    type master;
    file "customers.zone";
    allow-transfer { none; };
};

zone "mycustomer2domain.com" IN {
    type master;
    file "customers.zone";
    allow-transfer { none; };
}; 
```

所以你必须在`/var/named`目录下创建你刚才提到的`customers.zone`和`mydomain.ir.zone`文件:

`sudo touch /var/named/customers.zone`5.1-
5.2-`sudo touch /var/named/mydomain.ir.zone`

然后根据需要编辑它们:

6-

```
$TTL 86400
@   IN  SOA     ns1.mydomain.com. root.mydomain.com. (
        2013042201  ;Serial
        3600        ;Refresh
        1800        ;Retry
        604800      ;Expire
        86400       ;Minimum TTL
)
; Specify our two nameservers
        IN  NS      ns1.mydomain.ir.
        IN  NS      ns2.mydomain.ir.
; Resolve nameserver hostnames to IP, replace with your two droplet IP addresses.
ns1     IN  A       SERVER_IP_HERE
ns2     IN  A       SERVER_IP_HERE

; Define hostname -> IP pairs which you wish to resolve
@       IN  A       SERVER_IP_HERE
www     IN  A       SERVER_IP_HERE 
```

那么任何决定托管在该服务器上的域，都必须在**域控制面板**中将`ns1.mydomain.ir`和`ns2.mydomain.ir`设置为它们的名称服务器。如果网站在 Apache 中被定义为虚拟主机，它将是可访问的。

# 虚拟主机

编辑您想要托管的每个网站的配置文件:`sudo vi /etc/httpd/conf/httpd.conf` *。* 

```
<VirtualHost *:80>
   DocumentRoot "/var/www/html/domainname"
   ServerName domainname
   ServerAlias www.domainname
   RedirectPermanent / https://domainname2
</VirtualHost> 
```

# 参照:

[https://twitter.com/MRezaTayyebi/status/1152794765164974080](https://twitter.com/MRezaTayyebi/status/1152794765164974080)
[https://support . rackspace . com/how-to/centos-6-Apache-and-PHP-install/](https://support.rackspace.com/how-to/centos-6-apache-and-php-install/)
[https://www.tecmint.com/install-php-7-in-centos-7/](https://www.tecmint.com/install-php-7-in-centos-7/)
[https://www . digital ocean . com/community/tutorials/how-to-create-a-sudo-user-on-centos-quick start](https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-centos-quickstart)[https://gist . github . com/tayyebi/5e 03 f83 dff 1](https://gist.github.com/tayyebi/5e03f83dff1b897a51e530748d74e872)