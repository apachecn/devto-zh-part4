# 一个自助服务的 Raspberry Pi VPN

> 原文：<https://dev.to/ugw7g85q/a-self-service-raspberry-pi-vpn-2f45>

最近，我想在我的家庭网络上设置一个驻留在 Raspberry Pi 上的 VPN 服务器，并想与你分享我的冒险经历。虽然这更多的是一个基于基础设施和 VPN 的帖子，但我认为它可能会像我一样为一些需要安全方式回到他们家庭网络的开发人员提供一个安全的方法。此外，这是一个有趣的方式来刷基础设施的工作和周围的树莓皮！

以下是我的设置概述:

[![](img/71780d8888ec628fd398fde6c8cd7919.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TeE9Lfv---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4axdzf5le26c2a3kwikw.png)

必要的设备:

1.  树莓派(最好是 RBP2+)
2.  一个 AWS 账户(或任何其他云提供商)——在这篇文章中，我使用 AWS
3.  一个领域。

问题和背景:当我旅行和使用公共网络时，我希望有一种安全的方法连接回我的家庭网络。VPN 公司是一种选择，但我希望如下:

1.  能够更新和检查我在家庭网络上运行的物联网项目。
2.  成本- VPN 公司拥有遍布全球的强大服务器。不过，他们每月都会产生费用，RBP 选项要便宜得多(每月总计约 2.80 美元)，包括能源和 AWS 托管区域的费用。
3.  我有一个 RBP 躺在附近——为什么不呢！

解决方案:在我的家庭网络上运行的 PiVPN 允许我同时访问我的家庭网络和互联网。我的 VPN 端点(RBP)保持在线，因为我每 24 小时运行一次 Cron 作业来更新 AWS Route53 上托管区域的 A 记录。

1.  我设置了 RBP:更新 Linux 并把登录密码改为非常强的密码(安全总是！).我更进一步，为了额外的安全性，只为 SSH 启用了 PEM 密钥登录。

2.  我将路由器设置为在随机端口上向我的 RBP 转发端口。

3.  使用[PIV pn](http://www.pivpn.io/)T0】生成 OpenVPN 证书，并根据我的偏好设置 OpenVPN 服务器。

4.  创建 PiVPN 证书并使用 SCP 下载该证书并更改。opvn 文件，以确保端点是我的 DynamicDNS 记录(例如:vpn.example.com)。

5.  为了规避我遇到的一个问题，我的 VPN 指向我的路由器的公共 IP，我的 ISP 改变了我的 IP 地址，我修改了我找到的一个动态 DNS 脚本(向[https://willwarren.com/](https://willwarren.com/)致敬)。

6.  如果我的 ISP 再次更改我的公共 IP 地址，设置 Cron 作业每天查询一次并更新我的 AWS Route53 A 记录。当 SSH'ed 进入 RBP 运行`crontab -e`并设置触发 Cron 作业的期望频率时。这里有一个简单易用的工具。
    每天午夜运行的 24 小时 Cron 作业示例:`0 0 * * * /bin/bash /home/pi/dynamic_dns_route53.sh`

7.  在 RBP 上安装 [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) 。

8.  使用策略`dynamicdnspolicy` :
    创建一个 IAM 用户

```
 {  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "route53changerecord",  "Effect":  "Allow",  "Action":  "route53:ChangeResourceRecordSets",  "Resource":  "arn:aws:route53:::hostedzone/HOSTEDZONE"  }  ]  } 
```

1.  创建访问密钥/密钥对，并添加到 RBP 上的 AWS CLI 配置文件。

2.  从我的存储库中下载 [DynamicDNS 脚本](https://github.com/hunttom/bodega/tree/master/aws)

3.  将托管区域和记录集添加到 Route53 的第 8 行和第 11 行的`dynamic_dns_route53.sh`脚本中。

4.  验证脚本是否运行！

您可以将脚本更改为您拥有的任何云/DNS 提供商！

最大的成功(当然是安全的！)

* * *

PS 这在我的手机和笔记本上都有效，我只需要一个 OpenVPN 客户端来使用证书！