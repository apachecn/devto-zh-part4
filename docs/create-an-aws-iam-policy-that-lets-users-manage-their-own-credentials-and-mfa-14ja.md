# 创建一个 AWS IAM 策略，允许用户管理他们自己的凭据和 MFA

> 原文：<https://dev.to/alanwsmith/create-an-aws-iam-policy-that-lets-users-manage-their-own-credentials-and-mfa-14ja>

*原载于[alanwsmith.com](http://alanwsmith.com/how-to-create-an-iam-policy-that-lets-users-manage-their-credentials)T3】*

我一直在钻研亚马逊网络服务(AWS)的用户管理。我的第一个目标是创建一个用户，给他们发送密码，让他们在没有我的情况下管理其余的凭证。

各种凭证类型位于每个用户的“我的安全凭证 <sup>2</sup> 部分下。它们是:

1.  用于控制台访问的 IAM 密码
2.  CLI、SDK 和 API 访问的访问密钥
3.  多因素身份认证(MFA)
4.  X.509 证书
5.  SSH 密钥
6.  AWS 代码提交的 HTTPS Git 凭据

Amazon 提供了五个内置的 IAM 策略，这些策略提供了对凭证集的访问。第一个是`IAMFullAccess`，提供对身份和访问管理(IAM)服务的完全管理控制。它不仅允许用户管理他们自己的详细信息，还允许他们创建、删除任何其他用户、组、策略、角色等...不是给非管理员用户的东西。

第二个内置的 AWS 策略是`IAMReadOnlyAccess`。它只是做它在 tin 上所说的事情，提供对 IAM 仪表板的各个部分的只读访问。所以，让用户设置他们的凭证是没有帮助的。

最后三个内置策略(以及它们对应的内容)是:

1.  `IAMSelfManageServiceSpecificCredentials` -允许用户为 AWS 代码提交更新他们的 HTTPS Git 凭证
2.  `IAMUserChangePassword` -允许用户更新其 IAM 密码以进行控制台访问
3.  `IAMUserSSHKeys` -允许用户更新他们的 SSH 密钥

虽然已经完成了一半，但我们仍然无法管理“CLI、SDK 和 API 访问的访问密钥”、“多因素身份验证(MFA)”和“X.509 证书”。因为我要找的不是内置的，所以我自己着手构建一个自定义策略。

在参考了半打网页、IAM docs <sup>3</sup> 和同样多的 StackOverflow <sup>4</sup> 页面之后，我得出了下面的策略定义。它为用户提供了对所有六组凭据的控制。它还需要多因素认证(MFA) <sup>5</sup> ，并在安装之前将用户锁定在所有 AWS 服务之外。

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["iam:ListVirtualMFADevices"],
      "Resource": "*"
    },
    {
      "Sid": "AllowUsersToManageCoreCredentials",
      "Effect": "Allow",
      "Action": [
        "iam:*AccessKey*",
        "iam:ChangePassword",
        "iam:GetUser",
        "iam:*ServiceSpecificCredential*",
        "iam:*SigningCertificate*"
      ],
      "Resource": ["arn:aws:iam::*:user/${aws:username}"]
    },
    {
      "Sid": "AllowIndividualUserToListOnlyTheirOwnMFA",
      "Effect": "Allow",
      "Action": ["iam:ListMFADevices"],
      "Resource": [
        "arn:aws:iam::*:mfa/*",
        "arn:aws:iam::*:user/${aws:username}"
      ]
    },
    {
      "Sid": "AllowIndividualUserToManageTheirOwnMFA",
      "Effect": "Allow",
      "Action": [
        "iam:CreateVirtualMFADevice",
        "iam:DeleteVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:ResyncMFADevice"
      ],
      "Resource": [
        "arn:aws:iam::*:mfa/${aws:username}",
        "arn:aws:iam::*:user/${aws:username}"
      ]
    },
    {
      "Sid": "AllowIndividualUserToDeactivateOnlyTheirOwnMFAOnlyWhenUsingMFA",
      "Effect": "Allow",
      "Action": ["iam:DeactivateMFADevice"],
      "Resource": [
        "arn:aws:iam::*:mfa/${aws:username}",
        "arn:aws:iam::*:user/${aws:username}"
      ],
      "Condition": {"Bool": {"aws:MultiFactorAuthPresent": "true"}}
    },
    {
      "Sid": "AllowIndividualUserToManageTheirSSHCredentials",
      "Effect": "Allow",
      "Action": [
        "iam:DeleteSSHPublicKey",
        "iam:GetSSHPublicKey",
        "iam:ListSSHPublicKeys",
        "iam:UpdateSSHPublicKey",
        "iam:UploadSSHPublicKey"
      ],
      "Resource": "arn:aws:iam::*:user/${aws:username}"
    },
    {
      "Sid": "BlockMostAccessUnlessSignedInWithMFA",
      "Effect": "Deny",
      "NotAction": [
        "iam:CreateVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:ListMFADevices",
        "iam:ListUsers",
        "iam:ListVirtualMFADevices",
        "iam:ResyncMFADevice"
      ],
      "Resource": "*",
      "Condition": {"BoolIfExists": {"aws:MultiFactorAuthPresent": "false"}}
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

说到 MFA，它在 web 控制台中很容易使用。在命令行上使用它并不简单。关于基本过程的细节，请看亚马逊的视频[。(如果你像我一样，这看起来是编写一个小脚本来帮助自动化这个过程的绝好机会。这是留给读者的一个练习。)](https://aws.amazon.com/premiumsupport/knowledge-center/authenticate-mfa-cli/)

* * *

**脚注**

1.  现代世界的奇迹之一，[亚马逊网络服务](https://aws.amazon.com)是正在吞噬软件世界的*云计算平台。*
**   每个用户都有一个 [My Security Credentials](https://console.aws.amazon.com/iam/home?#/security_credentials) 部分，他们可以使用这个部分来管理他们的各种凭证，假设他们有这样做的权限。当然，这也是这篇文章的主旨。*   亚马逊的文档是我见过的最好的文档之一。 [IAM 文档](https://docs.aws.amazon.com/iam/index.html)也不例外(尽管没有我想要的特定东西)。*   现代世界的另一个奇迹是， [StackOverflow](https://stackoverflow.com) 是*解决你所有编程问题的地方。(不过，他们也没有我想要的东西。至少，直到我把这个也贴在那里。)**   [多重身份认证](https://en.wikipedia.org/wiki/Multi-factor_authentication)是*要走的*路。基本思想是，登录需要“你有的东西，你知道的东西”。你知道的是你的密码。你所拥有的是某种每 30 秒钟就会产生一次随机数的设备(或者你手机上的应用程序)。如果你没有正确的密码，你就不能登录。这很重要的原因是，如果你的密码被盗，坏人无法进入，但你仍然可以与你的 MFA 设备。*