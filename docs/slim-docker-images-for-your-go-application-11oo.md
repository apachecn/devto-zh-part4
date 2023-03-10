# 适用于您的 Go 应用程序的超薄 Docker 图像

> 原文：<https://dev.to/andrioid/slim-docker-images-for-your-go-application-11oo>

如何使用多阶段图像为您的 Go 应用程序构建一个超薄 Docker 容器。

我使用 Go 1.13 和社区模块代理来构建二进制和 Alpine 作为基础映像。它添加一个用户和组，而不是作为根用户运行。

### Dockerfile

确保用您的主文件替换“cmd/server/server.go”。

```
FROM golang:1.13 as builder

WORKDIR /app
COPY . /app
RUN CGO_ENABLED=0 GOOS=linux GOPROXY=https://proxy.golang.org go build -o app cmd/server/server.go

FROM alpine:latest
# mailcap adds mime detection and ca-certificates help with TLS (basic stuff)
RUN apk --no-cache add ca-certificates mailcap && addgroup -S app && adduser -S app -G app
USER app
WORKDIR /app
COPY --from=builder /app/app .
ENTRYPOINT ["./app"] 
```

Enter fullscreen mode Exit fullscreen mode

希望这有帮助。我写博客主要是为了给自己做记录。