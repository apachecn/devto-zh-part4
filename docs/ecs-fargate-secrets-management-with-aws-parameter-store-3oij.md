# 具有 AWS 参数存储的 ECS Fargate 秘密管理

> 原文：<https://dev.to/farrukhnaeem14/ecs-fargate-secrets-management-with-aws-parameter-store-3oij>

我有一个 node.js 应用程序，它显然有一些需要在配置文件中管理的秘密/配置。我正在考虑将配置传递给我的 Fargate 任务定义的 AWS 参数存储选项，正如 [@sagarjauhari](https://dev.to/sagarjauhari) 在他的第三个秘密管理方法中所解释的那样:[https://dev . to/sagarjauhari/3-ways-to-handle-secrets-in-AWS-ECS-tasks-20lb](https://dev.to/sagarjauhari/3-ways-to-handle-secrets-in-aws-ecs-tasks-2olb)

我只是不能理解我的应用程序如何在容器中运行，如何获取这些秘密，比如数据库密码等等。

将 SSM 秘密传递给任务定义会自动使这些秘密在容器内部可用吗？如果是，它们在哪里，如何在我的应用程序中访问它们？