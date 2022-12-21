# rocket Sam——在 AWS 中创建和部署微服务的 CLI

> 原文：<https://dev.to/nadav96/rocketsam-a-cli-to-create-and-deploy-microservices-in-aws-32g3>

大家好！

我最近发布了一个名为 Rocketsam 的新 cli，它有助于使用 SAM local 创建微服务并将其部署到 AWS，同时引入了一些新功能，例如:

每个函数的缓存(只部署修改过的函数)

支持使用 ubuntu docker 镜像在 nodejs 和 python 3 中安装依赖项

为每个功能从云观察获取 API urls 和实时日志

在函数间共享代码

每个函数的模板，而不是一个大模板文件

[https://github.com/nadav96/rocketsam](https://github.com/nadav96/rocketsam)

开发始于我使用无服务器或 AWS SAM 本地 cli 的挫折

如果您觉得这有帮助，请告诉我:)