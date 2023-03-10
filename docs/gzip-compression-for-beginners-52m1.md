# 面向初学者的 Gzip 压缩

> 原文：<https://dev.to/ganesh/gzip-compression-for-beginners-52m1>

Gzip 是一种压缩方法，它使文件变得更小，以实现更快的网络传输。

比方说，您有一个 1 MB 大小的 HTML 页面，您可以使用 Gzip 压缩来压缩它，而不是将它直接发送给客户端。这将减小文件的大小，从而加快文件的传输速度。
一旦客户端浏览器接收到文件，它就可以解压缩并呈现该文件。

[![](img/b50640ab0122ebcdbe94a47432de4d54.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5OLSsVJP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vfnvm1vemkkgwnxa6env.png)

这适用于所有的 HTML、CSS、JS 和 JPEG 文件。Gzip 通常会将文件压缩 50%。大多数现代浏览器都支持 Gzip 压缩。如果浏览器支持 gzip，它会在发出请求时在标题中包含“Accept-Encoding: gzip，deflate”。如果标题没有这个值，您可以用老方法发送计划 HTML 页面。

[![](img/3a2a5eea52a8c133a328e858211a1970.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kuh5p0yZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8buqv9eds74q3dsrdrfh.png)

Gzip 可以不动态或静态地避免服务器负载。Gzip 不能压缩 PNG、zip 或任何其他已经压缩的格式。

* * *

在这里阅读我的 python 脚本挑战

[![ganesh](img/77d4c97559adcbb34e17945887ca1f77.png)](/ganesh) [## 100 天 100 个 Python 脚本挑战

### Ganesh Raja Apr 1 ' 191 分钟阅读

#python #100dayschallenge #automate #productivity](/ganesh/100-days-100-python-scripts-challenge-3fc)