# 使用。网络核心

> 原文：<https://dev.to/alistairjevans/easily-loading-lots-of-data-in-parallel-over-http-using-dataflow-in-net-core-1k9h>

[![](img/58596504bde6492bec54ba6a2b080365.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NyR0ZaPF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0tbfb62dbqv823q799v8.PNG)

我最近有一个需求，要尽可能快地将大量数据加载到应用程序中。

问题中的数据是大约 100，000 笔交易，逐行存储在一个文件中，需要通过 HTTP 发送到一个 web 应用程序，该应用程序将对其进行处理并将其加载到数据库中。

这其实很容易。NET，并且使用 async/await 非常高效: