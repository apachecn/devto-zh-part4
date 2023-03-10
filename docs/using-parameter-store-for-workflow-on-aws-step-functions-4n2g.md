# 在 AWS 步骤功能中使用工作流的参数存储

> 原文：<https://dev.to/mengjiann/using-parameter-store-for-workflow-on-aws-step-functions-4n2g>

AWS 步骤功能中的工作流由一个状态机表示，该状态机由多个状态(步骤)、它们的关系以及它们的输入和输出组成。当我们使用 [Amazon States Language](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html) 构建状态机时，我们可能会将应用程序秘密定义为状态机代码的一部分。

一种可能的情况是，我们正在使用一个`Pass`状态为下一个状态准备一个输入 JSON。在下面的例子中，我们将远程 FTP 用户名和密码传递给下一个状态，这是一个从远程 FTP 服务器下载文件的 Lambda 函数。

```
"ftp-download-lambda-config": {
      "Type": "Pass",
      "Result": {
          "ftpHost" : "ftp-server.com",
          "ftpUsername" : "username",
          "ftpPassword" : "password",
          "withDecryption": true
      },
      "ResultPath": "$",
      "Next": "ftp-download-lambda"
},
"ftp-download-lambda": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:xxx:xxx:function:ftp-download-lambda",
      "Next": "next-step"
}, 
```

如果我们要在任何版本控制系统(如 Git)中对状态机代码进行版本控制，这将引发一个问题。这是因为应用程序秘密(FTP 用户名和密码)将暴露给有权访问代码库的用户。作为一个良好的安全实践，建议将机密存储在一个单独的环境中，这将确保它是安全的，只有有限的访问权限，并可能在存储位置进行加密。

市场上很少有突出的服务:**金库**(来自 hashi corp)**秘密管理者**和**参数商店**(来自 AWS)。由于托管服务是首选，因此不会考虑保险存储。将秘密管理器与参数存储进行比较，秘密管理器每月每个秘密花费 0.40 美元，每 10，000 次 API 调用花费 0.05 美元，而参数存储不需要任何使用成本。虽然 Secrets Manager 附带了一个秘密轮换功能，允许您自动轮换 API 密钥、密码等，但这并不是我们的要求所必需的。因此，AWS **参数存储库**更适合这个用例。

因此，一旦我们决定将我们的秘密存储在 AWS 参数存储中，我们必须修改我们的应用程序(在本例中是 Lambda 函数),以接受存储在参数存储中的秘密的引用，并从 Lambda 函数中检索秘密。状态机代码的变化如下所示:

```
"ftp-download-lambda-config": {
      "Type": "Pass",
      "Result": {
          "ftpHost" : "ftp-server.com",
          "ftpUsername" : "<Reference to username in Parameter Store>",
          "ftpPassword" : "<Reference to password in Parameter Store>",
          "withDecryption": true
      },
      "ResultPath": "$",
      "Next": "ftp-download-lambda"
},
"ftp-download-lambda": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:xxx:xxx:function:ftp-download-lambda",
      "Next": "next-step"
}, 
```

或者，我们可以使用额外的 Lambda 函数从参数存储中检索秘密，并将输出传递给状态机中的下一个状态。因此，通过使用新的 Lambda 函数，现有的 Lambda 函数/任务可以保持原样。新 Lambda 函数的用法如下所示:

```
"ssm-parameters-getter-lambda-config": {
      "Type": "Pass",
      "Result": {
          "param": [
            "parameters-store-ftpUsername",
            "parameters-store-ftpPassword"
          ],
          "withDecryption": true
      },
      "ResultPath": "$",
      "Next": "ssm-parameters-getter-lambda"
},
"ssm-parameters-getter-lambda": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:xxx:xxx:function:ssm-parameters-getter-lambda",
      "Next": "ftp-download-lambda"
},
"ftp-download-lambda": {
      "Type": "Task",
      "Parameters" : {
        "ftpHost": "ftp-server.com",
        "ftpUsername.$": "$.parameters-store-ftpUsername",
        "ftpPassword.$": "$.parameters-store-ftpPassword",
      },
      "Resource": "arn:aws:lambda:xxx:xxx:function:ftp-download-lambda",
      "Next": "next-step"
}, 
```

`ssm-parameters-getter-lambda`接受以下输入:

```
{
  "param": [
    "random-input-a",
    "random-input-b"
  ],
  "withDecryption": true
} 
```

并从参数存储中生成一个以 param name 为属性和值的对象。该值可以是

```
{
  "random-input-a": "<Value from Parameter Store>"
  "random-input-b": "<Value from Parameter Store>"
} 
```

Lambda 函数在 Github 上是开源的。它可以使用源代码 index.js 手动部署到您的 AWS 帐户，或者直接从名为`ssm-parameters-getter-lambda`的 AWS **无服务器应用程序库**部署。记得检查**显示创建自定义 IAM 角色或资源策略**的应用程序，好像这个 Lambda 需要额外的角色。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [蒙嘉恩](https://github.com/mengjiann)/[SSM-parameters-getter-lambda](https://github.com/mengjiann/ssm-parameters-getter-lambda)

### 从 AWS 系统管理器的参数存储中检索参数。

<article class="markdown-body entry-content p-5" itemprop="text">

# SSM-参数-getter-λ

从 AWS 系统管理器的参数存储中检索参数。

# 用例

最好的用例是将它放在接受配置作为输入(如用户名和密码)的步骤函数工作流中的状态之前。因此，应用程序机密安全地保存在 AWS 参数存储中，而不是保存在 Step 函数的状态机代码中。

# 参数访问限制

遵循最小特权原则，有两个选项允许限制 AWS Lambda 函数从参数存储中访问某些参数。

1.  ParametersPrefix -仅允许访问参数存储中带有特定前缀的参数。
2.  标记键列表-仅允许访问带有参数特定标记的参数。允许逗号分隔的标记键列表。

# 样本输入和输出

## 投入

```
{
  "param": [
    "random-input-a"
    "random-input-b"
  ]
  "withDecryption": true
} 
```

## 输出

```
{
  "random-input-a": "<Value from Parameter Store>"
  "random-input-b": "<Value from Parameter Store>"
} 
```

…</article>

[View on GitHub](https://github.com/mengjiann/ssm-parameters-getter-lambda)

参考资料:

*   [https://docs . AWS . Amazon . com/systems-manager/latest/user guide/systems-manager-parameter-store . html](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)
*   [https://docs . AWS . Amazon . com/step-functions/latest/DG/concepts-Amazon-States-language . html](https://docs.aws.amazon.com/step-functions/latest/dg/concepts-amazon-states-language.html)
*   [https://docs . AWS . Amazon . com/secrets manager/latest/user guide/intro . html](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html)