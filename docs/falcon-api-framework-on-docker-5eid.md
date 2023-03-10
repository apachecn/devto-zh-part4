# Docker 上的 Falcon API 框架

> 原文：<https://dev.to/_mertsimsek/falcon-api-framework-on-docker-5eid>

我们要调整 Docker 上的 Falcon API 框架。这样，我们将能够构建我们的 API。有 2 种框架，全功能和裸框架。如果你想用 Python 为你的项目开发一个 RESTFul API，一般来说，你可以考虑如下的结构。

1)带长颈瓶的长颈瓶-RESTFul

2) Django + REST 框架

另一方面，Python 中有一个非常好的轻量级 API 框架，叫做 [Falcon](https://falconframework.org/) 。

[![](img/728149c879e94791a2bc5ba442f8c57d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2VEicJ4s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yki4uug1o4nlkwfmieph.png)

因此，Falcon 声称(我同意他们的观点)，其他框架用大量的依赖和不必要的抽象来拖累你。Falcon 以简洁的设计切入正题，它包含了 HTTP 和 REST 架构风格。

***友情提示***
在本文中，我们将看到猎鹰在 Docker 上的调整和安装。我的下一篇文章将演示用 Falcon 构建 API。

好吧，我们开始吧。最初，我们应该修改目录结构。应该是这样的。

[![](img/435f2d80b72ae566896000fd0a23f77d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WkXfa_kw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uai9k9xaftfev0q7v1qg.png)

首先，我将把 **docker-compose.yml** 文件共享到根目录中。

```
version: '2'
services:
  backend:
    build: ./backend
    ports:
      - "9000:8000"
    volumes:
      - ./app/.:/app
    tty: true
    depends_on:
      - postgresql

  postgresql:
    image: postgres:alpine
    environment:
      POSTGRES_DB: test_dev
      POSTGRES_USER: mertingen
      POSTGRES_PASSWORD: mertingen
    ports:
      - '5432:5432' 
```

如您所见，作为数据库解决方案，我指的是 Postgresql。一旦构建了这个文件，就会创建一个名为 **test_dev** 的数据库和一个名为 **mertingen** 的用户。此外，我们的应用程序公开了 **9000** 端口用于外部服务。

在数据库目录中，我有一个文件。让我们检查一下文档。

```
FROM postgres:10.1-alpine 
```

现在，我只是在画一幅图像。因此，我们将能够连接数据库，并在 docker-compose.yml 文件中引用这些环境变量。我们会运行这个文件。在后端目录中，我有两个文件 Dockerfile 和 start.sh。

```
FROM python:3.6.2

COPY start.sh /scripts/start.sh
WORKDIR /app
ENTRYPOINT ["/scripts/start.sh"] 
```

而且，这里是 start.sh.

```
#!/bin/bash

pip install --upgrade pip
pip install -r /app/requirements.txt

gunicorn app:api -c /app/conf/gunicorn_conf.py --reload 
```

你可以在这里找到 **requirements.txt** 。

```
attrs==19.1.0
falcon==2.0.0
falcon-autocrud==1.0.36
gunicorn==19.9.0
jsonschema==3.0.1
marshmallow==2.19.5
psycopg2==2.8.3
pyrsistent==0.15.4
python-dateutil==2.8.0
six==1.12.0
SQLAlchemy==1.3.6
webargs==5.4.0 
```

**start.sh** 文件安装依赖项并创建一个服务器来运行应用程序。为了服务应用程序，我在 conf 目录中创建了两个配置文件， **config.ini** 和 **gunicorn_conf.py** 。这是 **config.ini** 文件。

```
[postgresqlDB]
host = postgresql
db = test_dev
user = mertingen
pass = mertingen 
```

为了连接数据库，我们将使用这个文件。在我的下一篇文章中，这些参数将是环境变量。该文件有助于连接到数据库。

```
import psycopg2
import configparser
import os

dir_path = os.path.dirname(os.path.realpath(__file__))
config = configparser.ConfigParser()

config.read(dir_path + '/../conf/config.ini')

def connect():
    try:
        connection = psycopg2.connect(user=config['postgresqlDB']['user'],
                                      password=config['postgresqlDB']['pass'],
                                      host=config['postgresqlDB']['host'],
                                      port="5432",
                                      database=config['postgresqlDB']['db'])
        print("You are connected!")
        return connection
    except (Exception, psycopg2.Error) as error:
        print("Error while connecting to PostgreSQL", error)
    # finally:
    #    if connection:
    #        connection.close()
    #        print("PostgreSQL connection is closed") 
```

现在分享一下 **gunicorn 服务器**的配置。

```
import multiprocessing

bind = '0.0.0.0:8000'
workers = multiprocessing.cpu_count() * 2 + 1
timeout = 30
worker_connections = 1000 
```

我们几乎不需要创建 Falcon 应用程序。为此，让我们创建 **app.py** 并放入这段代码。

```
import falcon
from resources import user
from services import database

#from middlewares import (
#    ContentEncodingMiddleware,
#) 
conn = database.connect()

#api = falcon.API(middleware=[
#    ContentEncodingMiddleware(),
#]) 
api = falcon.API()
user = user.User(conn)
api.add_route('/users/{id}', user)
api.add_route('/users', user, suffix='collection') 
```

简单地说，为了查看来自这个 API 的响应，我在 resources 目录中创建了一个用户资源文件。是这样的。

```
import falcon

    def __init__(self, conn):
        self.conn = conn

    @staticmethod
    def set_columns(data, cursor):
        items = []
        for x in data:
            item = {}
            c = 0
            for col in cursor.description:
                item.update({col[0]: x[c]})
                c = c + 1
            items.append(item)
        return items

    def on_get_collection(self, req, resp):
        try:
            cursor = self.conn.cursor()

            query = """SELECT * FROM Users"""
            cursor.execute(query)
            records = cursor.fetchall()
            cursor.close()

            output = {"status": True, "message": None, 'data': self.set_columns(records, cursor)}

            resp.status = falcon.HTTP_200
            resp.body = output
        except Exception as error:
            output = {"status": False, "message": str(error), "data": None}
            resp.status = falcon.HTTP_500
            resp.media = output 
```

事实上，我没有提到代码，因为我说过我们将在下一篇文章中构建一个 API。我插入了一些假的用户数据，应用程序返回数据。

从现在开始，我们只需要在根目录下用这个命令运行 Docker Compose 服务。

```
docker-compose up --build 
```

结果，您将得到这样的输出。

[![](img/e2a33eece882d0b7f73679e776b41700.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--si6exlJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s4v0x8hu23wem37zg50i.png)

此时，只要你请求**[http://localhost:9000/users](http://localhost:9000/users)**URL，你就能够得到这个结果。我说过，我玩的是假数据。

[![](img/07e398287f5ce7992c83955e9401d393.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yz5kQ1hj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2mjvxj87zxx26xtbnhf4.png)

***总而言之***

感谢你们所有人读到这里。Docker 有助于构建您的开发工作空间，我们应该以这种方式利用它的功能。同样，Falcon 框架构建了非常酷的 API。希望，这篇文章对你有用，下篇再见。如果你有什么困难，尽管问。