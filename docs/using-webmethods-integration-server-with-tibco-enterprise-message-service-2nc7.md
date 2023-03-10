# 将 webMethods 集成服务器与 TIBCO 企业消息服务结合使用

> 原文：<https://dev.to/techcommunity/using-webmethods-integration-server-with-tibco-enterprise-message-service-2nc7>

*乔纳森·海伍德，产品管理软件公司经理*

#### 目录 [[-]](https://dev.tojavascript:;)

*   0.0.1 抽象集成服务器(IS)支持使用最广泛的 JMS 提供者发送和接收消息。本文描述了如何配置到 TIBCO 企业消息服务的 JMS 连接，这在标准文档中没有描述。
*   0.0.2 JNDI 配置按如下方式添加 JNDI 提供商别名:
*   0.0.3 JMS 配置为了配置 JMS 连接，请执行以下操作:
*   0.0.4 身份验证如果 Tibco 服务器需要身份验证，则需要在 JNDI 和 JMS 设置中进行配置，否则您将收到类似以下内容的错误:
*   0.0.5 是触发器要通过 JMS 触发器接收文档，请将目的地名称设置为您希望订阅的队列名称。

集成服务器(IS)支持使用最广泛使用的 JMS 提供者发送和接收消息。本文描述了如何配置到 TIBCO 企业消息服务的 JMS 连接，这在标准文档中没有描述。

**适用性**

产品:集成服务器

版本:7.1.x、8.0、9.12

#### abstract integration Server(IS)支持使用最广泛的 JMS 提供者发送和接收消息。本文描述了如何配置到 TIBCO 企业消息服务的 JMS 连接，这在标准文档中没有描述。

```
Important Note: Software AG has not formally tested and explicitly does not support this type of connection with Tibco JMS. The configuration described here has been found to work in the field and anyone using this configuration does so at their own risk. 
```

Enter fullscreen mode Exit fullscreen mode

#### JNDI 配置添加 JNDI 提供商别名，如下所示:

*   将以下 jar 文件从 Tibco 复制到 IntegrationServer\lib\jars
    *   tibjms.jar
    *   tibjmsapps.jar
*   重启是
*   在“设置>信息> JNDI 设置”中，完成以下操作:
    *   输入您选择的名称/描述
    *   输入初始上下文工厂:com . tibco . TiB JMS . naming . tibjmsinitialcontextfactory
*   输入提供商 URL:
    *   tibjmsnaming: *<主机名> :7222*

注意:测试按钮将不起作用，而是返回一个 javax . naming . operationnotsuportedexception。这是因为 Tibco 没有为 JNDI 上下文类实现 list()方法。然而，除了测试按钮之外，连接应该正常工作。

#### JMS 配置为了配置 JMS 连接，请执行以下操作:

*   将以下 jar 从 Tibco 复制到您打算在其中创建 JMS 连接的 IS 包的 IntegrationServer\packages(包名)\code\jars 中:
    *   tibjms.jar
    *   tibjmsapps.jar
*   注意:这些 jar 文件需要存储在 lib/jars 目录和包中，这是正确的。
*   在设置>消息传递> JMS 设置> JMS 连接别名中，完成以下操作:
    *   输入连接别名
    *   输入您选择的描述
    *   选择交易类型为**否 _ 交易**
    *   使用包类加载器创建连接
    *   选择 JNDI 代码
    *   JNDI 提供商别名:
    *   选择您之前创建的 Tibco one
    *   连接工厂查找名称:
    *   GenericConnectionFactory(QueueConnectionFactory 不起作用)

#### 身份验证如果 Tibco 服务器需要身份验证，则需要在 JNDI 和 JMS 设置中进行配置，否则您将收到类似以下内容的错误:

*无效的名称或密码*

#### IS trigger 通过 JMS 触发器接收文档，将目的地名称设置为您希望订阅的队列名称。

本文档中的信息按原样提供，不含任何担保。软件公司不对任何不准确的结果负责。