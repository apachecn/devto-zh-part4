# 实用锈网开发

> 原文：<https://dev.to/werner/practical-rust-web-development-cors-4kg>

为了让我们的生活更轻松，继续系列，让我们做一个声明，我们的 API 将是公共的，任何人都可以通过令牌连接到我们，所以，这意味着我们应该修改我们处理 Cors 的方式，允许所有的访问。

我们需要修改主文件来处理 cors 配置中的所有请求。

`src/main.rs` :

```
 cors::Cors::new()
                .send_wildcard()
                .allowed_methods(vec!["GET", "POST", "PUT", "DELETE"])
                .allowed_headers(vec![header::AUTHORIZATION,
                                      header::CONTENT_TYPE,
                                      header::ACCEPT,
                                      csrf_token_header.clone()])
                .expose_headers(vec![csrf_token_header.clone()])
                .max_age(3600) 
```

[源代码中的引用](https://github.com/practical-rust-web-development/mystore/blob/v3.2/src/main.rs#L33)