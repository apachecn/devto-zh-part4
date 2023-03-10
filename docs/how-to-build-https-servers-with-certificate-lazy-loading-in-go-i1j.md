# 如何在 Go 中构建证书延迟加载的 https 服务器

> 原文：<https://dev.to/satrobit/how-to-build-https-servers-with-certificate-lazy-loading-in-go-i1j>

近年来，Go 在服务器程序员中变得非常流行，随着这种流行趋势的持续，一些特定的需求出现了。

在本文中，我们将讨论 Go 在 https 服务器、反向代理中的使用以及托管大量域时出现的问题。

### 问题

假设我们要构建一个反向代理，它托管大量的域，比如 CDN 的边缘服务器。

普通 HTTP 比 HTTPS 容易处理得多，所以我不打算在本文中讨论它。

有了之前关于 [TLS 握手](https://www.cloudflare.com/learning/ssl/what-happens-in-a-tls-handshake/)如何工作的知识，我们知道在某个时候我们的服务器会向客户端发送一个公钥。该密钥特定于客户端看到的域。

所以问题是如何在单个 IP/端口上为大量拥有不同证书的网站提供服务。

<figure>[![TLS Handshake](img/1f7fd9a5252622ef4be985b7b53f117a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7MvMRT28--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/779/0%2AwsiEvbqQ6S2KTDjs.png) 

<figcaption>TLS 握手</figcaption>

</figure>

### 解

在 [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication) 出现之前，我们受限于一个 IP 或端口来托管一个网站(是的，多域证书是可用的，但它们是有限的且不可扩展)。

什么是 [SNI](https://en.wikipedia.org/wiki/Server_Name_Indication) ？基本上，我们在 TLS 握手完成之前获得服务器名称。所以现在我们知道根据我们收到的服务器名称发送什么证书。让我们开始编码吧。

Go 中的“crypto/tls”包已经为我们提供了这个场景中需要的一切。我们做的第一件事是用自定义配置创建一个 TLS 监听器。

```
config := &tls.Config{
    GetCertificate: returnCert,
}

ln, err := tls.Listen("tcp", ":443", config) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们提供了一个名为“returnCert”的函数来获取配置类型中的证书。这基本上是告诉 TLS 侦听器在 TLS 握手开始时回调我们的函数，以便我们可以为您提供正确的证书。

之后，我们可以简单地在 TLS 侦听器上创建一个 https 服务器。

```
http.HandleFunc("/", func(w http.ResponseWriter, r \*http.Request) {

    fmt.Fprintf(w, "Hello TLS!")
})

http.Serve(ln, nil) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们植入“returnCert”函数。我们的回调函数接收 ClientHelloInfo，它包含关于客户端支持的密码套件的信息，最重要的是 ServerName。

我不会详细介绍如何存储和加载证书文件，但一个简单的场景是将证书文件存储在数据库甚至文件系统中，然后在加载时缓存它们，这样我们就不必每次都加载它们。

在这个示例代码中，我直接从文件系统加载文件。尽管这不是一个好的生产设计！

```
func returnCert(helloInfo \*tls.ClientHelloInfo) (\*tls.Certificate, error) {
    //helloInfo.ServerName ( This contains our Server Name )

    cer, err := tls.LoadX509KeyPair("server.crt", "server.key")

    if err != nil {

        log.Println(err)
        return nil, nil

    }

    return &cer, nil

} 
```

Enter fullscreen mode Exit fullscreen mode

这是本文的全部代码: