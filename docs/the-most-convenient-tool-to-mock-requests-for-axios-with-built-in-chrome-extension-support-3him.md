# 模拟 axios 请求的最方便的工具，内置 Chrome 扩展支持。

> 原文：<https://dev.to/eshengsky/the-most-convenient-tool-to-mock-requests-for-axios-with-built-in-chrome-extension-support-3him>

## 为什么需要模拟请求

*   不需要等待依赖的 web 服务开发和部署，只需要定义接口字段，前后端可以并行开发。
*   一些 web 服务可能会污染生产环境中的数据，而真实的请求不会通过模拟请求和指定您想要的响应来发送。
*   很多时候，web 服务可能会返回各种类型的响应，开发人员和测试人员需要验证它在不同的返回下是否正常工作，例如，当 web 服务状态代码为 500 时，页面可以按预期显示。通过正常操作创建数据有时会特别麻烦或困难，而使用模拟请求很方便，如果您想返回想要的结果，边界测试可以有效地完成。
*   它是测试驱动开发和自动化测试的基础。

## 首页

[https://github.com/eshengsky/axios-mocker](https://github.com/eshengsky/axios-mocker)