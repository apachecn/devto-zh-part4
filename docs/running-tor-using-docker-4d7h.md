# 使用 docker 运行 tor

> 原文：<https://dev.to/boris/running-tor-using-docker-4d7h>

如果你想在不使用 tor 浏览器的情况下连接到 [tor](https://www.torproject.org/) 网络，docker 就是解决方案！

只需将这段代码复制到 Dockerfile:

```
FROM alpine:edge

RUN apk update
RUN apk add tor

RUN echo "Log notice stdout" >> /etc/torrc
RUN echo "SocksPort 0.0.0.0:9150" >> /etc/torrc

EXPOSE 9150

CMD tor -f /etc/torrc 
```

Enter fullscreen mode Exit fullscreen mode

用`docker build -t tor -f Dockerfile .`构建它，用
运行它

```
docker run -d --rm --name tor -p 9150:9150 tor 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果您不想进行构建，只需运行:

```
docker run -d --rm --name tor -p 9150:9150 boris/tor:alpine 
```

Enter fullscreen mode Exit fullscreen mode

之后，您将有一个 tor 代理在您的`127.0.0.1:9150`上运行，因此继续配置您选择的浏览器，在`127.0.0.1:9150`上使用 SOCKS 代理，您将从这开始:

[![](img/7911c3450beee2e29a5fe77e8a1f746e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YzQ9vKF0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i8mao2ihncce537fiwxm.png)

对此:

[![](img/2a7c01c6706ce39607e824d2213d50aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dZiaJ6of--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jtoud1vithoe2431khmv.png)

享受您的隐私:)

PS:我的 Docker 文件的代码是[这里是](https://github.com/boris/docker/blob/master/tor/Dockerfile-alpine)，Docker hub 中的图像是[这里是](https://hub.docker.com/r/boris/tor)