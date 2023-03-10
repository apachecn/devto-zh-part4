# 列出与 VPC 无关的 AWS Lambda 函数

> 原文：<https://dev.to/akloya/list-aws-lambda-functions-not-associated-with-vpc-3d3m>

AWS Lambdas 已经非常受欢迎，现在大多数公司都在使用它。

对于一些安全要求，我们可能需要在你想拉所有不与 VPC 相关联的 lambdas。下面的脚本可以帮助你做到这一点。

```
import boto3
from botocore.exceptions import ClientError

client = boto3.client('lambda')
response = client.list_functions()
for function in response['Functions']:
    try:
        response = client.get_function(
            FunctionName=function['FunctionName']
        )
        vpcid = response['Configuration']['VpcConfig']['VpcId']
    except KeyError:
        print("==>" + function['FunctionName'])
    except ClientError as e:
        print("[ERROR] Invoking Lambda Function" + e) 
```

Enter fullscreen mode Exit fullscreen mode