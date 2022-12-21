# PHP + nginx + caché + tmpfs，一个 ganadora 组合

> 原文：<https://dev.to/lito/php-nginx-cache-tmpfs-una-combinacion-ganadora-92p>

[![](img/ea6098b32dc9a7fe43d6294eb7b9e2d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KMRBqmn---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q6fvugweqfuzepay3uv1.png)

假设我们有一个简单的项目，没有用户系统，我们希望它对服务器造成非常低的负载，并实现超快的响应时间。

一种解决方法可能是通过自己的代码或某种“[”外部库“](https://github.com/nette/caching)”将缓存委托给 PHP 后端。

另一个更好的解决方案是降低一个级别，让 nginx 负责生成缓存。

此选项有其优点，因为它的速度快得多(没有后端请求，它是一个较低的层)和缺点(无法从 PHP 管理此缓存)。

如果选择后者，则设置相当简单。

首先是创建一个基于“`tmpfs`”的挂载点，使我们能够减少对磁盘的请求并优化其读取。

```
$> mkdir /var/cache/nginx
$> chown www-data:www-data /var/cache/nginx
$> echo 'tmpfs /var/cache/nginx tmpfs defaults,size=256M 0 0' >> /etc/fstab
$> mount /var/cache/nginx 
```

现在，我们配置 nginx:

> /etc/nginx/sites-enabled/example . com

```
# Creamos una zona de caché con un límite de de 256 megas apuntando al directorio tmpfs
# Todo fichero con más de 60 minutos desde el último acceso será descartado fastcgi_cache_path /var/cache/nginx levels=1:2 keys_zone=EXAMPLE:256m inactive=60m;
# Indicamos con qué parametros se deben generar las cachés fastcgi_cache_key "$scheme$request_method$host$request_uri";

server {
    listen 443 ssl http2;

    server_name example.com www.example.com;

    root /var/www/example.com/httpdocs/public;
    index index.php;

    include sites/example.com/base.conf;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/example.com/fullchain.pem;

    access_log /var/log/nginx/example.com_access.log combined buffer=32k flush=60;
    error_log  /var/log/nginx/example.com_error.log;
} 
```

> /etc/nginx/sites/example . com/base . conf

```
rewrite ^/(.*)/$ /$1 permanent;

location / {
    try_files $uri $uri/ /index.php?$query_string;
}

location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

    include fastcgi_params;

    include sites/example.com/cache.conf;
} 
```

> /etc/nginx/sites/example . com/cache . conf

```
# Añadimos una cabecera con el status de cada petición add_header X-Cache $upstream_cache_status;

# Configuramos la zona de caché a usar fastcgi_cache EXAMPLE;
# Configuramos que tipo de respuesta es válida para ser cacheada y los tiempos en caché fastcgi_cache_valid 200 301 302 60m;
# Configuramos con que tipo de errores podemos mostrar una caché obsoleta fastcgi_cache_use_stale error timeout updating invalid_header http_500 http_503;
# Configuramos los usos mínimos por caché fastcgi_cache_min_uses 1;
# Si entran varias peticiones simultáneas a una ruta sin caché, esperar
# a que la primera de ellas genere la caché y el resto responda lo mismo fastcgi_cache_lock on;
# Evitamos las cabeceras de Cache-Control fastcgi_ignore_headers Cache-Control;

set $no_cache 0;

# Aquí puedes añadir todas tus condiciones para evitar la caché 
# No se cachea ninguna petición POST if ($request_method = POST) {
    set $no_cache 1;
}

fastcgi_cache_bypass $no_cache;
fastcgi_no_cache $no_cache; 
```

如果你想深入探讨这个话题，你有很多有趣的帖子，比如[使你的 PHP 网站飞起来。即使在小型 VM 实例上。](https://www.lucasoft.info/topics/blog/2/phenomenal-performance-php-nginx-microcaching)

有了这个和一个蛋糕你的半静态网站就有了一个简单高效的缓存系统。