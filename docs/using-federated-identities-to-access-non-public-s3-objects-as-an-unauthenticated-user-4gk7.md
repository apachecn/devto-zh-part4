# 使用联合身份以未经身份验证的用户身份访问非公共 S3 对象。

> 原文：<https://dev.to/jimatjibba/using-federated-identities-to-access-non-public-s3-objects-as-an-unauthenticated-user-4gk7>

我最近参与了一个项目，这个项目有一些有趣的需求。该项目包括一个主要的应用程序，将严重依赖于放大器(显然😃)，利用 Auth、AppSync、Storage 等等。所有服务和数据都需要锁定给创建它们的用户，并且应用程序管理员可以访问它们。项目的第二部分包括未知数量的外部应用程序。这些应用程序将由其他外部开发人员编写，不会使用主应用程序生成的 Amplify 服务。这些应用程序需要能够访问非公共 S3 存储桶中的一些资产，并且不需要进行身份验证。

这些应用程序将无法像主应用程序那样严重依赖 Amplify，所以让我们来看看如何做到这一点。

正如 AWS 中的所有东西一样，给猫剥皮的方法不止一种。我们仍然需要以安全的方式访问这些对象，我们可以通过组合身份池[联合身份](https://docs.aws.amazon.com/cognito/latest/developerguide/identity-pools.html)和 AWS Signature Version 4 来实现这一点。

### 联合身份

> Amazon Cognito 身份池为来宾用户(未经身份验证)和已通过身份验证并收到令牌的用户提供临时 AWS 凭据。

首先，我们需要允许未经认证的身份，登录 AWS 控制台并选择 Cognito，然后选择管理联合身份。选择已为外部应用程序创建的身份池，并单击右上角的**编辑身份池**。向下滚动并点击**未认证身份**。选择复选框并保存您的更改。AWS 现在将为您提供临时凭据。

这样，我们现在可以设置我们的应用程序，使用身份池生成的临时凭证向 AWS 服务发出未经身份验证的请求。

我们仍然需要一种方法来创建这些临时凭据，为此我们将使用`aws-amplify` npm 包，因为它使创建变得非常简单。我们将使用`Auth`模块。显然，您可以使用`aws-sdk`并从那里使用 Cognito 包。它们将实现相同的目标，我只是碰巧喜欢 amplify 产品。

在 index.js 文件中，添加以下内容:

```
import Amplify, { Auth } from "aws-amplify";

Amplify.configure({
  Auth: {
    identityPoolId: "eu-west-1:****-***********-***",
    region: "eu-west-1"
  }
}); 
```

这是生成未经身份验证的身份的身份池的 id。

现在，您可以使用以下内容来访问临时凭证

```
const creds = await Auth.currentCredentials(); 
```

### AWS4

但是，我们如何处理这些新获得的凭证呢？这就是 AWS Signature 第 4 版的用武之地。这允许我们对我们的请求进行签名，签名过程向查询字符串添加一个令牌，用我们的临时凭证对我们进行身份验证。所以在我们的例子中，我们想要访问 S3 的一个对象。我们可以在发送请求之前对其进行签名，这使得 AWS 知道请求来自持有这些未经认证的凭证的人，而不是互联网上的某个随机的 chap。为了签署我们的请求，我们使用了 [`aws4`](https://www.npmjs.com/package/aws4) 包。

[![Birds](img/614380833c701fc3b52a10963b546d35.png)](https://i.giphy.com/media/26Ff9P7oFlrqX2epq/giphy.gif)

它几乎可以与所有亚马逊服务兼容，包括 S3、EC2、DynamoDB、Kinesis、Lambda、SQS、SNS、IAM、STS、RDS、CloudWatch、CloudWatch Logs、CodeDeploy、CloudFront、CloudTrail、ElastiCache、EMR、Glacier、CloudSearch、Elastic Load Balancing、Elastic Transcoder、CloudFormation、Elastic Beanstalk、Storage Gateway、Data Pipeline、Direct Connect、Redshift、OpsWorks、SES、SWF、AutoScaling、Mobile Analytics、Cognito Identity、Cognito Sync、Container Service、AppStream、Key Management Service、Config

为了签署我们对 S3 对象的请求，我们可以使用下面的函数。这返回

```
import aws4 from "aws4";
import { Auth } from "aws-amplify";

export default async (src: string) => {
  const credentials = await Auth.currentCredentials();
  const request = {
    host: "s3.eu-west-2.amazonaws.com",
    path: `/${process.env.REACT_APP_S3_BUCKET}/${src}`,
    service: "s3",
    signQuery: true
  };
  const protocol = "https://";
  const signedRequest = aws4.sign(
    request,
    Auth.essentialCredentials(credentials)
  );

  return protocol + signedRequest.host + signedRequest.path;
}; 
```

我们可以使用返回 URL 以安全的方式访问我们的 S3 对象。试试看！

虽然我的情况很不正常，但我绝对能看出这有什么用处。我希望这个分类对其他需要签署未经认证的用户对 AWS 服务的请求的人有用。