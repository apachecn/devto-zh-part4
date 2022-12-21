# 用域保存 verify mailgun 域。谷歌

> 原文：<https://dev.to/ilumin/verify-mailgun-domain-domain-google-1kp6>

## [t1㎡来源于何处](#%E0%B8%97%E0%B8%B5%E0%B9%88%E0%B8%A1%E0%B8%B2%E0%B8%97%E0%B8%B5%E0%B9%88%E0%B9%84%E0%B8%9B)

*   为客户制作网站 campaign
*   我们和客户都没有服务器
*   梅勒根以付费的方式发送-只使用多少付费
*   我们买了一个域名。google 放了一个

## 为什么选择邮箱

优势

*   在 environment dev 中，我们可以免费发送，不需要 billing，但仅限于发送在我们的邮件群 accoun 中做 verify 的电子邮件。
*   多域名可以建立，适用于多个客户。
*   所有域都使用相同的密钥 API

缺点

*   当产品真正上市的时候，需要做真正的 verify domain。
*   每个域都没有 limit usage

其他

*   没时间看其他的你，早些时候认识和利用梅尔根。

## 怎么办

1.  在 mailgun 中添加新的域
2.  把 mailgun 的 DNS 记录放在域中
3.  进入域 google 直逼顶端，按“我的域”。
4.  我们将看到域列表，按选择“管理”。
5.  在左侧选择“DNS”
6.  通过选择动态 DNS 在合成记录中添加子域
7.  添加自定义资源记录
8.  type = TXT，target = subdomain，text = "ตาม邮件枪"
9.  类型= TXT，目标= pic。_domainkey.subdomain，text = "ตาม邮件枪"
10.  type = MX，target = subdomain，text = " mxa . eu . mail gun . org "+" mxb . eu . mail gun . org "
11.  type = CNAME，target = email.subdomain，text = "eu.mailgun.org "

下一步是等待。..大约 45 分钟，
如果 DNS 仍未完成，或者我们填写的值有问题，mailgun 将一直转到“verify 域”页面和“young”。