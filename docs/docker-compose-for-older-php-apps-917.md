# docker-为旧的 PHP 应用程序编写

> 原文：<https://dev.to/horaciodegiorgi/docker-compose-for-older-php-apps-917>

迁移 php 中的旧应用程序有时很复杂，因为只适用于旧版本的 php。我有一个适用于 php 5.2 的旧应用程序(有些库非常旧)，我需要一个开发环境。在做一些调查时，我发现了 http://devilbox.org/的 T2。
一个拥有大量选项和文档的 docker 图片。
我的应用程序也需要 postgresql 和一些 php 扩展。在一个简单的 docker-compose 食谱中，我找到了我需要的一切。