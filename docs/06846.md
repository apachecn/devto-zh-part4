# 一个 PHP 开发人员的第一次 NodeJS 之旅！(还碰上了一期 CORS 杂志)

> 原文：<https://dev.to/rhobur/a-php-developer-s-first-journey-into-the-nodejs-land-and-bumping-into-a-cors-issue-4aa>

我是一个长期的 LAMP 开发者，PHP 是我多年的主要语言。最近，我面临着为一个项目选择不同的堆栈，这个项目包括创建一个网站、一个后勤办公室以及相应的 IOS/Android 移动应用程序。
我是那种需要数月时间才能决定下一步转向哪种技术/框架的人，通常我会花大量时间在谷歌上搜索和阅读特性、功能、比较和评论。

在权衡利弊之后，我决定:

*   Nodejs，因为假设的速度和数量庞大的免费包；
*   Sailsjs 作为 nodejs 框架，因为它看起来更像是我在 PHP 领域习惯的 MVC 模型；
*   Vuejs，更容易掌握，不依赖科技公司；
*   Mysql 而不是无处不在的 mongo，因为我喜欢关系数据库，用了这么久。
*   移动应用程序的 nativescript，因为，嗯，你猜，它是最接近于原生的，而不是真正直接使用原生 API 的(这仍处于早期工作中，因为我刚刚开始使用它)。

这个堆栈的主要原因之一是我可以使用单一语言，Javascript。正如您可能猜到的，我以前与 JS 的互动主要是通过使用任何 PHP 开发人员都知道的 Jquery 库。与我必须学习的新技术相比，重新熟悉和更新 JS 并不痛苦。

对我来说，作为一个新的堆栈，我必须学习一些 Udemy 课程，大量阅读，并从每个开发人员都熟知的常见的反复试验开始。

我的常规项目正在进行中，我已经与客户达成一致，我将在这个项目上分配最多 50%的工作时间，所以难怪我花了近 6 个月的时间来交付 webapp 和它的后勤办公室。

这个时间量包括学习、开发、测试和修复错误，所以我希望在下一个类似的项目中，我能够将这个时间减少到不超过一半。

我已经用 Sails 和它特殊的 Vue 包装器(parasails)构建了 webapp。backoffice 是一个独立的 webapp，使用与 Sails 相同的 API，但这次我使用普通的 Vuejs 作为后端前端。

当需要为客户端安装所有东西时，我选择了 ec2 实例。虽然有些人可能会争辩说可能有更好的方法，但作为一个 Linux 的家伙，我认为我应该更好地坚持服务器技术，没有时间做进一步的实验。

因为这个服务器意味着运行几个应用程序，所以我选择将 Nginx 配置为反向代理，并将请求发送到 PM2。

说起来容易做起来难，因为尽管我阅读了关于这种设置的所有资料，但我还是陷入了 CORS 地狱，在从后端应用程序访问 Sails API 方面似乎没有任何东西可以工作。

这就是为什么我认为我应该写下这个应用服务设置的最终工作配置，因为我已经花了很多天试图让它工作。

我希望它能让其他人避免我在这段时间遭受的挫折，这段时间似乎没有什么对我有用，尽管它显然对其他人有用。

这就是:

1.  Nginx 为 sails 网站配置，位于 nginx 网站-可用目录:

```
server {
    listen 80;
    server_name example.com;
    return 301 https://example.com$request_uri;
}

server {
    server_name example.com www.example.com
    root /usr/share/nginx/example.com;

    location / {
    include /etc/nginx/cors.conf;

    proxy_http_version 1.1;
    proxy_pass http://app_nodes;
    proxy_redirect off;
    proxy_pass_header  Set-Cookie;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-NginX-Proxy true;
    proxy_pass_header X-XSRF-TOKEN;
    proxy_read_timeout 240s;
   }

    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = www.example.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    if ($host = example.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot

    listen 80 default_server;

    server_name example.com www.example.com;
    return 404; # managed by Certbot
} 
```

类似的第二个配置对于代理连接到 Sails API 的后端 Vue 应用程序是必要的。

1.  包含在 nginx 根目录中的 cors 文件:

```
set $cors_origin "";
    set $cors_cred   "";
    set $cors_header "";
    set $cors_method "";

    if ($http_origin ~ '^https?://(localhost|www\.allscubashops\.com|XXX.XXX.XXX.XXX)') {
            set $cors_origin $http_origin;
            set $cors_cred   true;
            set $cors_header $http_access_control_request_headers;
            set $cors_method $http_access_control_request_method;
    }

    add_header Access-Control-Allow-Origin      $cors_origin;
    add_header Access-Control-Allow-Credentials $cors_cred;
    add_header Access-Control-Allow-Headers     $cors_header;
    add_header Access-Control-Allow-Methods     $cors_method; 
```

“XXX。XXX.XXX.XXX”代表 Vue 后端在开发模式下运行，以便连接到 Sails API。

1.  最后，在 Sails config/security.js 中的 cors 设置"

```
cors: {
    allRoutes: true,
    allowOrigins: [
      'http://XXX.XXX.XXX.XXX:443',
      'https://example.com',
    ],
    allowRequestMethods: 'GET, POST, PUT, DELETE, OPTIONS, HEAD',
    allowRequestHeaders: 'Content-Type, Authorization,Cache-Control,Content-Type,DNT,If-Modified-Since,Keep-Alive,Origin,User-Agent,X-Requested-With',
    allowCredentials: true,
  } 
```

暂时就这些了，希望对某个人有帮助。