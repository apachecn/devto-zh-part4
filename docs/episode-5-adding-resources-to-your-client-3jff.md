# 第 5 集-为您的客户添加资源

> 原文：<https://dev.to/victoravelar/episode-5-adding-resources-to-your-client-3jff>

> 在阿尔卑斯山呆了几天后，我们又开始了。

## 什么是资源？

根据[https://restful-API-design . readthedocs . io](https://restful-api-design)资源描述如下:

> 任何 RESTful API 中的基本概念都是资源。资源是具有类型、相关数据、与其他资源的关系以及对其进行操作的一组方法的对象。它类似于面向对象编程语言中的对象实例，重要的区别是只为资源定义了几个标准方法(对应于标准的 HTTP GET、POST、PUT 和 DELETE 方法)，而对象实例通常有许多方法。

这意味着当添加一个资源时，这将允许我们与我们的客户在特定的和良好定义的类型上执行操作。

## 如何给客户端添加资源？

1.  我们创建一个资源类型来保存与文章交互的方法，这将包含一个指向我们的`devto.Client`实现的指针，

```
// articles.go

type ArticleResource struct{
    API *Client // this will receive a pointer to your client instance
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们使用客户端构造函数来附加它

您可以在资源库中看到一个工作示例

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[devto-API-go](https://github.com/VictorAvelar/devto-api-go)

### 开发到 API 的 go 客户端

<article class="markdown-body entry-content container-lg" itemprop="text">

# 开发应用编程接口

开发到 API 的 go 客户端

[![License: MIT](img/db85796de36d003cae919db6f133f455.png)](https://opensource.org/licenses/MIT)

#### 特拉维斯·CI

[![Build Status](img/2f1b58bd484d692482b6dfbc5f4cf54d.png)](https://travis-ci.org/VictorAvelar/devto-api-go)

#### 仔细或彻底检查

[![Scrutinizer Build Status](img/dd26fac85af7836c178831bb4174749a.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/build-status/master)[![Scrutinizer Code Quality](img/4e95ffe53e71af50f0cf446342069fdf.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/?branch=master)[![Code Coverage](img/4b24d605b035f3712c4061b10d4c59ed.png)](https://scrutinizer-ci.com/g/VictorAvelar/devto-api-go/?branch=master)

#### Go 生态系统

[![Go Report Card](img/9b9b43938b0abe1ad6b7bf883b9ce923.png) ](https://goreportcard.com/report/github.com/VictorAvelar/devto-api-go) [ ![GoDoc](img/8074e2e7c6159296ccb342c7f3aea036.png)](https://godoc.org/github.com/VictorAvelar/devto-api-go)

## 路标

*   基本客户端和配置
*   文章资源
*   CLI 实用程序
*   认证资源
*   扩展测试套件
*   提供代码示例

## 放弃

这个库不是一个官方的开发者 API 库，而是对开发者社区的友好贡献

</article>

[View on GitHub](https://github.com/VictorAvelar/devto-api-go)

从现在开始，我们要做最后一部分。