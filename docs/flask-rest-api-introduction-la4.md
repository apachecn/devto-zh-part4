# 烧瓶支架 API:简介

> 原文：<https://dev.to/dbanty/flask-rest-api-introduction-la4>

作为一名专业开发人员，我所做的大部分工作都涉及到使用 Python 和 Flask 的后端服务。我已经尝试了许多不同的方法和技术的组合，并提出了一种在大多数情况下都很有效的模式。在本系列的整个过程中，我将尽最大努力向您介绍构建 web 服务时的不同考虑事项，并解释我做出这些选择的原因。最后，我希望既有 Flask 开发人员的成功指南，又有可以用来启动新的 web 开发项目的模板。

# 概述

这里是我计划涉及的所有与 web 服务相关的主题。我将给出我如何解决每个问题的具体细节，但我也会尝试给出通用的建议，不管你使用的是什么语言/框架。在我写作的过程中，有些可能会改变。我可能会根据对评论的兴趣添加或删除一些东西。如果你认为我在这里遗漏了什么，请告诉我，这样我就可以在开始写的时候补充了！我还计划在 GitHub 上公开所有的代码。

1.  基础:构建一个随时可以增长的应用。
    1.  基本的、可持续的包装结构
    2.  依赖性管理
    3.  本地运行服务
    4.  与 ide 集成
    5.  基本单元测试
2.  持续集成:尽早发现错误
    1.  自动化测试
    2.  静态分析
    3.  依赖性漏洞
    4.  样式检查
3.  认证:知道谁在使用你的应用
    1.  安全存储和检查凭证
    2.  使用令牌来识别用户
    3.  对所有资源实施身份验证
    4.  围绕认证的测试(模仿/伪造)
4.  数据库:存储和访问数据
    1.  ORMs
    2.  用于测试的一次性数据库
    3.  迁移(跟踪模式更改)
5.  API 设计:使您的服务易于使用
    1.  定义 API 资源
    2.  基本操作
    3.  API 规范和开发 UI
    4.  测试 API
6.  授权:确保人们只能访问他们的数据
    1.  可扩展权限的框架
    2.  在端点上实施授权检查
7.  异步任务:不要降低用户的速度
    1.  从请求启动异步作业
    2.  运行定期任务
    3.  电子邮件通知
8.  生产准备:增加稳定性
    1.  生产服务器
    2.  数据库技术
    3.  集装箱(码头工人)
    4.  安全标题
    5.  版本管理(变更日志)
    6.  记录
9.  部署:将您的应用程序发布出去
    1.  基础设施作为代码
    2.  域名服务器(Domain Name Server)
    3.  负载平衡
    4.  管理秘密
    5.  多重环境
    6.  出现问题时的警报/通知
    7.  手动和持续部署
10.  消费 API:实际使用这个东西
    1.  使用用户界面
    2.  使用 API 工具(Postman)
    3.  生成客户端库