# 用 Xdebug & VSCode 在 vagger 中调试 PHP 应用程序

> 原文：<https://dev.to/vuong/debugging-php-application-in-vagrant-by-xdebug-vscode-3lol>

## 备注

*   按照所有步骤设置 VSCode。
*   按照 1、2、5 设置服务器(任何调试器工具都需要)。

## 必需品

*   PHP >=5.3 英寸 CentOS 6.x
*   你的 PHP 项目正在运行
*   在 CentOS 6 上运行的本教程中的流浪者实例
*   本教程中的主机是 Windows 10
*   当然是 VSCode！

## 在流浪汉中安装&配置 Xdebug

*   通过命令访问根权限

```
sudo -i 
```

Enter fullscreen mode Exit fullscreen mode

*   在流浪者中运行以下命令

```
rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
yum install --enablerepo=remi --enablerepo=remi-php56 php-pear php-devel -y
pecl install xdebug
service httpd restart 
```

Enter fullscreen mode Exit fullscreen mode

*   在`/etc/php.ini`文件的末尾添加 XDebug 的配置

```
zend_extension=/usr/lib64/php/modules/xdebug.so
[XDebug]
xdebug.remote_enable=1
xdebug.remote_autostart=1
; Host machine of Vagrant instance xdebug.remote_host=10.0.2.2
xdebug.remote_connect_back=1

; Another xdebug configuration (in php.ini) set to profiling source code (optional). [Read more](https://xdebug.org/docs/profiler). 
; xdebug.profiler_enable_trigger=1
; xdebug.profiler_output_dir=/vagrant/profiler 
```

Enter fullscreen mode Exit fullscreen mode

*   打开`.vscode/launch.json`或(VSCode 中调试模式的按钮设置)，配置如下:

```
{  "version":  "0.2.0",  "configurations":  [  {  "name":  "Listen for XDebug",  "type":  "php",  "request":  "launch",  "serverSourceRoot":  "/var/www/your_website",  "localSourceRoot":  "${workspaceRoot}",  "port":  9000,  "log":  true  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

## 安装 PHP Debug for Visual Studio 代码

*   以 PHP Debug 的名称搜索扩展，安装它

## 防火墙的问题

操作系统防火墙可能有问题。

求解:在 Windows 中，进入`Control Panel\System and Security\Windows Firewall` > >选择`Allow an app or feature through Windows Firewall` >勾选两个选项(私/公网)获取 VSCode(或 PhpStorm)。

## 相关主题

[为 PHP 开发配置 Visual Studio 代码](https://blogs.msdn.microsoft.com/nicktrog/2016/02/11/configuring-visual-studio-code-for-php-development/)