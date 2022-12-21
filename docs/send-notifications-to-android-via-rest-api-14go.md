# 通过 REST-API 向 Android 发送通知

> 原文：<https://dev.to/jmattheis/send-notifications-to-android-via-rest-api-14go>

不久前，我想在我的服务器上发生某些事件时获得通知。这可能是 SSH 登录或完成备份。

当时，我开始自主托管服务，以减少对第三方的依赖，并获得对我的数据的控制权。经过一些研究，我找不到任何具有我想要的功能的开源项目，所以我开始了自己的 Gotify。
Gotify 是一个用 Go 编写的非常简单的服务，它公开了一个 WebSocket 端点，可以用来订阅新发布的消息。Gotify 不依赖**任何**第三方服务运行，因此不使用谷歌服务向您的手机发送推送通知。

在这篇博文中，我将向您展示如何设置 Gotify 并向它发送一些消息。

## 设置 Gotify

Gotify 可以通过二进制或 Docker 启动。在本教程中，我将使用提供的 Docker 图像，因为它们很容易设置。

[hub . docker . com/r/gotify/server](https://hub.docker.com/r/gotify/server)

```
$ docker run -p 8080:80 \
             -v /var/gotify/data:/app/data \
             -e GOTIFY_DEFAULTUSER_PASS=secret \
             gotify/server:2.0.6 
```

或者通过 docker-compose:

```
version: '3'

services:
  gotify:
    image: gotify/server:2.0.6
    ports:
      - 8080:80
    volumes:
      - "/var/gotify/data:/app/data"
    environment:
      GOTIFY_DEFAULTUSER_PASS: "secret" 
```

(从`docker-compose up -d`开始)

默认情况下，Gotify 使用 SQLite 作为数据库。因此，进一步简化了设置，因为不需要单独的数据库。SQLite 应该可以很好地处理小用户群，但是如果有很多并发用户，不同的数据库可能会提高性能。此外，SQLite Gotify 支持 PostgreSQL 和 MySQL/MariaDB。

`/app/data`包含数据库文件(如果使用 SQLite)，应用程序的图像和其他东西。在本例中，目录被装载到`/var/gotify/data`。该目录应该包含在备份中。

`-e GOTIFY_DEFAULTUSER_PASS=secret`更改启动时创建的默认用户的密码。

查看[gotify.net/docs/config](https://gotify.net/docs/config)的所有配置选项(如不同的数据库设置)。

## 首次登录/定义

默认情况下，默认用户名/密码是`admin`，但是在本教程中，我们将密码改为`secret`。有了这些凭证，现在可以在 [http://localhost:8080/](http://localhost:8080/) 登录 WebUI(使用您在启动 docker 容器时指定的端口)。

在 UI 中，你可以配置不同的东西。

**客户端**:客户端是可以管理其他客户端、消息和应用的设备或应用。但是，不允许客户端发送消息。

在这种情况下，你的浏览器就是一个客户端。

**应用**:应用是只能发送消息的设备或应用。

一个应用程序可以是在重启时发出通知的 raspberry pi。

## 发送消息

你需要一个应用程序向 Gotify 发送信息。只有创建应用程序的用户才能看到它的消息。可以通过以下方式添加应用程序:

*   WebUI:登录后点击右上角的`apps`标签，添加应用
*   REST-API: `curl -u admin:secret -X POST https://yourdomain.com/application -F "name=test" -F "description=tutorial"`参见 [API-Docs](https://gotify.github.io/api-docs/)

要作为应用程序进行身份验证，您需要应用程序令牌。令牌在 REST 请求中返回，并在 WebUI 中可见。

复制令牌后，您可以简单地使用 curl、HTTPie 或任何其他 http 客户端来推送消息。

```
$ curl -X POST "http://localhost/message?token=<apptoken>" -F "title=my title" -F "message=my message" -F "priority=5"
$ http -f POST "http://localhost:8080/message?token=<apptoken>" title="my title" message="my message" priority="5" 
```

用您的应用程序令牌替换`<apptoken>`，应该是这样的:`AKTlZf.InA3uZHK`。

`priority`目前只在 android 应用中有效果，0 =不打扰 10 =非常打扰。

UI 将以纯文本形式呈现消息，也可以使用 [extras](https://gotify.net/docs/msgextras) 将其呈现为 markdown。

可以使用 [gotify/cli](https://github.com/gotify/cli) 推送消息。CLI 将 url 和令牌存储在配置文件中。

```
$ gotify push -t "my title" -p 10 "my message"
$ echo my message | gotify push 
```

安装 gotify/cli 。

## 安卓应用

虽然 WebUI 已经创建了新消息通知，Gotify 也有一个名为 [gotify/android](https://github.com/gotify/android) 的 android 应用程序。在 [F-Droid](https://f-droid.org/de/packages/com.github.gotify/) 的[游戏商店](https://play.google.com/store/apps/details?id=com.github.gotify)有售，你也可以在发布页面下载 apk [。设置非常简单，输入 Gotify 实例的 url 并登录。](https://github.com/gotify/android/releases/latest)

请注意:默认情况下，Android 会在耗尽电池电量时杀死长时间运行的应用程序。在启用电池优化的情况下，Gotify 会被杀死，你不会收到任何通知。

这里有一种方法可以禁用 Gotify 的电池优化。

*   打开“设置”
*   搜索“电池优化”
*   找到“Gotify”并禁用电池优化

* * *

...就是这样！感谢阅读我的第一篇博文(:。