# 快速简单地将 AWS 参数存储解析为 JSON

> 原文：<https://dev.to/dvddpl/parse-aws-parameter-store-as-json-the-quick-and-easy-way-4bbk>

几天前，我们想保留我们的 [Google Developers](https://console.developers.google.com) 项目的凭证，允许我们将 React 应用程序与 Cognito 和 Google API 连接起来。
正如我在这里解释的[，我们最终将一个 JSON 配置放入](https://dev.to/dvddpl/where-do-you-keep-credentials-for-your-lambda-functions-5dno) [AWS 参数存储](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)。

在我们当前的项目中，我们需要在部署后在一个钩子中访问这个配置(基本上，只是一个在`sls deploy`结束时执行的 shell 脚本)。
通过简单地运行

`aws ssm get-parameters --name auth-google-config`

我们在哪里得到整个事情:

```
{
    "Parameters": [
        {
            "Name": "auth-google-config",
            "Type": "String",
            "Value": "\"{\"client_id\":\"123_your_client_id_666.apps.googleusercontent.com\",\"client_secret\":\"aBc_your_secret_666_XYZ\",\"authorize_scopes\":\"profile email openid\"}\"",
            "Version": 3,
            "LastModifiedDate": 1562138784.993,
            "ARN": "arn:aws:ssm:your_region:your_account_id:parameter/auth-google-config"
        }
    ],
    "InvalidParameters": []
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这些值都包含了**转义引号**

如果用 node 读取参数存储，我们只需将其解析成一个对象:

```
const value = JSON.parse(Value) 
```

Enter fullscreen mode Exit fullscreen mode

但是如何在一个 shell 脚本中实现这一点呢？

[JQ](https://dev.to/dvddpl/tools-i-wish-i-knew-from-the-start-48kl) 来救援了！

```
aws ssm get-parameters --name auth-google-config --query "Parameters[0].Value" 
| jq '.|fromjson' 
```

Enter fullscreen mode Exit fullscreen mode

基本上，我们将 aws 查询的结果直接输入 JQ，让它将字符串解析成一个对象(参见手册[这里](https://stedolan.github.io/jq/manual/)

[![piece of cake](img/f374e56b366c24e3a723de14cd646fea.png)](https://i.giphy.com/media/l2JejeLTKnkSBoIZW/giphy.gif)

如果你愿意，你可以使用 fromjson(以及 JQ 的所有其他函数和操作符)

* * *

斯蒂芬·斯坦鲍尔在 Unsplash 上的照片