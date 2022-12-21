# 对主动开发有用的 10 个 docker-compose 和 docker 命令

> 原文：<https://dev.to/aduranil/10-docker-compose-and-docker-commands-that-are-useful-for-active-development-22f9>

当然你可能需要其他的，但是随着时间的推移，我发现这些是我唯一需要或经常使用的，我经常在各种项目中使用`Docker`和`docker-compose`。

#### 1。码头集装箱

`docker exec -it :container_id bash`

您可能需要终端到一个容器中来做一些事情，比如运行测试或应用迁移。

```
[13:54:41] (master) selfies
🙋 docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
b5e87b73f6f6        selfies_web            "python manage.py ru…"   2 seconds ago       Up 1 second         0.0.0.0:8000->8000/tcp   selfies_web_1
d8e636ad4805        postgres:10.1-alpine   "docker-entrypoint.s…"   3 seconds ago       Up 2 seconds        0.0.0.0:5432->5432/tcp   selfies_db_1
aeb5cba5a482        redis:latest           "docker-entrypoint.s…"   3 seconds ago       Up 2 seconds        6379/tcp                 selfies_redis_1
[13:54:43] (master) selfies
🙋 docker exec -it b5e87b73f6f6 bash
root@b5e87b73f6f6:/selfies# python manage.py makemigrations
No changes detected
root@b5e87b73f6f6:/selfies# 
```

#### 2。在调试模式下运行 docker 容器

`docker-compose run --service-ports web`

如果您想调试您的服务器，这个命令将允许您这样做。否则，如果在代码中放置调试器，可能会出现错误。

```
[13:56:59] (master) selfies
🙋 docker-compose run --service-ports web
Creating network "selfies_default" with the default driver
Creating selfies_redis_1 ... done
Creating selfies_db_1    ... done
Performing system checks...

System check identified no issues (0 silenced).
July 24, 2019 - 17:57:11
Django version 2.1.7, using settings 'selfies.settings'
Starting ASGI/Channels version 2.2.0 development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
Performing system checks...

System check identified no issues (0 silenced).
July 24, 2019 - 18:12:29
Django version 2.1.7, using settings 'selfies.settings'
Starting ASGI/Channels version 2.2.0 development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
HTTP OPTIONS /app/users/ 200 [0.01, 172.25.0.1:60046]
> /selfies/app/views/account_views.py(48)post()
-> try:
(Pdb) 
```

#### 3。构建 docker 容器

`docker-compose build`

这将运行`Dockerfile.`中的所有内容，我通常在第一次构建项目时运行它，之后只有在我将依赖项添加到我的`requirements.txt`文件或更改 docker 文件中的任何内容时才运行。

```
[18:59:42] (master) selfies
// ♥ docker-compose build
db uses an image, skipping
redis uses an image, skipping
Building web
Step 1/7 : FROM python:3.6-stretch
 ---> 9167692c277e
Step 2/7 : ENV PYTHONUNBUFFERED 1
 ---> Using cache
 ---> 0533dfe1c141
Step 3/7 : ENV REDIS_HOST "redis"
 ---> Using cache
 ---> c01adb015773
Step 4/7 : RUN mkdir /selfies
 ---> Using cache
 ---> e60377d4e9ee
Step 5/7 : WORKDIR /selfies
 ---> Using cache
 ---> 9018fb3984b0
Step 6/7 : ADD . /selfies/
 ---> Using cache
 ---> 8c6d291d99a7
Step 7/7 : RUN pip install -r requirements.txt
 ---> Using cache
 ---> 7caa2f3bf2ac
Successfully built 7caa2f3bf2ac
Successfully tagged selfies_web:latest 
```

#### 4。启动 docker 容器

`docker-compose up`

这将在终端中运行您的容器，并显示服务器输出

```
[13:39:32] (master) selfies
🙋 docker-compose up
Creating network "selfies_default" with the default driver
Creating selfies_redis_1 ... done
Creating selfies_db_1    ... done
Creating selfies_web_1   ... done
Attaching to selfies_db_1, selfies_redis_1, selfies_web_1
db_1     | 2019-07-24 17:40:36.069 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
db_1     | 2019-07-24 17:40:36.069 UTC [1] LOG:  listening on IPv6 address "::", port 5432
redis_1  | 1:C 24 Jul 2019 17:40:36.085 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 24 Jul 2019 17:40:36.085 # Redis version=5.0.5, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 24 Jul 2019 17:40:36.085 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis_1  | 1:M 24 Jul 2019 17:40:36.086 * Running mode=standalone, port=6379.
redis_1  | 1:M 24 Jul 2019 17:40:36.086 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis_1  | 1:M 24 Jul 2019 17:40:36.086 # Server initialized
redis_1  | 1:M 24 Jul 2019 17:40:36.086 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
redis_1  | 1:M 24 Jul 2019 17:40:36.086 * Ready to accept connections
db_1     | 2019-07-24 17:40:36.072 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
db_1     | 2019-07-24 17:40:36.086 UTC [18] LOG:  database system was shut down at 2019-07-24 17:39:28 UTC
db_1     | 2019-07-24 17:40:36.090 UTC [1] LOG:  database system is ready to accept connections
web_1    | Performing system checks...
web_1    | 
web_1    | System check identified no issues (0 silenced).
web_1    | July 24, 2019 - 17:40:38
web_1    | Django version 2.1.7, using settings 'selfies.settings'
web_1    | Starting ASGI/Channels version 2.2.0 development server at http://0.0.0.0:8000/
web_1    | Quit the server with CONTROL-C. 
```

#### 5。在后台启动 docker 容器

`docker-compose up -d`

这将在后台运行容器，因此您可以继续在终端中键入内容。如果我真的不需要看到服务器返回什么，我通常这样运行它。

```
[13:31:03] (master) selfies
🙋 docker-compose up -d
Creating network "selfies_default" with the default driver
Creating selfies_db_1    ... done
Creating selfies_redis_1 ... done
Creating selfies_web_1   ... done 
```

#### 6。查看当前运行的所有 docker 容器

活动 docker 容器列表，这对`CONTAINER ID`很有用，可以了解你正在运行什么。

`docker ps`

```
[13:31:10] (master) selfies
🙋 docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
a2b93a900c36        selfies_web            "python manage.py ru…"   2 seconds ago       Up 1 second         0.0.0.0:8000->8000/tcp   selfies_web_1
2d39a1161aa2        postgres:10.1-alpine   "docker-entrypoint.s…"   4 seconds ago       Up 2 seconds        0.0.0.0:5432->5432/tcp   selfies_db_1
62a6f364860e        redis:latest           "docker-entrypoint.s…"   4 seconds ago       Up 2 seconds        6379/tcp                 selfies_redis_1 
```

#### 7。删除存储库中的所有 docker 容器

`docker-compose down`

出于习惯，我几乎总是使用这个命令来确保容器被成功移除。

```
[13:37:20] (master) selfies
🙋 docker-compose down
Stopping selfies_web_1   ... done
Stopping selfies_db_1    ... done
Stopping selfies_redis_1 ... done
Removing selfies_web_1   ... done
Removing selfies_db_1    ... done
Removing selfies_redis_1 ... done
Removing network selfies_default 
```

#### 8。移除特定的 docker 容器

`docker kill :container_id`

在执行`docker ps.`时，容器 id 是最左边的一列。如果我需要删除一个我不使用的特定容器，我有时会这样做。

```
[13:51:43] (master) selfies
🙋 docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
274b1605ca94        selfies_web            "python manage.py ru…"   11 minutes ago      Up 1 second         0.0.0.0:8000->8000/tcp   selfies_web_1
a641f449edfc        postgres:10.1-alpine   "docker-entrypoint.s…"   11 minutes ago      Up 2 seconds        0.0.0.0:5432->5432/tcp   selfies_db_1
61b08693e242        redis:latest           "docker-entrypoint.s…"   11 minutes ago      Up 2 seconds        6379/tcp                 selfies_redis_1
[13:51:44] (master) selfies
🙋 docker kill 274b1605ca94
274b1605ca94 
```

#### 9。查看所有 docker 图像

您可以通过运行这个来查看您的所有构建。对我来说，这些要么是 redis 或 python 之类的官方“图片”，要么是我的项目的旧版本。我只是偶尔使用它，它不是我日常发展的一部分。

`docker images`

```
[18:28:17] (master) selfies
// ♥ docker images
REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
selfies_web                      latest              4869d063569e        7 days ago          1.32GB
python                           3.6-stretch         9167692c277e        11 days ago         936MB
redis                            latest              598a6f110d01        12 days ago         118MB
<none>                           <none>              9c4676224e86        2 months ago        1e+03MB
<none>                           <none>              6c925f68c3a9        2 months ago        929MB
<none>                           <none>              b44ef8ff52f4        2 months ago        929MB
<none>                           <none>              903b976cd478        2 months ago        1.47GB
<none>                           <none>              f7009c6f0868        2 months ago        1.49GB
<none>                           <none>              52750c0c3926        2 months ago        1.48GB
<none>                           <none>              1c509a380925        2 months ago        1.44GB 
```

#### 10。清除任何图像、构件等。，那可能是吊死

`docker system prune`

我只是偶尔使用它，它不是我日常发展的一部分。

```
[18:18:35] (master) selfies
// ♥ docker system prune
WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all dangling images
        - all build cache
Are you sure you want to continue? [y/N] y
Deleted Containers:
1f570bbf6828dadfdaa97655165943fd0b93ce6c185df2531f61a82982ec24f2
40f79091bc943c86f5f3ab7bc9d484ac9b576effc686a8a0fdb0031a465b16b4
f3399b0cd4f8c30159b15e1a2027aadeeaf9006efa4b759e4e3586d4589a0004
e0d18cddd40241de3d3b03d695afe667819c884a12fd36465c4c6d584df5aaa6
b7757cef15b49ecfe58ac6a8de3f8fcae91c71d793b7942955ccaf7c266bff92
a8fc94cd1af14f27b0ae22c1728b33fc8cef4090aa7deef1c2549c33755ed114
4de12efd1f88b9ee3541049c9b6fb3df5b1ade3b5787f888f5cf05f2e02c3cec
21d97261412949c06a4fcfb9846a6dee22f92e6a928cd6fb715ee81924917d43
073ca414e8c79e593c82f46f565adbae92a159994699dbbd0fb9df8044b3b1bb
b62f36a4a182479d25c2251e8d90fb7d2b612f31a03e943e83882ab436981879
456510a02d7ad5137dcb907484b2c8d9e07f51946be1103d294e4e253bc0e664
effee0d0a9afa74825f8f820bd363ebe1b7b54948aed38d57e84342482c367fd
5d930ece03ed8a2c2cb9178b81c9b42ef4c7397ef90d29b040ed3b1220f8ba27
a6ccf4f68831a19ab7381354f5643f3afd94cfbe0d066ff9ce4b248b90139a63
1a65f709f9b9c7d9749a04e88d3c4d18539a495160e143337c25025b10f9b24c
e98fbb022cb920dd4550751fdb735c1f73ae1dd6a049b9855bd77db1a2cbb3d1
18cb48bcb6428d4a9d5ca2f4b9c0a2fbedb3ef98c1d1fe6788029d7abea8efe9
97d0ee4625df7d107bc543318b1b45701f17720c41c7bb9614183e2cca0e26c7
5fafd4e2df31a0b84657ce1a0450c9cc8d8caa56ede9b10cddbfa6b6f55f6b50
212dae9eab339a84817b590cceb345d66290fa7f0fd7e347bffda322eb60400e
14cdd7e40ca412e1b091ddf640513ecf3f5f8bfd51cbc826e59b4901b1a0e213
ceeeb46b3aa52660a7080949543654e6436b0c050c865ef6cbed83f43bba8cc3
6c96d28cffed031a8bdd836670401b74eb52fa8f03c1dca83b73b8d0ba8530ec
9c89d36c6ee1f8fbc9d0c542d14d6d5b924817abf47a656941062eed95e607f6
58d212388ac7f15468306e306977661169e8aacdeeaba2fea4fc9cf7ebbb9e46
71963edbfe38d4578ba1338a0d03ed94f81664b202f969acf72d38ceeded5fed
4838aa870e8962e6a1c8c0e679dad8d25f5ad08824bfd45df150d148ae7097ba
abab59a1cd1fcf0a14e1d8f45d03f1787b218233a118af59f604ad890cc64dc1
5375b62e20d2660f8347f5f7355122f9fb528a1b603b1e58ceb7d7b811740410
29d7238e6d81f522f8c98b4d2e478bb304aec7a00892b8339bf1b4a0483e7040

Deleted Networks:
selfiesh_default 
```