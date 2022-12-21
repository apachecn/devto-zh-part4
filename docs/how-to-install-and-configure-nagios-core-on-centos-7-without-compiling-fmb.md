# 如何在 CentOS 7 上安装和配置 Nagios Core(无需编译)

> 原文：<https://dev.to/kavishgour/how-to-install-and-configure-nagios-core-on-centos-7-without-compiling-fmb>

Nagios Core 是一款开源工具，用于基础设施监控。Nagios 依靠插件来监控一切——数据库、操作系统、应用程序、网络设备、协议等等！

### 安装

更新您的 repo 并安装 nagios:

```
yum update -y ; yum install nagios -y 
```

Enter fullscreen mode Exit fullscreen mode

Nagios 的 web 界面依赖于 Apache web 服务器。前面的命令将安装 apache。启动并启用服务:

```
systemctl enable httpd.service
systemctl start httpd.service 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们打开 80 号端口:

```
firewall-cmd --zone=public --add-port=80/tcp
firewall-cmd --zone=public --add-port=80/tcp --permanent 
```

Enter fullscreen mode Exit fullscreen mode

安装插件和 nrpe:

```
yum install nagios-plugins nrpe nagios-plugins-nrpe
systemctl start nrpe
systemctl enable nrpe 
```

Enter fullscreen mode Exit fullscreen mode

**注意:** NRPE，允许您在其他 Linux/Unix 机器上远程执行 nagios 插件，并通过 Nagios 接收输出。

我们还需要一些 nagios 默认需要的基本插件:

```
for i in users uptime ssh ping procs load http swap disk; do yum install nagios-plugins-$i -y; done 
```

Enter fullscreen mode Exit fullscreen mode

启动并启用 nrpe:

```
systemctl start nrpe
systemctl enable nrpe 
```

Enter fullscreen mode Exit fullscreen mode

除非我们在/var/www/html 中创建一个' index.html ',否则 check_http 插件不会工作。让我们创建一个:

```
echo "<h1> Apache is UP and RUNNING. </h1>" > /var/www/html/index.html 
```

Enter fullscreen mode Exit fullscreen mode

点击[此处](http://nagios-plugins.org/doc/man/index.html)了解更多关于插件的细节。

现在，为了让 apache 能够访问 web 界面，我们必须给“nagiosadmin”用户一个密码。默认情况下会创建用户。如果你愿意，你可以改变它。为此，让我们运行“htpasswd”并输入您的密码:

```
htpasswd  /etc/nagios/passwd nagiosadmin 
```

Enter fullscreen mode Exit fullscreen mode

一切就绪。让我们启动并启用 nagios 服务:

```
systemctl start nagios.service
systemctl enable nagios.service 
```

Enter fullscreen mode Exit fullscreen mode

进入' [http://localhost/nagios](http://localhost/nagios) 或者' http://{ip-address}/nagios '，输入你的凭证，就这样。

**注意:**在服务选项卡中，几个插件将打印状态关键。给 nagios 一两分钟的时间来阅读它的配置并使用它所需的插件。

您也可以在命令行上使用“nagiostats”实用程序:

```
[root@centos \~]# nagiostats | grep '^Services Ok'
Services Ok/Warn/Unk/Crit:              8 / 0 / 0 / 0 
```

Enter fullscreen mode Exit fullscreen mode

只是为了好玩:我写了一个 [python 脚本](https://github.com/kavishgr/My-Python--Bash-Scripts/blob/master/NagiosInstall.py)来自动化安装。