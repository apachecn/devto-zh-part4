# 如何版本控制 docker-compose 文件

> 原文：<https://dev.to/ducaale/how-to-version-control-docker-compose-file-1o4n>

在某种程度上，docker-compose 文件变得复杂，我们需要对它进行版本控制。典型的目录结构如下:

```
todo
├── .env
├── docker-compose.yml
├── todo-api/
├── todo-worker/
└── todo-database/ 
```

Enter fullscreen mode Exit fullscreen mode

而我们的`docker-compose.yml`档是这样的:

```
# todo/docker-compose
version: '3.1'

services:
  todo-database:
    image: postgres:11.3
    volumes:
      - ./todo-database:/docker-entrypoint-initdb.d/
    environment:
      POSTGRES_PASSWORD: $POSTGRES_PASSWORD

  todo-api:
    build: ./todo-api
    environment:
      APP_KEY: $APP_KEY
      DATABASE_URL: postgres://postgres:${POSTGRES_PASSWORD}@todo-database:5432/postgres

  todo-worker:
    build: ./todo-worker
    environment:
      DATABASE_URL: postgres://postgres:${POSTGRES_PASSWORD}@todo-database:5432/postgres 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的`.env`文件

```
# todo/.env
POSTGRES_PASSWORD=secret-1
APP_KEY=secret-2 
```

Enter fullscreen mode Exit fullscreen mode

## 那么我们如何才能对它进行版本控制呢？

一种方法是创建`todo-compose/`文件夹并将`.env`和`docker-compose.yml`文件移动到`todo-compose/`，这样我们就可以在其中初始化一个 git repo

```
$ mkdir todo-compose
$ mv docker-compose.yml .env todo-compose/ 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将`.env`重命名为`.env.example`,并删除其中的所有密码，这样我们就可以用它作为参考。现在的`todo-compose/`应该是这样的:

```
todo-compose
├── .env.example
└── docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

因为我们将`docker-compose.yml`文件移动到了它自己的文件夹中，所以我们必须修改每个服务构建和卷密钥，以便像这样向上移动一个目录:

```
# todo/todo-compose/docker-compose.yml
version: '3.1'
  # ...
  todo-database:
    volumes:
      - ../todo-database:/docker-entrypoint-initdb.d/
  # ...
  todo-api:
    build: ../todo-api
  # ...
  todo-worker:
    build: ../todo-worker
  # ... 
```

Enter fullscreen mode Exit fullscreen mode

但是我们能做得更好吗？原来，我们可以使用符号链接让 docker 认为`docker-compose.yml`存在于`todo/`中，而实际上它在`todo/todo-compose/`T3 中

```
$ ln -s todo-compose/docker-compose.yml ./docker-compose.yml 
```

Enter fullscreen mode Exit fullscreen mode

现在我们把我们的`.env.example`参考文件复制到`todo/`

```
$ cp todo-compose/.env.example ./.env 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们修改新创建的`.env`来包含项目密码。现在我们的目录结构应该是这样的:

```
todo
├── .env
├── docker-compose.yml -> todo-compose/docker-compose.yml
├── todo-compose/
│   ├── .env.example
│   └── docker-compose.yml
├── todo-api/
├── todo-worker/
└── todo-database/ 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的一个好处是，如果你有多个 docker 文件，比如:

```
todo-compose
├── .env.example
├── docker-compose.yml/
├── docker-compose.prod.yml/
└── docker-compose.dev.yml/ 
```

Enter fullscreen mode Exit fullscreen mode

你负责开发部门的`docker-compose -f docker-compose.yml -f docker-compose.dev.yml up`和生产部门的`docker-compose -f docker-compose.yml -f docker-compose.prod.yml up`。现在你只需要做

```
# in development
$ ln -s todo-compose/docker-compose.yml ./docker-compose.yml
$ ln -s todo-compose/docker-compose.dev.yml ./docker-compose.override.yml

# in production
$ ln -s todo-compose/docker-compose.yml ./docker-compose.yml
$ ln -s todo-compose/docker-compose.prod.yml ./docker-compose.override.yml 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以使用相同的命令`docker-compose up`进行开发和生产

*本帖最早出现在[https://du caale . github . io](https://ducaale.github.io)T3】*