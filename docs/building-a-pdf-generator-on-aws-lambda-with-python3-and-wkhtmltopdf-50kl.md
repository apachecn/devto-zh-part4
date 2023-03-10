# 用 Python3 和 wkhtmltopdf 在 AWS Lambda 上构建 PDF 生成器

> 原文：<https://dev.to/_rich/building-a-pdf-generator-on-aws-lambda-with-python3-and-wkhtmltopdf-50kl>

***更新 07/18/2019:源代码:【https://github.com/rbk/Lambda-PDF-Generator】***

***2019 年 6 月 28 日更新:对于任何试图遵循这一点的人，请阅读关于生成的 pdf 上的字体错误的后续帖子:[https://blog . richardkeller . net/wkhtmltopdf-bad-kerning-on-AWS-lambda/](https://blog.richardkeller.net/wkhtmltopdf-bad-kerning-on-aws-lambda/)***

## 简介

随着无服务器自动扩展计算能力的广泛使用，在 2019 年创建可扩展的 API 比以往任何时候都更容易。在本文中，我将向您展示我如何创建一个可以处理 1000 个并发请求的 PDF generator API。

这篇文章的目的是向你展示我是如何创建 API 的，这样你就会明白创建你自己的无服务器 API 是多么容易。当这个项目完成时，您将拥有一个 API 端点，您可以在其中发布一个 JSON 对象，并在亚马逊 S3 上接收一个 PDF 链接。JSON 对象是这样的:

```
{  "filename":  "sample.pdf",  "html":  "<html><head></head><body><h1>It works! This is the default PDF.</h1></body></html>"  } 
```

Enter fullscreen mode Exit fullscreen mode

## 设置

**你需要什么:**

*   Python3
*   安装并配置了 Aws CLI
*   无服务器安装

### 无服务器

您需要做的第一件事是初始化一个新的无服务器项目。Serverless 是一个工具，它极大地简化了 AWS、Gcloud 和 Azure 服务的使用，因此您可以创建 API，而不必担心管理服务器。如果你需要更多关于无服务器的信息，请访问他们的网站:[Serverless.com](https://serverless.com)。在您的终端中运行以下命令来初始化您的项目。

**注:sls 是无服务器的简称，可以互换使用。**

```
sls create --template aws-python3 
```

Enter fullscreen mode Exit fullscreen mode

这个命令将引导一个 Python3 Lambda 设置供您使用。

### [T1】WKHTMLTOPDF 二进制](#wkhtmltopdf-binary)

接下来，我们需要获得将 HTML 转换成 pdf 的二进制文件。为此，我使用了 [WKHTMLTOPDF](https://github.com/wkhtmltopdf/wkhtmltopdf) 。**重要提示:**您必须使用版本 0.12.4，因为 wkhtmltopdf 的更高版本需要主机系统的动态依赖关系，而这些依赖关系无法安装在 Lambda 上。

在此下载 0.12.4 版:

[https://github . com/wkhtmltopdf/wkhtmltopdf/releases/download/0 . 12 . 4/wkhtmltox-0 . 12 . 4 _ Linux-generic-amd64 . tar . xz](https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz)

一旦提取了这个 tar 文件，将二进制文件 ***wkhtmltopdf*** 复制到项目中的二进制文件夹中。

```
./binary/wkhtmltopdf 
```

Enter fullscreen mode Exit fullscreen mode

更多关于 wkhtmltopdf 的信息可以在他们的网站上找到:
[WKHTMLTOPDF](https://wkhtmltopdf.org/)

### Python3 依赖关系

现在我们有了 WKHTMLtoPDF 二进制文件，我们需要 Python 库， ***pdfkit*** 来使用它。由于我们使用无服务器和 AWS Lambda，我们不能只运行 *pip install pdfkit* 。我们需要一个无服务器插件来安装我们对 Lambda 的依赖。

在我们的项目文件夹中，为无服务器安装 **python 插件需求**模块。

```
sls plugin install -n serverless-python-requirements 
```

Enter fullscreen mode Exit fullscreen mode

现在，在您的 serverless.yml 文件中，您需要在 yaml:
中添加一个自定义部分

```
custom:
  pythonRequirements:
    dockerizePip: true 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了无服务器插件 requirements，您可以将 requirements.txt 文件添加到您的项目中，当您部署时，它会自动安装在 lambda 上。

你这个项目的 requirements.txt 只需要有 pdfkit。

**Requirements.txt**

```
pdfkit 
```

Enter fullscreen mode Exit fullscreen mode

**对于此模块的任何问题签出存储库问题:**
[无服务器 Python 需求](https://github.com/UnitedIncome/serverless-python-requirements)

## 就绪，设定，代码

我们现在准备编码。对于这个项目，我们只需要处理两个文件:serverless.yml ad handler.py。通常，您希望您的无服务器项目是轻量级的，并且独立于代码库的其他部分。

### Serverless.yml

**serverless.yml** 文件是我们的主配置文件。一个 serverless.yml 文件被分成几个重要的部分。如果你刚到 YAML，[查看 YAML](https://learn.getgrav.org/16/advanced/yaml) 的文档。

对于这个 serverless.yml 配置，您需要创建一个名为 config.yml 的文件。这将存储 S3 存储桶名称。serverless.yml 将引用 config.yml 为您的项目设置正确的存储桶。

**config . yml**的内容

```
BucketName: 'your-s3-bucket-name' 
```

Enter fullscreen mode Exit fullscreen mode

**这里是一个无服务器. yml 文件的高级概述:**

```
service: pdf-services # name or reference to our project
provider: # It is possible to use Azure, GCloud, or AWS
functions: # Array of functions to deploy as Lambdas
resources: # S3 buckets, DynamoDB tables, and other possible resources to create
plugins: # Plugins for Serverless
custom: # Custom variables used by you or plugins during setup and deployment 
```

Enter fullscreen mode Exit fullscreen mode

我们的无服务器配置将在部署时为我们做一些事情:

1.  创建一个名为 **pdf-service-bucket** 的 S3 存储桶来存储我们的 pdf
2.  创建一个将创建 pdf 的函数
3.  让我们的函数访问 S3 桶
4.  在以下位置为我们的 Lambda 函数设置 API 端点:

```
POST https://xxxx.execute-api.xxxx.amazonaws.com/dev/new-pdf 
```

Enter fullscreen mode Exit fullscreen mode

下面是完整的 serverless.yml 配置。我在代码中添加了一些重要的注释。

```
service: pdf-service
provider:
  name: aws
  runtime: python3.7
  # Set environment variable for the S3 bucket
  environment:
    S3_BUCKET_NAME: ${file(./config.yml):BucketName}
  # Gives our functions full read and write access to the S3 Bucket
  iamRoleStatements:
    -  Effect: "Allow"
       Action:
         - "s3:*"
       Resource:
          - arn:aws:s3:::${file(./config.yml):BucketName}
          - arn:aws:s3:::${file(./config.yml):BucketName}/*
functions:
  generate_pdf:
    handler: handler.generate_pdf
    events:
      - http:
          path: new-pdf
          method: post
          cors: true
resources:
 # Creates an S3 bucket in our AWS account
 Resources:
   NewResource:
     Type: AWS::S3::Bucket
     Properties:
       BucketName: ${file(./config.yml):BucketName}
custom:
  pythonRequirements:
    dockerizePip: true
plugins:
  - serverless-python-requirements 
```

Enter fullscreen mode Exit fullscreen mode

### [T1】handler . py](#handlerpy)

Handler.py 是我们唯一需要的 Python 文件。它包含一个生成 PDF 并保存到 Lambda 的函数。您可以在这个文件中创建更多的函数来将代码分成可重用的部分，但是对于这个例子来说，一个函数就足够了。Lambda 函数默认有两个参数:事件和上下文。

**上下文**包含环境变量和系统信息。

**事件**包含发送给 lambda 函数的请求数据。

在这个项目中，我们将向函数 **generate_pdf** 发送一个文件名和 HTML，它将返回它创建的 pdf 的 URI。

```
import json
import pdfkit
import boto3
import os
client = boto3.client('s3')

# Get the bucket name environment variables to use in our code S3_BUCKET_NAME = os.environ.get('S3_BUCKET_NAME')

def generate_pdf(event, context):

    # Defaults
    key = 'deafult-filename.pdf'
    html = "<html><head></head><body><h1>It works! This is the default PDF.</h1></body></html>"

    # Decode json and set values for our pdf    
    if 'body' in event:
        data = json.loads(event['body'])
        key = data['filename']
        html = data['html'] 

    # Set file path to save pdf on lambda first (temporary storage)
    filepath = '/tmp/{key}'.format(key=key)

    # Create PDF
    config = pdfkit.configuration(wkhtmltopdf="binary/wkhtmltopdf")
    pdfkit.from_string(html, filepath, configuration=config, options={})

    # Upload to S3 Bucket
    r = client.put_object(
        ACL='public-read',
        Body=open(filepath, 'rb'),
        ContentType='application/pdf',
        Bucket=S3_BUCKET_NAME,
        Key=key
    )

    # Format the PDF URI
    object_url = "https://{0}.s3.amazonaws.com/{1}".format(S3_BUCKET_NAME, key)

    # Response with result
    response = {
        "headers": {
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Credentials": True,
        },
        "statusCode": 200,
        "body": object_url
    }

    return response 
```

Enter fullscreen mode Exit fullscreen mode

## 部署

现在您已经准备好了所有的代码，是时候进行部署了。在您的项目文件夹中，从您的终端运行以下命令:

```
sls deploy 
```

Enter fullscreen mode Exit fullscreen mode

运行 deploy 后，Serverless 将在 AWS 中为您创建所有内容。您将获得用于生成 pdf 的 HTTP POST 端点。端点看起来会像这样:

```
https://xxxxxx.execute-api.us-east-1.amazonaws.com/dev/new-pdf 
```

Enter fullscreen mode Exit fullscreen mode

你可以用 **curl** 来测试你的函数。下面的 curl 命令向 lambda 端点发送一个 JSON 对象。JSON 对象包含一个文件名和一些 html 来转换成 PDF。

```
curl -d '{"filename":"my-sample-filename.pdf", "html":"<html><head></head><body><h1>Custom HTML -> Posted From CURL as {JSON}</h1></body></html>"}' -H "Content-Type: application/json" -X POST REPLACE-WITH-YOUR-ENDPOINT 
```

Enter fullscreen mode Exit fullscreen mode

*注意:将“替换为您的端点”替换为您从无服务器接收的端点。*

运行此命令后，您应该会收到生成的 PDF 的 URI。

## 结论

创建一个可扩展的 PDF 生成器很容易与 AWS 和无服务器。除了拥有一个可扩展的 API 之外，你还不用担心服务器的维护。我鼓励你用 AWS Lambda 创建更多的项目，以适应这个平台和配置。

感谢阅读！

## 下一步

*   使用[无服务器域管理器](https://github.com/amplify-education/serverless-domain-manager)设置自定义域
*   使用[无服务器离线](https://github.com/dherault/serverless-offline)设置本地测试

## 资源

*   无服务器环境变量-[https://server less . com/framework/docs/providers/AWS/guide/Variables/](https://serverless.com/framework/docs/providers/aws/guide/variables/)
*   无服务器 CORS 指南-[https://server less . com/blog/CORS-API-gateway-survival-Guide/](https://serverless.com/blog/cors-api-gateway-survival-guide/)
*   字体 AWSλ-[https://forums.aws.amazon.com/thread.jspa?messageID=776307](https://forums.aws.amazon.com/thread.jspa?messageID=776307)
*   boto 3 API-[https://boto 3 . Amazon AWS . com/v1/documentation/API/latest/reference/services/S3 . html # S3。Client.upload_file](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.upload_file)
*   S3 论据参考-[https://gist . github . com/rbk/926 bfd 3d 886 B2 c25 c 53818 ee B6 e 77 d6a](https://gist.github.com/rbk/926bfd3d886b2c25c53818eeb6e77d6a)

最初发表于[https://blog.richardkeller.net](https://blog.richardkeller.net)。