# 使用 Nginx 和 Gunicorn 部署 masonite 2 项目

> 原文：<https://dev.to/holiccl/deploying-masonite-2-project-with-nginx-and-gunicorn-3pnj>

Masonite 是一个令人难以置信的开发人员友好的框架，可以用 python 开发现代 web 应用程序。具有大量开箱即用的功能和极具可扩展性的体系结构。根据我自己的经验，用漂亮的代码构建应用程序对你有很大的帮助。

它给你带来了一个叫做`craft`命令的实用程序，可以更容易更快地创建你的模型/控制器/认证/中间件类等等。

Craft command 内置了一个 web 服务器，用于在开发环境中运行您的 web 应用程序。虽然这使得开发变得很容易，但是这个 web 服务器并不是为在生产环境中部署而设计的。

在本指南中，我将尝试解释如何在虚拟专用服务器(如 AWS 或 DigitalOcean)上配置和部署您的 Masonite 应用程序。

## 用于本指南的要求

*   [Debian Buster](https://www.debian.org/distrib/)
*   [Masonite 2.2](https://docs.masoniteproject.com/)
*   Python 3.7
*   Nginx
*   Gunicorn

## 安装您的网络应用

首先，我将制作一个新的 Masonite 项目。我将跳过安装步骤，因为这不是本指南的目的，但是你可以使用[这篇解释得很好的教程](https://dev.to/masonite/masonite-framework-tutorial-series-part-1-140e)来完成安装过程。

```
craft new demoapp
cd demoapp

# Create the virtual env with python3
python3 -m venv venv
source venv/bin/activate
craft install 
```

这将使用精心制作的应用程序创建一个新的 *demoapp* 文件夹，然后创建并激活虚拟环境，最后安装`requirements.txt`文件中描述的需求。

在最后一步中(在我使用 Debian Sid 的机器上)，它打印了一些安装了 Debian 版本 python wheels 的错误:

```
Failed building wheel for masonite
Failed building wheel for masonite-validation
Failed building wheel for masonite-dot
Failed building wheel for pyyaml 
```

但是 craft 命令创建了应用程序密钥，并且一切正常，没有问题，但是如果它发出噪音，我建议的解决方法是在虚拟环境中安装带有 pip 的轮子:

```
# activate virtualenv if is deactivated
source venv/bin/activate
pip install wheel
craft install 
```

## 安装 nginx & gunicorn

在 debian 中你可以使用 apt，apt-get 或者 aptitude 来安装包。我们需要安装 nginx web 服务器，并将其配置为与 gunicorn 相反的代理。

```
# We need nginx and gunicorn3 for python3
sudo aptitude install nginx gunicorn3 
```

Gunicorn 是一个用于 UNIX 的 Python WSGI HTTP 服务器。建议在 HTTP 代理服务器后面使用 Gunicorn(本例中为 Nginx)。

Masonite 带来了一个`wsgi.py`文件，您可以使用它来执行我们的应用程序:

```
gunicorn3 wsgi:application 
```

Gunicorn 默认设置将只允许访问本地机器。如果您想从其他设备访问，您可以使用`-b`选项。

# Nginx 和 Gunicorn 配置

安装 nginx 后，我们将配置 Gunicorn 作为 systemd 上的服务运行，这样我们就可以使用`systemctl`命令进行控制，并在系统重启后运行。

这将是`/etc/nginx/sites-avalaible/default`文件，到项目的路径是`/home/admin/webapps/demoapp`

```
server {
    server_name demoapp.io;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /public/ {
        root /home/admin/webapps/demoapp/storage;
    }
    location /static/ {
        root /home/admin/webapps/demoapp/storage;
    }
    location /uploads/ {
        root /home/admin/webapps/demoapp/storage;
    }
    location /compiled/ {
        root /home/admin/webapps/demoapp/storage;
    }

    access_log /var/log/nginx/access.log;

    location / {
        include proxy_params;
        proxy_set_header Host $host;
        proxy_pass http://unix:/run/gunicorn.socket;
    }
} 
```

现在 Nginx 配置文件已经编辑好了，我们需要检查语法是否正确:

```
sudo nginx -t

# Output
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful 
```

创建 Gunicorn 服务:
`/etc/systemd/system/gunicorn.service`

```
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
PIDFile=/run/gunicorn/pid
User=admin
Group=www-data
RuntimeDirectory=gunicorn
WorkingDirectory=/home/admin/webapps/demoapp
ExecStart=/usr/bin/gunicorn3 --pid /run/gunicorn/pid\
          --workers 3\
          --bind unix:/run/gunicorn.socket wsgi\
          --error-logfile /home/admin/logs/error.log\
          --access-logfile /home/admin/logs/access.log\
          --log-level debug\
          --timeout 320
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target 
```

该服务文件使用 pid 文件(在`/run/gunicorn/pid`上)执行`gunicorn3`命令，并通过位于`/run/gunicorn.socket`上的套接字文件建立连接，它将日志保存在`/home/admin/logs/`目录中，因此需要权限:

```
mkdir ~/logs/
sudo chown admin.www-data ~/logs/ 
```

创建`/etc/systemd/system/gunicorn.socket`文件:

```
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/run/gunicorn.socket

[Install]
WantedBy=sockets.target 
```

现在几乎一切就绪，我们需要重新加载我们的服务:

```
sudo systemctl daemon-reload

# start gunicorn
sudo systemctl start gunicorn 
```

检查 gunicorn 服务是否工作:

```
sudo systemctl status gunicorn

# Output
● gunicorn.service - gunicorn daemon
   Loaded: loaded (/etc/systemd/system/gunicorn.service; disabled)
   Active: active (running) since Wed 2019-07-29 21:05:07 UTC; 49s ago
 Main PID: 10154 (gunicorn)
   CGroup: /system.slice/gunicorn.service
           ├─10154 /home/admin/webapps/demoapp/venv/bin/python3 /home/admin/webapps/demoapp/venv/bin/gunicorn --workers 3 --bind unix:/run/gunicorn.socket wsgi:application
           ├─10157 /home/admin/webapps/demoapp/venv/bin/python3 /home/admin/webapps/demoapp/venv/bin/gunicorn --workers 3 --bind unix:/run/gunicorn.socket wsgi:application
           ├─10158 /home/admin/webapps/demoapp/venv/bin/python3 /home/admin/webapps/demoapp/venv/bin/gunicorn --workers 3 --bind unix:/run/gunicorn.socket wsgi:application
           └─10159 /home/admin/webapps/demoapp/venv/bin/python3 /home/admin/webapps/demoapp/venv/bin/gunicorn --workers 3 --bind unix:/run/gunicorn.socket wsgi:application

Dec 21 21:05:07 debian-512mb-nyc3-01 systemd[1]: Started gunicorn daemon. 
```

最后，重启 nginx 来使用我们部署的应用程序

```
sudo systemctl restart nginx 
```

还有哒哒！
[![](img/57982488c5fe1060850bbdb8cec3a3a9.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--g5AAXFRo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ppfqdyegqrtkdsezpbn.png)

## 结论

如果你喜欢漂亮的代码，试试 Masonite。