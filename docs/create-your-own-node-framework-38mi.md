# 创建自己的节点框架

> 原文：<https://dev.to/theashraf/create-your-own-node-framework-38mi>

我创建了一个简单的节点框架，只是为了理解像 express 这样的节点框架背后的思想。

检查这个回购协议中的代码。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [ theashraf ](https://github.com/theashraf) / [裸节点](https://github.com/theashraf/bare-node)

### 一个简单的节点框架

<article class="markdown-body entry-content container-lg" itemprop="text">

# 简单节点框架

## 什么

*   一个简单的零依赖节点框架
*   实现中间件模式的 Express like 框架
*   仅用于演示目的，不用于生产(可能以后)

## 为什么

了解节点框架背后的思想，特别是 express(最流行的节点框架)

## 怎么

*   中间件处理正在使用责任链模式实现，检查`lib/App.js`以查看实现细节
*   通过添加一些更容易发送响应的 util 方法来扩展`http.ServerResponse`对象的功能，在将响应发送到客户端之前，它使用构建器模式来构建响应，检查`lib/Response.js`
*   添加`BodyParser`中间件，该中间件通过监听`data`事件上的`http.IncommingMessage`来解析请求体，并保持连接传入的缓冲区块，直到`end`事件触发，然后它将在将`body`对象添加到`req`后调用下一个中间件，因此稍后我们可以在我们的…

</article>

[View on GitHub](https://github.com/theashraf/bare-node)