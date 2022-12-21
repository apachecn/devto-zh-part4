# go-react 启动器

> 原文：<https://dev.to/aesrael/go-react-starter-5g4h>

面向所有希望开始使用 go 构建 web 应用和 auth APIs 的人。

我在周末做了一个 go and react 项目，帮助您开始使用 golang 进行身份验证，包括一个 postgresql 数据库和使用 JWT 的基于令牌的身份验证。

这是网址。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [以色列](https://github.com/aesrael)/[go-postgres-react-starter](https://github.com/aesrael/go-postgres-react-starter)

### 一个 go，postgres API，带有基本的 jwt auth starter，带有一个 react 前端

<article class="markdown-body entry-content container-lg" itemprop="text">

# GO-React 启动器

[![](img/8e5277b879ce5d2486e977a0bce2c73d.png)](https://github.com/aesrael/go-postgres-react-starter.github/logo.png)

这是一个带有 react 前端的 go web 服务器的基本示例。

它使用了 [go fiber](https://github.com/gofiber/fiber) 框架

## 入门指南

### 本地运行

克隆这个库下载并安装 [golang](https://golang.org)

下载并安装 [postgres](https://www.postgresql.org/download/)

设置你的 postgres 数据库，env secrets 可以在[中修改。环境](https://github.com/aesrael/go-postgres-react-starter./server/.env)文件

*   [PostgreSQL 完全指南](https://prabhupant.medium.com/a-complete-guide-to-postgresql-e4d1cefb9866)

*   [为 Mac、Linux 和 Windows 安装 PostgreSQL】](https://medium.com/@dan.chiniara/installing-postgresql-for-windows-7ec8145698e3)

确保您已经安装了`make`。

```
make run
```

Enter fullscreen mode Exit fullscreen mode

这将启动 go 服务器 react 前端。

### 使用 docker

确保您已经安装了`docker`

```
make docker-build
make docker-run
```

Enter fullscreen mode Exit fullscreen mode

服务器在`:8081`上运行，UI 在`:3000`上运行

## 端点

| 端点 | 方法 | 身体 | 描述 |
| --- | --- | --- | --- |
| /API/会话 | 得到 | 获取用户会话 |
| /api/login | 邮政 | {电子邮件字符串，密码字符串} | 登录用户 |
| /API/注册 | 邮政 | {电子邮件字符串，密码字符串，姓名字符串} | 注册新用户 |

## 贡献的

欢迎拉取请求。对于重大的变化，请首先打开一个问题来讨论你…

</article>

[View on GitHub](https://github.com/aesrael/go-postgres-react-starter)

希望你喜欢！

欢迎投稿。干杯🎉