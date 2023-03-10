# 如何备份 AWS 安全组设置

> 原文：<https://dev.to/lukas238/how-to-backup-aws-security-groups-settings-5ia>

如果像我一样，您必须在您的 AWS 帐户上管理几个安全组，您可以使用`describe-security-groups` AWS CLI 命令将它们下载为. json 文件进行备份。

## TL；速度三角形定位法(dead reckoning)

你可以跳转到[官方 AWS 文档](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html)获取`describe-security-groups` AWS CLI 命令的链接:[https://docs . AWS . Amazon . com/CLI/latest/reference/ec2/describe-security-groups . html](https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-security-groups.html)

## 先决条件

您需要在您的系统上安装 AWS CLI 命令行工具和配置。

您可以在这两个链接上找到必要的信息:

*   [https://docs . AWS . Amazon . com/CLI/latest/user guide/CLI-chap-install . html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
*   [https://docs . AWS . Amazon . com/CLI/latest/user guide/CLI-chap-configure . html](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)

## 如何使用

1.  在本地文件夹中打开一个终端窗口，运行以下命令:

    ```
    aws ec2 describe-security-groups --group-ids sg-123abcd4 
    ```

    > 您将需要想要备份的安全组的 ID(在本例中为“sg-123abcd4”)。

2.  搞定了。您现在可以备份新创建的。json 文件，或者以任何你想要的方式。

## 讲述着。json 文件

这里有一个快速参考。json 文件看起来:

```
{  "SecurityGroups":  [  {  "IpPermissionsEgress":  [],  "Description":  "My security group",  "IpPermissions":  [  {  "PrefixListIds":  [],  "FromPort":  22,  "IpRanges":  [  {  "CidrIp":  "203.0.113.0/24"  }  ],  "ToPort":  22,  "IpProtocol":  "tcp",  "UserIdGroupPairs":  []  }  ],  "GroupName":  "MySecurityGroup",  "OwnerId":  "123456789012",  "GroupId":  "sg-903004f8",  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode