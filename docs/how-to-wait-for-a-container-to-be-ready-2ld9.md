# 如何等待容器准备好？

> 原文：<https://dev.to/selahattinunlu/how-to-wait-for-a-container-to-be-ready-2ld9>

最初发表在我的[博客](https://blog.selahattinunlu.com/2019-08-04-how-to-wait-for-a-container-to-be-ready)

* * *

### 问题

首先，我在 Docker 上的经验不多。我只是想把我的一个副业项目做成 Docker，我想可以了解一下 Docker。虽然我还没有部署它，但至少在开发中使用它似乎很棒。不管怎样，还是说正题吧。

如果你以前使用过 Docker，你会知道一些容器可能依赖于其他容器。例如，您的后端应用程序可能依赖于 MongoDB(就像我的例子)。在这种情况下，后端应用程序必须等待 mongo 容器运行。

有几种方法可以在配置文件中设置这种依赖关系。是其中之一，我在我的应用程序中使用了它。开始的时候一切都很好，但是今天我遇到了一个问题！我的应用程序没有连接到 MongoDB。但是怎么做呢？上周它还在工作，而我什么也没做！搞什么鬼？

然后，我检查了日志，意识到在我的后端应用程序运行之后，MongoDB 已经准备好等待连接了！但是我用的是`depends_on`配置！我在谷歌上快速搜索了一下，理解了这个问题。

* * *

**depends_on** 只关心容器是否运行。因此，mongodb 是否准备好进行连接并不重要。啊哈！太懂事了。docker 怎么知道它准备好了没有？

* * *

### 解

我也搜索了解决方案，找到了几个解决方案。我要告诉你最适合我的一个。

解决方案是**wait-it . sh**[这里是资源库](https://github.com/vishnubob/wait-for-it)

这是一个 bash 脚本，用于等待 TCP 主机和端口的可用性。你可以在它的知识库中找到描述“它对于同步相互依赖的服务的旋转是有用的，比如链接的 docker 容器。”

#### 如何应用解决方案

*   我已经将`wait-for-it.sh`文件复制到我的应用程序文件夹中。

*   然后将这些行添加到 Dockerfile:

```
COPY wait-for-it.sh /usr/wait-for-it.sh
RUN chmod +x /usr/wait-for-it.sh 
```

Enter fullscreen mode Exit fullscreen mode

*   最后一步，我更改了 docker-compose.yml 中的应用程序命令:

```
app:
    container_name: backend
    restart: always
    build: .
    volumes:
        - .:/usr/src/app
        - /usr/src/app/node_modules
    ports:
        - "3030:3030"
        - "9229:9229"
    depends_on:
        - mongo
    command: bash -c "/usr/wait-for-it.sh --timeout=0 mongo:27017 && npm run dev" 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！我的后端应用程序一直等到 mongodb 准备好连接。故事结束！:)