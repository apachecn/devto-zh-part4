# 在 Linux 虚拟机中托管 ASP.NET 核心站点的清单

> 原文：<https://dev.to/bharatdwarkani/checklist-for-hosting-asp-net-core-site-in-linux-vm-39le>

我列出了一些参考项目，这些项目将帮助您在生产环境的全新虚拟机中从头开始部署您的 ASP.NET 核心站点。

*   安装和设置 **Supervisor** -这是一个在服务器重启时帮助你的站点自动启动的工具，否则，在服务器重启时，你可能需要手动启动站点。
*   安装和设置 **Nginx** -用于反向代理到您的。网络应用
*   安装 dot net Core[https://dot net . Microsoft . com/download/Linux-package-manager/Ubuntu 16-04/SDK-current](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/sdk-current)
*   获得一个**域名**——可以从 GoDaddy 或者任何其他厂商那里获得。
*   设置和安装**让我们加密** -以获得免费的 SSL 认证，或者如果你有自己购买的证书，你也可以使用它。
*   **启用防火墙**以允许端口 80、443——或基于您的要求的任何其他端口。
*   在您的 Ngnix 配置中启用 **https 重定向**
*   托管后，确保您已经为站点启用了基本的**安全头**-可以使用安全头测试站点-【https://securityheaders.com/ T2】
*   如果你的网站需要的话，可以测试你的网站的搜索引擎优化功能

**参考文献**

*   服务器设置
    你可以花 5-10 美元买到一台便宜的数字海洋服务器，这足够运行一个简单的网站了。
    这个网站运行在同一个-【https://sharetechlinks.com T4】

*   **启用远程桌面-XRDP**
    T3】https://website for students . com/connect-to-Ubuntu-16-04-17-10-18-04-Desktop-via-Remote-Desktop-connection-RDP-with-XRDP/

*   安装 Ubuntu Mate OS
    通常，当你从 Digital Ocean 或任何其他厂商购买 Linux VM 时，你不会得到 OS。要安装轻量级 Ubuntu Mate Linux 操作系统

[https://jerrygamblin . com/2016/10/19/Ubuntu-remote-desktop-on-digital-ocean/](https://jerrygamblin.com/2016/10/19/ubuntu-remote-desktop-on-digital-ocean/)

*   **主管、Nginx 配置**
    [https://www . hanselman . com/blog/publishinganaspnetcorewebsitetoaceapplinuxvmhost . aspx](https://www.hanselman.com/blog/PublishingAnASPNETCoreWebsiteToACheapLinuxVMHost.aspx)

*   **微软官方文档**
    [https://Docs . Microsoft . com/en-us/aspnet/core/host-and-deploy/Linux-nginx？view = aspnetcore-2.1&tabs = aspnetcore 2x](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/linux-nginx?view=aspnetcore-2.1&tabs=aspnetcore2x)

*   **Nginx -深度配置**
    [https://sharetechlinks . com/post/detail/173/Nginx-everything-about-proxy-pass](https://sharetechlinks.com/post/detail/173/nginx-everything-about-proxy-pass)
    [https://sharetechlinks . com/post/detail/204/Nginx-with-modsecurity-and-brotli-production-setup-dockered](https://sharetechlinks.com/post/detail/204/nginx-with-modsecurity-and-brotli-production-setup-dockerized)

*   **设置防火墙**
    [https://www . digital ocean . com/community/tutorials/how-to-set-a-Firewall-with-ufw-on-Ubuntu-14-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-firewall-with-ufw-on-ubuntu-14-04)

*   **在窗户上安装油灰**

[https://medium . com/@ bharatdwarkani/5-easy-steps-to-install-putty-and-connecting-to-Linux-Ubuntu-server-in-windows-adcdc 9d 755 DC](https://medium.com/@bharatdwarkani/5-easy-steps-to-install-putty-and-connecting-to-linux-ubuntu-server-in-windows-adcdc9d755dc)

*   **使用 Putty**
    将文件从 windows 传输到 Linux[https://medium . com/@ bharatdwarkani/copy-a-file-from-windows-to-Linux-through-ssh-17 F4 F4 F4 C2 ECA 8](https://medium.com/@bharatdwarkani/copying-a-file-from-windows-to-linux-through-ssh-17f4f4c2eca8)

*   **安全头**
    [https://sharetechlinks . com/post/detail/133/adding-http-Headers-to-improve-Security-in-an-ASP-net-core-application](https://sharetechlinks.com/post/detail/133/adding-http-headers-to-improve-security-in-an-asp-net-core-application)

*   **在 Ubuntu**
    [https://www . hosting advice . com/how-to/Unzip-file-linuxubuntu/](https://www.hostingadvice.com/how-to/unzip-file-linuxubuntu/)中解压文件参考此

*   **在 Linux 中安装我的 SQL**
    [https://www . digital ocean . com/community/tutorials/how-to-install-MySQL-on-Ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-16-04)

[https://www . digital ocean . com/community/tutorials/how-to-secure-MySQL-and-Maria db-databases-in-a-Linux-VPS](https://www.digitalocean.com/community/tutorials/how-to-secure-mysql-and-mariadb-databases-in-a-linux-vps)

[https://www . configserver firewall . com/Ubuntu-Linux/enable-MySQL-remote-access-Ubuntu/](https://www.configserverfirewall.com/ubuntu-linux/enable-mysql-remote-access-ubuntu/)

**结论**
上述清单足以让你从零开始在一个全新的 Linux 虚拟机上托管你的 ASP.NET 核心站点。