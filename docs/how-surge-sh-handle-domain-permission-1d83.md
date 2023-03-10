# surge.sh 如何处理自定义域权限？

> 原文：<https://dev.to/voanhcuoc/how-surge-sh-handle-domain-permission-1d83>

今天我使用 [http://surge.sh](http://surge.sh) 将一个静态页面部署到一个定制域。它工作起来很有魅力，但我觉得有些奇怪，所以我反复阅读[添加自定义域](https://surge.sh/help/adding-a-custom-domain)的指南。

哦，直到现在我的笨脑子就是想不出怎么汹涌。sh 知道这个域名是属于我的帐户！为了确保这一点，我查看了 Zeit Now 的指南，他们确实有办法通过随机的域名服务器或 TXT 记录中的验证码来验证。

是什么魔法帮助她这么做的？

附言:这个评论更好地澄清了我的问题:

[![voanhcuoc profile image](img/2f6fde4a8745f5e7acf858f9753154ca.png) ](/voanhcuoc) [ Khoa Che ](/voanhcuoc) • [<time datetime="2019-07-12T23:48:33Z"> Jul 12 '19 </time>](https://dev.to/voanhcuoc/comment/d15e) 

谢谢你非常详细的解释！

但是,[surge . sh](https://surge.sh/help/adding-a-custom-domain)的指南是一个静态网站，它为每个人提供相同的 DNS 配置！我可以在不登录 surge.sh 的情况下完成 DNS 配置。都一样:

```
sub.mydomain.com   3600   IN   CNAME   na-west1.surge.sh 
```

Enter fullscreen mode Exit fullscreen mode

在你的情况下`arte-docs.netlify.com`是特定于你的。您的帐户之前获得了该子域。

就我而言，我的账户没有什么特别之处。因此，任何拥有帐户的人都可以像这样部署到我的域中:

```
surge . sub.mydomain.com 
```

Enter fullscreen mode Exit fullscreen mode

声明:我没有用别的账号查，只是好奇！