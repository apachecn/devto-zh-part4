# 使用 uWSGI 将 Django 部署到生产环境中

> 原文：<https://dev.to/suda/deploying-django-to-production-with-uwsgi-54pa>

有很多关于 dockerizing Django 应用程序的帖子，但我觉得还有一些改进的空间。

许多方法只关注开发流程，这使得生成的图像非常适合迭代，但对于生产使用来说并不理想。其他的，产生非常大的图像，作为`root` 运行应用程序，或者忽略处理静态文件，把它委托给 S3。有了这些照片，我有了几个目标:

*   生产就绪映像(遵循一般安全提示)
*   使用`alpine`底座拍摄小图像
*   [多阶段](https://docs.docker.com/develop/develop-images/multistage-build/)构建更小更干净的图像
*   在同一容器中处理静态文件

我决定使用 [`uWSGI`](https://uwsgi-docs.readthedocs.io/en/latest/) ，它实际上也可以服务静态文件，使我的生活变得更加容易。

**注意:**我正在使用 [pipenv](https://pipenv.readthedocs.io/en/latest/) 来管理 virtualenv 和所有的依赖项(我也推荐你使用它)，因此如果你使用 bare `pip`，一些指令可能需要调整。

**注 2:** 如果你喜欢在 ASGI 中运行，我也为基于`uvicorn`的 Docker 镜像写了指令[。](https://dev.to/suda/production-ready-django-3-asgi-docker-image-1d49)

# 指令

## 设置`uWSGI`

首先将它添加到您的依赖项:

```
$ pipenv install uwsgi 
```

然后在你的项目根目录下创建`uwsgi.ini`文件:

```
[uwsgi]
chdir = /app
uid = $(UID)
gid = $(GID)
module = $(UWSGI_MODULE)
processes = $(UWSGI_PROCESSES)
threads = $(UWSGI_THREADS)
procname-prefix-spaced = uwsgi:$(UWSGI_MODULE)

http-socket = :8080
http-enable-proxy-protocol = 1
http-auto-chunked = true
http-keepalive = 75
http-timeout = 75
stats = :1717
stats-http = 1
offload-threads = $(UWSGI_OFFLOAD_THREADS)

# Better startup/shutdown in docker: die-on-term = 1
lazy-apps = 0

vacuum = 1
master = 1
enable-threads = true
thunder-lock = 1
buffer-size = 65535

# Logging log-x-forwarded-for = true

# Avoid errors on aborted client connections ignore-sigpipe = true
ignore-write-errors = true
disable-write-exception = true

no-defer-accept = 1

# Limits, Kill requests after 120 seconds harakiri = 120
harakiri-verbose = true
post-buffering = 4096

# Custom headers add-header = X-Content-Type-Options: nosniff
add-header = X-XSS-Protection: 1; mode=block
add-header = Strict-Transport-Security: max-age=16070400
add-header = Connection: Keep-Alive

# Static file serving with caching headers and gzip static-map = /static=/app/staticfiles
static-map = /media=/app/media
static-safe = /usr/local/lib/python3.7/site-packages/
static-gzip-dir = /app/staticfiles/
static-expires = /app/staticfiles/CACHE/* $(UWSGI_STATIC_EXPIRES)
static-expires = /app/media/cache/* $(UWSGI_STATIC_EXPIRES)
static-expires = /app/staticfiles/frontend/img/* $(UWSGI_STATIC_EXPIRES)
static-expires = /app/staticfiles/frontend/fonts/* $(UWSGI_STATIC_EXPIRES)
static-expires = /app/* 3600
route-uri = ^/static/ addheader:Vary: Accept-Encoding
error-route-uri = ^/static/ addheader:Cache-Control: no-cache

# Cache stat() calls cache2 = name=statcalls,items=30
static-cache-paths = 86400

# Redirect http -> https route-if = equal:${HTTP_X_FORWARDED_PROTO};http redirect-permanent:https://${HTTP_HOST}${REQUEST_URI} 
```

**注意:**上面配置的作者是[迪德里克·范德布尔](https://github.com/nginxinc/kubernetes-ingress/issues/143#issuecomment-347814243)🙇

## 创建`Dockerfile`

```
# Build argument allowing to change Python version
ARG PYTHON_VERSION=3.7

# Build dependencies in a separate container
FROM python:${PYTHON_VERSION}-alpine AS builder
ENV WORKDIR /app
COPY Pipfile ${WORKDIR}/
COPY Pipfile.lock ${WORKDIR}/

RUN cd ${WORKDIR} \
    && pip install pipenv \
    && pipenv install --system

# Create the final container with the app
FROM python:${PYTHON_VERSION}-alpine

ENV USER=docker \
    GROUP=docker \
    UID=12345 \
    GID=23456 \
    HOME=/app \
    PYTHONUNBUFFERED=1
WORKDIR ${HOME}

# Create user/group
RUN addgroup --gid "${GID}" "${GROUP}" \
    && adduser \
    --disabled-password \
    --gecos "" \
    --home "$(pwd)" \
    --ingroup "${GROUP}" \
    --no-create-home \
    --uid "${UID}" \
    "${USER}"

# Run as docker user
USER ${USER}
# Copy installed packages
COPY --from=builder /usr/local/lib/python3.7/site-packages /usr/local/lib/python3.7/site-packages
# Copy uWSGI binary
COPY --from=builder /usr/local/bin/uwsgi /usr/local/bin/uwsgi
# Copy the application
COPY --chown=docker:docker . .
# Collect static files
RUN python manage.py collectstatic --noinput

ENTRYPOINT ["uwsgi", "--ini", "uwsgi.ini"]
EXPOSE 8080 
```

## 启动容器

为了正常工作，您需要设置一些环境变量。如果你使用 Docker Compose，你可以使用类似的`docker-compose.yml`文件:

```
version: "3"
services:
  app:
    build: .
    image: foo/bar
    ports:
      - "8080:8080"
    environment:
      - UWSGI_MODULE=myapp.wsgi:application
      - UWSGI_PROCESSES=10
      - UWSGI_THREADS=2
      - UWSGI_OFFLOAD_THREADS=10
      - UWSGI_STATIC_EXPIRES=86400 
```

然后，您可以使用以下命令构建映像:

```
$ docker-compose build app 
```

# 结论

我对这个设置很满意，它已经在生产中工作了一段时间。请让我知道如果你有任何意见，如果我可以改善这一点！