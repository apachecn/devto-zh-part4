# 带 Netlify 的动态 DNS

> 原文：<https://dev.to/eli_xciv/dynamic-dns-with-netlify-28eb>

我不久前购买了一个远程访问我家的域名，但是它在那里呆了大约一年。所以我决定做点什么。

看着不同的动态 DNS 提供商，我真的不喜欢他们所提供的，或者它的成本是$$。我是个吝啬鬼。我用 Netlify 做了一个兼职项目，很喜欢他们提供的东西。所以我想把我的域名存放在他们那里。事实证明，他们所做的大部分(如果不是全部)都可以通过 API 获得。不幸的是，他们的 API 规范([https://open-api.netlify.com](https://open-api.netlify.com))没有我想要的。所以我去挖掘

我打开了 Chrome 调试器，查看了页面更新 DNS 记录的网络请求，结果发现这很简单。DNS 的终点是

`https://api.netlify.com/api/v1/dns_zones/example_com/dns_records`

在这个端点，您可以按照这个约定添加新记录

```
{
    dns_zone_id: "11111111111111111",
     errors: [],
     flag: null,
     hostname: "example.com",
     id: "1111111111111111111",
     managed: false
     port: null
     priority: null
     site_id: null
     tag: null
     ttl: 3600
     type: "A"
     value: "1.1.1.1"
} 
```

Enter fullscreen mode Exit fullscreen mode

要删除一条记录，你只需向
发送一个`DELETE`请求

`https://api.netlify.com/api/v1/dns_zones/example_com/dns_records/<id>`

因此，有了这些新发现的知识，我在我的 raspberrypi 上编写了一些 python 作为 cron 作业运行，用我的 home IP 更新我的 DNS。如果你愿意，你可以在这里查看代码。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)[Eli-xciv](https://github.com/eli-xciv)/[netlify-DNS-client](https://github.com/eli-xciv/netlify-dns-client)

### Netlify DNS 更新程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# netlify-DNS-客户端

Netlify DNS 更新程序

这是一个简单的脚本，可以用来更新 Netlify 的 DNS 服务上的“A”记录。

它的主要用途是在 raspberry pi (Debian Stretch)上运行。它也只真正用于动态家庭 IP 的。我可能会试着把它扩展成一个成熟的客户。

## 使用

*   `export NETLIFY_API_TOKEN="<YOUR_TOKEN>"`
*   `export NETLIFY_URL="www.example.com"`
*   运行脚本。您也可以将其作为 cron 作业运行。

## 将来的

*   扩展客户端以支持除`.com`之外的更多域
*   添加 CNAME，MX 记录等能力。
*   更好的可用性

## 贡献

*   叉它
*   更改
*   提交拉取请求
*   我会合并

</article>

[View on GitHub](https://github.com/eli-xciv/netlify-dns-client)

P.S .我在 dev.to 上潜伏了一年左右，终于决定写作了。希望每个人都会喜欢这个信息！