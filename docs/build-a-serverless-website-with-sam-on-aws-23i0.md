# 在 AWS 上用 SAM 构建一个无服务器网站

> 原文：<https://dev.to/izifortune/build-a-serverless-website-with-sam-on-aws-23i0>

当一个网站不直接需要任何服务器来运行时，它可以被认为是无服务器的。无服务器很快成为运行你的服务和整个网站的流行选择。采用无服务器架构使您和您的组织能够专注于提供商业价值。每个应用程序都必须有一个固有的不可简化的复杂性,[复杂性守恒定律](https://en.wikipedia.org/wiki/Law_of_conservation_of_complexity)说，唯一的问题是谁将不得不处理它。使用无服务器，我们将这种复杂性卸载给其他人，他们可以替我们处理。

在瑞安航空实验室工作，我每天都有机会与令人惊叹的人们一起工作，并为欧洲最繁忙的旅游网站尝试“无服务器架构”。瑞安航空网站的多个页面是使用无服务器技术构建的。看看我在伦敦 AWS 峰会[的演讲中是如何使用 AWS 无服务器技术的。](https://www.youtube.com/watch?v=8jSu31nQi1g&feature=youtu.be)

## 无服务器服务

AWS 有大量完全由 AWS 管理的无服务器服务。我们在这里要关注的是部署无服务器网站的基本构件。这些服务包括:

[![AWS serverless services for a website](img/24b829643d0a2341b36f6e2fbe5ccb44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KYrJNGwG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://izifortune.com/dist/static/a0b9dd49dd3e05654ee8ab210d71a0b9/9f1f4/simple-architecture.png)

*Cloudfront* :一个高可用的 CDN，通过边缘位置在全球范围内提供您的内容。我们将使用 *Cloudfront* 来利用我们网站资产的边缘缓存，通过最近的边缘位置路由每个用户请求，而不需要每次都联系原点。 *Cloudfront* 还通过使用 [WAF](https://aws.amazon.com/waf/) 与客户建立安全连接，确保我们网站的第一级安全。

*S3* 一种易于使用和管理的对象存储服务。网站页面只是由 HTML 粘合在一起的静态资源的集合。在开发 web 应用程序时，一个流行的选择是使用单页面应用程序(SPA)框架来降低服务器端的复杂性并提高交互性。例如，瑞安航空公司的网站就是用 Angular 和 Angularjs 构建的。另一项新兴技术是 Javascript API 标记(JAM)，由静态站点生成器推动。这两种技术都有一个简单的方法:它们产生静态资产，可以很容易地从 S3 存储和提供。

*Lambda at the Edge (L@E)* 你可以执行任何更靠近查看器的函数，在边缘和原点之间添加任何额外的逻辑。 *L@E* 作为*云锋*和原点(s)之间的粘合剂。

## SAM

在接下来的文章中，我们将使用 sam-cli ，这是一个由 AWS 构建无服务器应用程序的开源框架，以减少使用 Lambda @ Edge 时的样板文件。

假设我们已经配置了 aws-cli 和 sam-cli，如果不是这样，您可以遵循此处的指南

## 基础架构模板

使用 sam-cli 构建应用程序我们将使用 *Cloudformation* 模板来描述我们的基础架构资源。它让我们能够在项目之间快速复制、重用和共享基础架构。一个 *Cloudformation* 模板为我们在 RyanairLabs 中运行的所有前端项目提供动力，处理您的基础设施使我们能够快速了解变化，将它们版本化，并在基础设施上进行合作。

使用 sam-cli 或 T2 cloud formation，我们倾向于将一个项目的所有资源保存在一个文件中，这样更容易管理和部署资源。我们希望在第一次迭代中生成的是一个可以服务于任何资产的简单基础设施。

[![Basic infrastructure](img/24b829643d0a2341b36f6e2fbe5ccb44.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KYrJNGwG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://izifortune.com/dist/static/a0b9dd49dd3e05654ee8ab210d71a0b9/9f1f4/simple-architecture.png)

让我们从创建名为`template.yaml`的模板文件开始，其中包含一些我们需要的基本服务:

```
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: >
  Serverless website

Resources:
  CloudFrontOriginAccessIdentity:
    Type: 'AWS::CloudFront::CloudFrontOriginAccessIdentity'
    Properties:
      CloudFrontOriginAccessIdentityConfig:
        Comment: 'Serverless  website  OA'

  CloudfrontDistribution:
    Type: "AWS::CloudFront::Distribution"
    Properties:
      DistributionConfig:
        Comment: "Cloudfront  distribution  for  serverless  website"
        DefaultRootObject: "index.html"
        Enabled: true
        HttpVersion: http2
        # List of origins that Cloudfront will connect to
        Origins:
          - Id: s3-website
            DomainName: !GetAtt S3Bucket.DomainName
            S3OriginConfig:
              # Restricting Bucket access through an origin access identity
              OriginAccessIdentity: 
                Fn::Sub: 'origin-access-identity/cloudfront/${CloudFrontOriginAccessIdentity}'
        # To connect the CDN to the origins you need to specify behaviours
        DefaultCacheBehavior:
          # Compress resources automatically ( gzip )
          Compress: 'true'
          AllowedMethods:
            - GET
            - HEAD
            - OPTIONS
          ForwardedValues:
            QueryString: false
          TargetOriginId: s3-website
          ViewerProtocolPolicy : redirect-to-https

  S3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      # Change bucket name to reflect your website
      BucketName: <YOURSWEBSITE.COM>

  S3BucketPolicy:
    Type: AWS::S3::BucketPolicy
    Properties:
      Bucket: !Ref S3Bucket
      PolicyDocument:
      # Restricting access to cloudfront only.
        Statement:
          -
            Effect: Allow
            Action: 's3:GetObject'
            Resource:
              - !Sub "arn:aws:s3:::${S3Bucket}/*"
            Principal:
              AWS: !Sub "arn:aws:iam::cloudfront:user/CloudFront  Origin  Access  Identity  ${CloudFrontOriginAccessIdentity}" 
```

浏览定义的模板文件，我们在此列出了不同的服务:

`CloudfrontOriginAccessIdentity` *，*用于限制通过 *Cloudfront* 的请求对 *Bucket* 文件的访问。我们不必公开我们的*桶*，也不必通过使用*原始访问权限*来启用网站托管。*original access identity*将用于*cloudfront distribution Origin*和 S3*BucketPolicy。*

`CloudfrontDistribution` *，*它是 *AWS* *CDN* 在全球范围内低延迟交付数据和内容。控制 *CDN 的 *DistributionConfig* 中有一系列属性。*我在文件上留下了不同的注释来进一步解释，如果你想查看属性的完整列表，你可以遵循[用户指南](%5Bhttps://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cloudfront-distribution.html%5D(https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-cloudfront-distribution.html))。

`S3Bucket` *，*这是*桶*，我们将在这里存储网站的所有资产。记得给 *BucketName* 一个正确且唯一的标识符，例如我们可以使用网站的域名来帮助你记住哪个 *Bucket* 属于哪个网站。

`S3BucketPolicy` *，*定义了*桶中谁可以访问，允许什么类型的操作。*使用*原始访问权限*我们将读取权限限制在 *Cloudfront* 上。

## 部署基础设施

我们已经可以开始部署我们的第一个无服务器网站，只需要前面列出的四种资源。我们将在 *S3Bucket、* Cache 托管网站资产，并在 *Cloudfront 提供内容。*要使用 sam-cli 部署基础架构，我们需要首先生成一个 *S3 存储桶*，用于存储包含基础架构所有不同输入的部署包。

在下面的命令*中，记住*一定要用你网站的实际域名来代替。

```
aws s3 mb s3://<YOURSWEBSITE.COM>-sam --region=us-east-1 
```

创建 Bucket 后，我们可以打包应用程序:

```
sam package --output-template-file packaged.yaml --s3-bucket <YOURSWEBSITE.COM>-sam 
```

最后部署应用:

```
sam deploy --template-file packaged.yaml --stack-name <yourwebsite> --capabilities CAPABILITY_IAM --region us-east-1 
```

我们使用 *us-east-1* 作为我们基础设施的默认区域，以减少我们稍后在定义 *Lambda @ Edge* 时将会遇到的一些限制，在那里它们只能部署到该区域。

创建 *Cloudfront* 发行版需要一些时间，通常在开始的 20-30 分钟之间。部署完成后，发行版将拥有一个 AWS 生成的域名，格式为 [`d3k1beyfkv2133.cloudfront.net`](http://d3k1beyfkv9165.cloudfront.net/) ，可用于向网站发出第一个请求。

为了测试一切是否正常，我们可以在 *S3Bucket 的根文件夹中上传一个`index.html`，例如:*T3

```
<html>
<body>
  <h1>Hello serverless</h1>
  <img src="https://github.com/awslabs/serverless-application-model/blob/master/aws_sam_introduction.png">
</body>
</html> 
```

要上传文件，我们可以使用 *aws-cli* 命令:

```
aws s3 cp index.html s3://<YOURWEBSITE.COM> --acl public-read 
```

一旦文件被上传，如果我们现在导航到 *Cloudfront 域名*，我们应该能够看到:

[![Hello serverless page](img/2a564c880823f2d254601baa909ecf79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ym2QAECG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://izifortune.com/dist/static/ad25eb5c1d41a31fa5f41c7ef663e5e9/f55ca/hello-serverless.png)

在接下来的文章中，我们将探讨如何服务多个页面、保护、处理重写、关联域，以及如何在我们的无服务器网站上正确部署资产。这在一篇文章中涵盖了很多内容，所以不要忘记在 twitter 上关注我。

**PS:AWS CloudDevelopmentKit**

AWS CDK 现已正式发布，如果您希望通过编程方式创建 AWS 资源，可以看看下面的示例:[https://AWS . Amazon . com/blogs/AWS/AWS-cloud-development-kit-CDK-typescript-and-python-are-now-generally-available/](https://aws.amazon.com/blogs/aws/aws-cloud-development-kit-cdk-typescript-and-python-are-now-generally-available/)