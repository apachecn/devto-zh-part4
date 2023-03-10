# 使用 Centos OS 向服务器添加 LAMP 堆栈

> 原文：<https://dev.to/jrohatiner/add-a-lamp-stack-to-a-server-57i6>

**以 root 用户身份登录**

ssh[root @ your . IP . address . here](mailto:root@your.ip.address.here)

**添加一个拥有 sudo 权限的新用户**

[root @ my server ~]# adduser admin
[root @ my server ~]# passwd admin
更改用户 admin 的密码。
新密码:
重新键入新密码:
passwd:所有认证令牌更新成功。
[root@myserver ~]# usermod -aG 车轮管理员

**从 root 变为新用户**

[root@myserver ~]# su - admin
。
[admin @ my server ~]$ sudo ls-la/root

管理员的[sudo]密码:

**更新 Centos**

[admin@myserver ~]$ sudo yum 更新

**添加 apache**

[admin @ my server ~]$ sudo yum install httpd

**启动 apache**

[admin @ my server ~]$ sudo system CTL start httpd . service

**添加 mariadb**

[admin @ my server ~]$ sudo yum install Maria db-server Maria db

**启动 mariadb**

[admin @ my server ~]$ sudo system CTL start Maria db

**保护 mariadb**

[admin @ my server ~]$ sudo MySQL _ secure _ installation

**系统会问这些问题。用 y 回答**

设置 root 密码？[Y/n] y
新密码:
重新输入新密码:
密码更新成功！
重新加载权限表..
...成功！

删除匿名用户？[Y/n] y
...成功！

不允许远程 root 登录？[Y/n] y
...成功！

是否删除测试数据库并访问它？[是/否]是

*   丢弃测试数据库......成功！
*   正在删除测试数据库的权限......成功！

现在重新加载权限表吗？[Y/n] y
...成功！
清理...

**让系统启动玛丽亚数据库**

[admin @ my server ~]$ sudo system CTL enable Maria db . service

**添加 php 和 mysql**

[admin@myserver ~]$ sudo yum 安装 php php-mysql

**重启 apache**

[admin @ my server ~]$ sudo system CTL restart httpd . service

**安装纳米编辑器**

[admin@myserver ~]$ sudo yum 安装 nano

添加 php 信息文件，这样你就可以检查你的安装了

[admin @ my server ~]$ sudo nano/var/www/html/info . PHP

**信息页面在[http://your IP address/info . PHP](http://youripaddress/info.php)T3】**

**就这样。转到您的 ip 地址，您应该有 apache 起始页**