# 关于 Go 和 Elasticsearch 的合作方法

> 原文：<https://dev.to/kazehara/go-elasticsearch-3a4h>

# Attention

这篇文章是 2018 年 3 月 6 日写的。

# 简介

本文总结了从 Go 语言出发处理全文搜索引擎 Elasticsearch 的方法.将 Elasticsearch 与 Go 联合的方法，即使进行检索也不会出现太多的信息.而且，还有一些容易绊倒的地方.

# 通过 Docker 进行准备

在进入主题之前，原本就需要用于 Elasticsearch 和 Go 协作的方法。本文提出了 Docker 和 docker-compose 并用的协作方法。

为了 Go，准备以下的文件吧。

```
FROM golang:1.9
ENV APPNAME TestApp
RUN mkdir -p /go/src/$APPNAME
WORKDIR /go/src/$APPNAME
ADD . /go/src/$APPNAME
RUN go get -v 
```

将其设置在想要与 Elasticsearch 合作的 Go 服务器的顶级目录中。然后，制作如下的`docker-compose.yml`

```
version: '3'
services:
  app:
    build: .
    command: ["./wait-for-it.sh", "elastic:9200", "-t", "30", "--", "go", "run", "main.go"]
    volumes:
      - .:/go/src/TestApp
    ports:
      - "8080:8080"
    depends_on:
      - elasticsearch
  elasticsearch:
    image: elasticsearch
    command: elasticsearch
    ports:
      - "9200:9200" 
```

虽然只是普通的`docker-compose.yml`，但是有几个需要注意的点。因为在 Elasticsearch 服务器还没有完全启动的状态下 Go 服务器不能启动，所以使用 wait-for-it.sh 这个 shell 脚本，进行 go sarch

顺便说一下，`wait-for-it.sh`可以通过以下链接获取。

[https://github . com/vishnubob/wait-for-it/blob/master/wait-for-it . sh](https://github.com/vishnubob/wait-for-it/blob/master/wait-for-it.sh)

到此为止，用于 Elasticsearch 和 Go 协作的准备工作已经结束。下一节将介绍用于 Elasticsearch 和 Go 协作的软件包及其方法。

# 橡皮筋搜索と围棋

有几种从 Go 中调用 Elasticsearch 的方法。例如，作为 Elastic 公司的官方客户端有[go-elasticsearch](https://github.com/elastic/go-elasticsearch) ，这是 WIP。[Elasticsearch](https://github.com/olivere/elastic)

[https://godoc.org/github.com/olivere/elastic](https://godoc.org/github.com/olivere/elastic)

那么，我来做吧，在上一节构建的环境中，想要按照 GoDoc 的方式移动的话会失败。恐怕应该会显示`No Elasticsearch Node Available`。

虽然是发生这样错误的原因，但是据说因为从 Docker 容器返回的 Elasticsearch 服务器的 IP 地址是 Private 的，所以不能从外部访问[2][3].Sniffing 被关闭

```
client, err := elastic.NewClient(elastic.SetURL("http://elasticsearch:9200"))
if err != nil {
  panic(err)
} 
```

然后，按照以下步骤操作，可以向 Elasticsearch 服务器发送 REST 请求等，也可以与 Gin 等合作制作 http 客户端

```
package main
import (
   "github.com/olivere/elastic"
   "context"
)
func main() {
   client, err := elastic.NewClient(
      elastic.SetURL("http://elasticsearch:9200"),
      elastic.SetSniff(false),
   )
   if err != nil {
      panic(err)
   }
   defer client.Stop()
   resp, err := client.Get().
      Index("index").
      Type("Type").
      Id("Id").
      Do(context.Background())
   if err != nil {
      panic(err)
   }
} 
```

# 结论

以上，是在 Elasticsearch 和 Go 的合作上容易绊倒的点的总结。

# 参考文献

[1][https://medium . com/@ Leo _ hets ch/local-development-with-go-PostgreSQL-and-elastic search-in-docker-61 BC 8a 0 D5 e 66](https://medium.com/@leo_hetsch/local-development-with-go-postgresql-and-elasticsearch-in-docker-61bc8a0d5e66)

[2][https://github.com/olivere/elastic/issues/312](https://github.com/olivere/elastic/issues/312)

[3][https://qiita.com/tanan/items/9593927c5b5ae0e811ab](https://qiita.com/tanan/items/9593927c5b5ae0e811ab)