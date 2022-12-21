# 要求开发人员与 flask+sqlalchemy 进行斗争

> 原文：<https://dev.to/yev/ask-dev-to-struggle-with-flask-sqlalchemy-1e33>

你好！需要德夫·托的帮助吗🙏最近我开始用 flask+sqlalchemy(postgres+pgbouncer)+guni corn 为我的项目实现一个后端。问题是，它每秒只能处理大约 1 个请求...我开始使用 gunicorn 配置并设置了 5 个 gevent 工人，🙈猴子修补了 psycorp 2(postgres 的驱动程序)。没用。好吧，也许 sqlalchemy 阻塞了主线程，应用程序性能很差。所以我换了 5 个同步工人。现在它至少应该给我 5 RPS...这次不会。经过一些调查，我发现瓶颈在我的 web 应用程序和数据库之间。pg bouncer (47 个连接)和 postgres 是在数字海洋上托管的，所以我相信这些家伙为我做了正确的设置。唯一剩下的是 sqlalchemy 和它如何处理会话(以及我写代码的手)。也许有人有类似的问题，或者可以提供建议，在哪里寻找进一步？先谢谢你，你太棒了💚