# 如何用 expressjs 初始化 Singleton mongo 连接

> 原文：<https://dev.to/perigk/how-to-initialize-a-singleton-mongo-connection-with-expressjs-4a2f>

我试图用 app/server.js 文件中的 expressjs 以异步方式初始化 mongo Singleton 连接。我想使用本地驱动程序，而不是猫鼬或类似的东西。

整个互联网充满了坏模式(IMHO ),每个请求都会打开一个新的连接。

有没有人尝试过前者并获得成功？任何链接或建议将不胜感激。

例如

`// app.js
let client = await initMongoConnection()`