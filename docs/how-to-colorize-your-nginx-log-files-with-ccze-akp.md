# 如何用 CCZE 给你的 NGINX 日志文件着色

> 原文：<https://dev.to/robbinespu/how-to-colorize-your-nginx-log-files-with-ccze-akp>

如果你是 devops 或 NGINX 后端管理员，你会注意到，与 Httpd / Apache 有大量脚本和工具来着色日志相比，将 NGINX 访问或错误日志文件读取/转换为彩色是有帮助和可读的工具是有限的。

我最后一次配置 NGINX 是在 4 年前，昨天我用 NGINX 而不是 Httpd / Apache 为我的客户端设置了 web 服务器，7 分钟后，端口 80 对公众开放，我注意到我的客户端 VPS 机器很慢，所以我假设可能有一些大型机器人出于某种原因正在扫描这个 web 服务器。

我用我最喜欢的`less- R`命令行打开 NGINX log，我感觉很糟糕，很沮丧。你知道为什么吗？因为我参与了许多编程框架和工具，它们为我提供了 ANSI 彩色日志。

如果你和我一样，那么我建议你安装`ccze`！这里是来自 Fedora 库的一些当前信息

```
[rnm@robbinespu ~] $ sudo dnf info ccze
Last metadata expiration check: 0:00:13 ago on Thu 22 Aug 2019 11:55:41 AM +08.
Available Packages
Name : ccze
Version : 0.2.1
Release : 22.fc30
Architecture : x86_64
Size : 81 k
Source : ccze-0.2.1-22.fc30.src.rpm
Repository : fedora
Summary : A robust log colorizer
URL : http://bonehunter.rulez.org/CCZE.html
License : GPLv2+
Description : CCZE is a roboust and modular log colorizer, with plugins for apm,
             : exim, fetchmail, httpd, postfix, procmail, squid, syslog, ulogd,
             : vsftpd, xferlog and more. 
```

就像它说的，这是一个健壮的模块化日志着色程序，带有很少的插件。它可以在 Fedora、Debian、Ubuntu、Centos、Opensuse 和其他发行版库中找到。

只需安装:

```
$ sudo dnf install ccze #for Red Hat/CentOS/Fedora based
$ sudo apt install ccze #for Debian/Ubuntu based 
```

要使用它，你需要打开你的文件阅读器并连接到 ccze。例如:

```
$ sudo less -R /var/log/nginx/access.log | ccze -A | less -R 
```

[![NGINX access log with ccze](img/fde500ad4a45dba156600af9203793aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yU6QYJ5s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/hIQOFAc.png%23center)

你可以查看帮助以获得更多如何操作和使用 ccze
的选项

```
$ ccze --help
Usage: ccze [OPTION...]
ccze -- cheer up 'yer logs.

  -a, --argument=PLUGIN=ARGS... Add ARGUMENTS to PLUGIN
  -A, --raw-ansi Generate raw ANSI output
  -c, --color=KEY=COLOR,... Set the color of KEY to COLOR
  -C, --convert-date Convert UNIX timestamps to readable format
  -F, --rcfile=FILE Read configuration from FILE
  -h, --html Generate HTML output
  -l, --list-plugins List available plugins
  -m, --mode=MODE Change the output mode
                             (Available modes are curses, ansi and html.)
  -o, --options=OPTIONS... Toggle some options
                             (such as scroll, wordcolor and lookups,
                             transparent, or cssfile)
  -p, --plugin=PLUGIN Load PLUGIN
  -r, --remove-facility remove syslog-ng's facility from start of the
                             lines
  -?, --help Give this help list
      --usage Give a short usage message
  -V, --version Print program version

Mandatory or optional arguments to long options are also mandatory or optional
for any corresponding short options.

Report bugs to <algernon@bonehunter.rulez.org>. 
```

干杯，玩得开心！