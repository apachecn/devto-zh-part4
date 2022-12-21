# 拉勒维尔雄辩模型 UUID 路线钥匙

> 原文：<https://dev.to/ryancco/laravel-eloquent-model-uuid-route-keys-4efc>

不讨论包概念的原因，URL 中的自动递增键经常被认为是一种反模式、安全缺陷，并且通常是丑陋的(见这里的、这里的、这里的。

[Laravel UUID 模型](https://github.com/ryancco/laravel-uuid-models)提供了一个特性，当应用于 Laravel 雄辩模型时，无缝处理允许它使用 UUID 路由(而不是自动增加 ID)。

此外，生成[时间排序 UUID](https://www.percona.com/blog/2014/12/19/store-uuid-optimized-way/) 、验证、类型转换或编辑任何现有路线或链接*不需要进一步的代码更改。它就像安装软件包一样简单，将特征放在您想使用 UUID 路由键的雄辩模型上，并添加一个迁移来存储 UUID 字段。

在[存储库](https://github.com/ryancco/laravel-uuid-models)上，任何建议或改变总是受到鼓励和欢迎。

**所有路线都将工作，假设它们是由`route()`助手生成的。*