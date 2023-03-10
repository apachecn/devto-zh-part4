# 如何使用 Cloudflare 保护您的 AWS S3 桶

> 原文：<https://dev.to/joshuagilless/how-to-secure-your-aws-s3-bucket-with-cloudflare-4m14>

我注意到很多人在 S3 的自动气象站上托管他们的网站，前面有 T2 的云闪。这是一个很好的选择，因为对于单个开发人员来说，它既便宜又易于维护。由于小的[攻击面](https://en.wikipedia.org/wiki/Attack_surface)，静态站点在默认情况下是相当安全的。

S3 允许您托管桶中的网站，条件是您的桶名称与您托管的网站的 URL 相同。例如，我的网站是[https://www.joshuagilless.com](https://www.joshuagilless.com)，所以要在那个位置托管一个网站，s3 存储桶应该是`www.joshuagilless.com`。这意味着你放进桶里的所有东西都有一个*可预测的 URL 模式*。他们有一个很棒的设置静态网站属性的指南。

在该指南中，您被指示给予您的 bucket [*公共读取权限*](https://aws.amazon.com/premiumsupport/knowledge-center/read-access-objects-s3-bucket/) 。这意味着有了*公共阅读权限*和*可预测的 URL 模式*，任何街头的老小丑都可以绕过你的网站，直接进入你的 s3 存储桶。问题是，如果您继续阅读本系列的下一个[指南](https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-custom-domain-walkthrough.html)，用自定义域设置您的存储桶，它仍然会指示您放置一个*公共读取访问*存储桶策略。

接下来，在亚马逊指南系列中，你有[个指令](https://docs.aws.amazon.com/AmazonS3/latest/dev/website-hosting-cloudfront-walkthrough.html)来添加 [AWS Cloudfront](https://aws.amazon.com/cloudfront/) 。Cloudfront 是一个 CDN，而且是一个优秀的 CDN。这篇文章中的所有技巧都适用于 Cloudfront 和 Cloudflare。关于指令的事情，他们没有提到删除*公共读取权限*。因此，即使是官方指南也忽略了安全问题的这一部分。

### 安全

锁定 ACL(访问控制列表)为我们提供了 Cloudflare 承诺的所有保护。

想象一下这样一个场景，有人决定给你一大笔托管费用，然后开始直接从你的 S3 桶中请求对象。他们可以找到你托管的最大的文件，然后一遍又一遍地请求它，直到你开始从 AWS 那里得到一大笔带宽费用。Cloudflare 的 DDOS 保护可以缓解这一问题。

那么你是怎么做到的呢？

1.  在不允许公众阅读的情况下上传东西。
2.  删除允许所有内容公共读取访问的整体存储桶规则。
3.  创建存储桶策略

默认的上传策略是不允许公共读取访问，但是很多指南都提到要覆盖它，这样你就可以从一个方便的 URL 访问文件。重要的是不要覆盖它，这样当你试图访问它时，你会得到一个 403 禁止错误。如果你要做一个新的桶，就用默认的吧。

为了创建一个 bucket 策略，AWS 会给你一个关于添加 bucket 策略的[演练。](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/add-bucket-policy.html)

所以我们需要做的是给它一个 PublicReadGetObject:
的语句

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<your-domain>/*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": [
                        list,
                        of,
                        allowed,
                        ip,
                        addresses
                    ]
                }
            }
        }
    ]
} 
```

这里需要替换两件东西:

1.  “aws:SourceIp”括号之间是什么。
2.  `<your-domain>`用你实际的域名。例如，我会用`www.joshuagilless.com`代替`<your-domain>`

要了解 Cloudflare 的更新列表，您可以访问 https://www.cloudflare.com/ips-v4 的[和 https://www.cloudflare.com/ips-v6 的](https://www.cloudflare.com/ips-v4)[并添加这些列表。](https://www.cloudflare.com/ips-v6)

我从标题为“允许 IPv4 和 IPv6 地址”的章节中了解到，我们可以混合使用 IPv4 和 IPv6 地址。只要格式正确，它们的排列顺序并不重要。

### 给我点东西复制粘贴就行了

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<your-domain>/*",
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": [
                        "173.245.48.0/20",
                        "103.21.244.0/22",
                        "103.22.200.0/22",
                        "103.31.4.0/22",
                        "141.101.64.0/18",
                        "108.162.192.0/18",
                        "190.93.240.0/20",
                        "188.114.96.0/20",
                        "197.234.240.0/22",
                        "198.41.128.0/17",
                        "162.158.0.0/15",
                        "104.16.0.0/12",
                        "172.64.0.0/13",
                        "131.0.72.0/22",
                        "2400:cb00::/32",
                        "2606:4700::/32",
                        "2803:f800::/32",
                        "2405:b500::/32",
                        "2405:8100::/32",
                        "2a06:98c0::/29",
                        "2c0f:f248::/32"
                    ]
                }
            }
        }
    ]
} 
```

同样，只需将`<your-domain>`替换为您实际的 bucket 名称和网站 URL。

任何时候你在生产中使用 CDN，你都可以做类似的事情来限制对你不想暴露的位置的访问。仅允许 CDN 的 IP 地址的相同概念适用于所有资源，而不仅仅是 S3 桶中的资源。

我希望你觉得这很有用，我花了一段时间才意识到我应该这样做，所以我离开我的桶太久了。没人喜欢不安全的桶。