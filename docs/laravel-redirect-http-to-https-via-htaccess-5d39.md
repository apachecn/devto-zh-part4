# Laravel 重定向 HTTP 到 HTTPS 通过。htaccess

> 原文：<https://dev.to/skipperhoa/laravel-redirect-http-to-https-via-htaccess-5d39>

今天，我与你分享，重定向 http 到 Https 在 laravel
你创建的文件。根文件中的 htaccess

```
RewriteEngine On
RewriteRule ^(.*)$ public/ [L] 
```

上面的代码把它重定向到 http，如果你想把 http 转换成 https，你可以使用上面的代码

```
<IfModule mod_rewrite.c>
    RewriteEngine On        
  RewriteCond %{HTTPS} !=on    
  RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
  RewriteRule ^(.*)$ public/ [L]
</IfModule> 
```

Laravel 通过重定向 HTTP 到 HTTPS。htaccess