# 9 Django 概念-第 3 部分

> 原文：<https://dev.to/steelwolf180/9-django-concepts-part-3-4n8a>

# 简介

欢迎来到 Django 开发者的 9 个 Django 概念的最后一部分。

为此，我将介绍部署、测试和支持前端框架等部分。

这是一个 Django 开发人员为基于 Javascript 的前端框架开发的项目。

如果你错过了第一部分或第二部分的[，我建议你在阅读第三部分之前去看看，不要错过它。](https://www.maxongzb.com/top-9-django-concepts-part-1-4-mins/)

# 测试

默认情况下，Django 自带基于 Python 默认测试库的[测试模块](https://docs.djangoproject.com/en/2.2/topics/testing/)。

这被称为[单元测试](https://docs.python.org/3/library/unittest.html#module-unittest)。

我个人更倾向于使用 [Pytest](https://docs.pytest.org/en/latest/) 。

由于编写没有太多**样板文件**的测试用例**很简单**，而模拟数据来重复运行测试用例很难。

关于如何使用 Django 的测试框架的教程，我建议你去看看 **Harry Perceval 的**免费在线电子书，名为[用 Python 进行测试驱动开发](http://www.obeythetestinggoat.com/pages/book.html#toc)或者他的 [PyCon workshop - Django TDD](https://youtu.be/_rLPDxpXIFc) 。

# 支撑前端框架

由于像 **Vue** 、 **React** 、 **Angular** 这样的 Javascript 前端框架的流行。

Django 越来越多地被用作后端系统来为前端提供支持。

因此你需要使用新的工具或服务来提供你最好的前端支持。

第一个库肯定会是 [Django REST 框架](https://www.django-rest-framework.org/)。

这将是你用来构建 REST APIs 的**面包&黄油**技能。

## API 开发资源

如果你正走向 Django 的 API 开发路线。

重点采用基于 [OpenAPI v3](https://swagger.io/docs/specification/about/) 的 [Postman](https://www.getpostman.com/) 或 [Swagger Editor](https://editor.swagger.io/) 等工具。

这是一个行业 API 设计规范，可以加速 API 开发过程。

[API 布道者](http://apievangelist.com/)为 API 生态系统提供大量资源。

除非你的组织或公司的前端开发人员让你讨厌使用 [GraphQL](https://graphql.org/learn/) ，只要在上面建立 REST 端点，因为它的成熟度&生态系统。

为了构建 GraphQL，有一个名为 [Graphene](https://graphene-python.org/) 的项目，它扩展了 Django 以提供 GraphQL 端点。

# 部署

这里我将列出简单和复杂的部署。

如果您真的想学习更多关于部署 Django 的知识。

只要去拿一本叫做[高性能 Django](https://highperformancedjango.com/) 的书或者 [Django 关于部署的文档](https://docs.djangoproject.com/en/2.2/howto/deployment/)来理解 Django 的部署和扩展。

## 简单

我的建议是使用云主机提供商服务，比如 Linode T1、T2 Heroku T3 或 T4 digital ocean T5。

如果只是用户数量少的个人网站或服务，就去找这些云托管服务商吧。

## 复杂

因为用于部署 Django 的各种技术非常复杂。

如果你刚刚开始使用 [apache web 服务器](https://httpd.apache.org/)的 [mod_wsgi](https://modwsgi.readthedocs.io/en/develop/) 模块，或者使用 [Nginx](https://www.nginx.com/) 上提供的 [Gunicorn](https://docs.gunicorn.org/en/latest/index.html) 或 [Uwsgi](https://uwsgi-docs.readthedocs.io/en/latest/index.html) 。

使用 [Zappa](https://github.com/Miserlou/Zappa) 是一个很好的选择。

如果你使用 AWS 来扩展 Django。

# 结论

这些是我在成为更好的 Django 开发人员的过程中遇到的概念。

如果你对 Django concepts 或这篇文章有任何疑问，请在评论区告诉我。

最后，为了结束这篇文章，PyCon SG 邀请了《扎帕》**的创作者作为主题演讲人。**

 **如果你有兴趣了解里奇·琼斯的 Zappa，你可以前往领取 2019 年 PyCon SG 的门票

如果你喜欢我的文章，请**注册**Max[冒险家简讯](http://eepurl.com/dOUoUb)获取我每周在 **Python** 、**创业**和 **Web 开发**偶然发现的牛逼内容。

你也可以**关注**我来获取我在 **Dev** 上的文章的**最新**更新

这篇文章最初发表在 Max 的博客上，在 Unsplash 上由 Jaredd Craig 拍摄

# 参考文献

*   9 Django 概念第 1 部分
*   9 Django 概念第二部分
*   9 Django 概念第二部分
*   [测试模块](https://docs.djangoproject.com/en/2.2/topics/testing/)
*   [Unittest](https://docs.python.org/3/library/unittest.html#module-unittest)
*   [Pytest](https://docs.pytest.org/en/latest/)
*   [夹具](https://www.tutorialspoint.com/pytest/pytest_fixtures.htm)
*   [使用 Python 进行测试驱动开发](http://www.obeythetestinggoat.com/pages/book.html#toc)
*   [Django REST 框架](https://www.django-rest-framework.org/)
*   [邮递员](https://www.getpostman.com/)
*   [招摇编辑](https://editor.swagger.io/)
*   [OpenAPI v3](https://swagger.io/docs/specification/about/)
*   [API 布道者](http://apievangelist.com/)
*   [图表 QL](https://graphql.org/learn/)
*   [高性能 Django](https://highperformancedjango.com/)
*   Django 关于部署的文件
*   [mod_wsgi](https://modwsgi.readthedocs.io/en/develop/)
*   Gunicorn
*   [Uwsgi](https://uwsgi-docs.readthedocs.io/en/latest/index.html)
*   [Nginx](https://www.nginx.com/)
*   扎帕**