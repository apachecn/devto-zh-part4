# alpine linux 中的 node-gyp 支持

> 原文：<https://dev.to/grigorkh/node-gyp-support-in-alpine-linux-4d0f>

最初发表于[媒体](https://medium.com/devgorilla/node-gyp-support-in-alpine-linux-9374c6191140)

当使用 alpine 时，您需要为一些节点模块安装构建依赖项，以便能够在本地构建。
下面是一个例子，说明如何在 alpine 变体上为需要 node-gyp 支持的包安装依赖项:

```
FROM node:alpine

RUN apk add --no-cache --virtual .gyp python make g++ \
    && npm install [ your npm dependencies here ] \
    && apk del .gyp 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个多阶段构建的例子:

```
FROM node:alpine as builder

## Install build toolchain, install node deps and compile native add-ons
RUN apk add --no-cache python make g++
RUN npm install [ your npm dependencies here ]

FROM node:alpine as app

## Copy built node modules and binaries without including the toolchain
COPY --from=builder node_modules . 
```

Enter fullscreen mode Exit fullscreen mode

## 亲提示:

当 Alpine Linux 使用`musl`时，您可能会遇到一些期望类似`glibc`行为的环境问题——特别是如果您试图使用用`glibc`编译的二进制文件。您应该重新编译这些二进制文件以使用`musl`(在 Alpine 上编译可能是最简单的方法)。
如果你得到一个类似于错误加载共享库`ld-linux-x86-64.so.2`的错误，可能是你有依赖`libc`的依赖——你可以试着通过添加
来解决这个问题

```
RUN apk add --no-cache libc6-compat 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
RUN ln -s /lib/libc.musl-x86_64.so.1 /lib/ld-linux-x86-64.so.2 
```

Enter fullscreen mode Exit fullscreen mode

去你的档案。

## 喜欢学习？

在 twitter 上关注我，我会在这里发布所有最新最棒的 JavaScript、AI、DevOps、VR/AR、技术和科学！也在 [LinkedIn](https://www.linkedin.com/in/grigorkh/) 上和我联系吧！