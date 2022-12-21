# 使用 Docker 在 Django 项目中启用调试

> 原文：<https://dev.to/herchila/enable-debugging-in-a-django-project-with-docker-2550>

嗨！在这篇文章中，我将向你展示如何用 Docker 调试你的 Django 代码。

在一个有 3 个映像(django，postgres 和 redis)的 Django 项目中，你不能调试你的代码。

我们的项目运行没有任何问题:

```
$ docker-compose up
Starting redis_1        ... done
Starting postgres_1     ... done
Starting celeryworker_1 ... done
Starting django_1       ... done
Starting flower_1       ... done
Starting celerybeat_1   ... done
Attaching to redis_1, postgres_1, celerybeat_1, django_1, celeryworker_1, flower_1
...
...
django_1        | Performing system checks...
django_1        |
django_1        | System check identified no issues (0 silenced).
django_1        | August 25, 2019 - 02:29:42
django_1        | Django version 2.0.9, using settings 'config.settings.local'
django_1        | Starting development server at http://0.0.0.0:8000/
django_1        | Quit the server with CONTROL-C. 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们在代码(`import pdb; pdb.set_trace()`)中放一个断点，我们会得到这样的错误:

```
django_1        | Performing system checks...
django_1        |
django_1        | > /app/config/urls.py(12)<module>()
django_1        | -> path(settings.ADMIN_URL, admin.site.urls),
django_1        | (Pdb)
django_1        | Unhandled exception in thread started by <function check_errors.<locals>.wrapper at 0x7f959fef4400>
django_1        | Traceback (most recent call last):
django_1        |   File "/usr/local/lib/python3.6/site-packages/django/utils/autoreload.py", line 225, in wrapper
...
...
django_1        |   File "/app/config/urls.py", line 12, in <module>
django_1        |     path(settings.ADMIN_URL, admin.site.urls),
django_1        |   File "/app/config/urls.py", line 12, in <module>
django_1        |     path(settings.ADMIN_URL, admin.site.urls),
django_1        |   File "/usr/local/lib/python3.6/bdb.py", line 51, in trace_dispatch
django_1        |     return self.dispatch_line(frame)
django_1        |   File "/usr/local/lib/python3.6/bdb.py", line 70, in dispatch_line
django_1        |     if self.quitting: raise BdbQuit
django_1        | bdb.BdbQuit 
```

Enter fullscreen mode Exit fullscreen mode

我们要怎么做才能解决这个问题？我们需要在其他终端实例中运行 django 服务器:

这些是步骤:

1-打开其他终端实例
2-运行`docker-compose ps`查看运行中的容器

```
$ docker-compose ps
django_1         /entrypoint /start               Up       0.0.0.0:8000->8000/tcp
postgres_1       docker-entrypoint.sh postgres    Up       5432/tcp
redis_1          docker-entrypoint.sh redis ...   Up       6379/tcp 
```

Enter fullscreen mode Exit fullscreen mode

3-运行`docker rm -f django_1`移除容器
4-然后，运行`docker-compose run --rm --service-ports django` :

```
$ docker-compose run --rm --service-ports django
Starting postgres_1 ... done
PostgreSQL is available
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  No migrations to apply.
Performing system checks...

System check identified no issues (0 silenced).
August 25, 2019 - 02:54:59
Django version 2.0.9, using settings 'config.settings.local'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C 
```

Enter fullscreen mode Exit fullscreen mode

现在，你可以放一个断点了！保存更改并...

```
Performing system checks...

> /app/config/urls.py(12)<module>()
-> path(settings.ADMIN_URL, admin.site.urls),
(Pdb) 
```

Enter fullscreen mode Exit fullscreen mode

希望你觉得有用！