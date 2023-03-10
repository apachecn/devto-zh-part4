# Python 中的 Web 抓取 101

> 原文：<https://dev.to/scrapingbee/web-scraping-101-in-python-5aoj>

在这篇文章中，我们将介绍 Python 为你提供的几乎所有网络抓取工具，这篇文章可以作为我们[终极网络抓取指南](https://www.daolf.com/posts/avoiding-being-blocked-while-scraping-ultimate-guide/)的后续文章。我们将从最基础的到最先进的，并涵盖每一个的优点和缺点。当然，我们不可能涵盖我们讨论的每个工具的所有方面，但是这篇文章应该足以让我们对哪些工具做什么以及何时使用哪些工具有一个很好的了解。

注意:当我在这篇博文中谈到 Python 时，你应该认为我说的是 Python3。

## 目录:

*   0) [网络基础](#web-fondamentals)
*   1) [手动打开套接字，发送 HTTP 请求](#socket)
*   2) [urllib3 & LXML](#lxml)
*   3) [请求&美丽组](#requests)
*   4) [刺儿头](#scrapy)
*   5) [硒&铬——无头](#selenium)
*   [结论](#conclusion)

# 0)网络基础

互联网真的很复杂:在浏览器中浏览一个简单的网页涉及到许多基础技术和概念。我不想解释所有的事情，但是我会告诉你为了从网络中提取数据你必须了解的最重要的事情。

## 超文本传输协议

HTTP 使用**客户机/服务器**模型，其中 HTTP 客户机(浏览器、Python 程序 curl)请求...)打开一个连接并向 HTTP 服务器(Nginx，Apache)发送一条消息(“我想看那个页面:/product”)...).

然后服务器用一个响应(例如 HTML 代码)来回答，并关闭连接。HTTP 被称为无状态协议，因为每个事务(请求/响应)都是独立的。例如，FTP 是有状态的。

基本上，当您在浏览器中键入网站地址时，HTTP 请求如下所示: