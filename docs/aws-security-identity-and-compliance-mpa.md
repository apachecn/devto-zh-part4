# AWS 安全身份和合规性

> 原文：<https://dev.to/vikashagrawal/aws-security-identity-and-compliance-mpa>

# IAM(身份访问管理员)

它有助于管理 AWS 服务附带的角色、组和策略
。IAM 是全球性的，不受任何地区的限制。
·普通 AWS 账户是根账户。
您可以定义多个用户并将其附加到组中，并且该组也可以被定义。

```
o   Group is a way to group the users and used to apply the policy and /or role to them.
o   Permission to user can be assigned while tagging it to group or directly with the policy.
o   Access Key Id and Secret Access Key is used to access AWS programmatically.
o   User and Password are used to login to AWS Management Console.
o   New users have no permission when created.
o   Power User Access: Provides full access to AWS services and resources, but does not allow management of Users and groups.
o   Group and Role both contains policies but Group is associated with the user while Role is associated with the AWS service.
o   Custom policy also can be defined. 
```

为了避免误用，您可以对您的 root 帐户启用多因素身份验证(MFA)。角色是对策略进行分组的一种方式。
策略是权限定义。
政策文件是 JSON。
允许密码轮换策略
通过 putty 或 AWS CLI，您可以使用 configure 命令以用户名(您在此处创建的用户名)登录，但此选项会创建一个文件，将凭据存储在~/中。AWS/凭证:

```
o   aws_access_key_id
o   aws_secret_access_key 
```

角色比访问密钥和访问 id 更安全。角色更容易管理。
·角色甚至可以在设置后分配。

# 认知

它有助于限制对给定 AWS 服务的访问。

# 守卫职责

它监控您的 AWS 帐户中的恶意活动。

# 检查员

通过这种方式，您可以运行一系列测试案例来找出漏洞

# 你们有吗

它扫描 S3 桶，并找出它是否有任何个人信息，如卡号、手机号码

# 证书管理员

默认情况下，AWS 提供带域注册的 SSL 证书。

# 云 HSM

它用于存储公钥或私钥等密钥。

# 目录服务

它集成了 MS 级目录

# WAF (Web 应用文件)

# 盾牌

# 神器