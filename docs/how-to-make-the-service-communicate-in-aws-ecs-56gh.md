# 如何让 AWS ECS 中的服务进行通信？

> 原文：<https://dev.to/taragrg6/how-to-make-the-service-communicate-in-aws-ecs-56gh>

我在 AWS ECS 上运行了多个服务。现在，我尝试使用内置的服务发现将一个服务与另一个服务连接起来。在创建服务时，它成功地创建了一个 SRV 并映射到 Route53 中的一个记录。

问题是我无法使用服务发现端点进行连接。*它没有解析名称*

示例:

服务 1:运行任务定义:运行 nginx-phpfpm
服务 2:运行任务定义:运行 redis

目标是使服务 1 与服务 2 通信