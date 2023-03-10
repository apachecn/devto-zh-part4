# 在 GO 中解析 JSON

> 原文：<https://dev.to/petercour/parsing-json-in-go-p87>

[![](img/854cc751085f774a35410f8229cfd637.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--764Yqoar--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/prhzpctpv1zzdgoycv21.jpg)

JSON 无处不在。许多网站都用它来通过 API 传递数据。如果你向一个 API 发送一个请求，它通常要么是 JSON 格式，要么是 XML 格式(讨厌！)

JSON 是使用括号和引号的格式良好的数据。计算机更容易解析..或者是？

在 Go 中很难解析 JSON。对一个新手来说。为了使它更容易，您可以使用结构。但是如果你有复杂的数据，写一个结构就很麻烦了。

没有更简单的方法吗？是的，有。

所以有一个工具可以用来将 JSON 转换成 Golang 结构:[https://mholt.github.io/json-to-go/](https://mholt.github.io/json-to-go/)

[![](img/ba5c7ce2faa842bf1314c60b4c0fb082.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZkCu7vqC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zq9o0xr4jh2kfw29flvd.png)

在左边粘贴你的 JSON。在右边，将创建一个 Golang 结构。将该结构复制到 golang 代码中。

然后使用“encoding/json”模块来解析 json 数据，就像这样[https://golangr.com/json/](https://golangr.com/json/)

还有你的布景！