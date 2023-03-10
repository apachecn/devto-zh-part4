# 具有 AWS Lambda 和松弛反馈的计划休息呼叫

> 原文：<https://dev.to/gmoretti/scheduled-rest-call-with-aws-lambda-and-slack-feedback-3p9e>

这篇短文背后的原因有两个。首先，我们工作中的一个经常性任务是调用一个 REST 服务来创建一个 web 挂钩。第二，我使用 Amazon Web Services 已经有一段时间了，认为 Lambdas 是运行简单脚本的完美服务。我做了一些研究，当然我不是唯一的一个。一旦这个过程完成，我们通常会通过 slack 消息通知它已经准备好了，所以我也认为这样做很酷。

# AWSλ

Amazon Lambda 允许你为你需要的功能选择语言，我选择 Python 只是为了测试它，因为它有很酷的和经过良好测试的模块，就像 Requests 一样，我们将使用它。

按照官方文档[设置 lambda 函数非常简单](https://docs.aws.amazon.com/lambda/latest/dg/getting-started-create-function.html)所有要编码和测试的东西都可以直接从 amazon 控制台获得。所以让我们看看下一步需要做什么。

# 击穿

这是我们想要完成的三件事

1.  调用 web 服务。
2.  呼叫响应松弛。
3.  设置每周执行一次的触发器

## 服务标注

假设你面前已经有一个 lambda 上的 python 脚本，为了调用这些服务，我们将使用[请求模块](https://2.python-requests.org//es/latest/)这个库将使处理所有 REST 操作变得容易，这是一个很好的选择，因为它可以从 lambda 中默认提供的库中获取。这引出了非常重要的一点，那就是**任何需要的依赖项都必须手动添加到 lambda** 中，但是在 lambdas 函数中已经有了[的库。](https://gist.github.com/gene1wood/4a052f39490fae00e0c3)

下面的代码从可用模块中导入请求模块，并对服务进行所需的调用。在我的例子中，需要一个 POST 调用，它必须包含一个用于认证的承载令牌，所以我创建了两个额外的方法。

```
import json
import datetime
from botocore.vendored import requests

api_url = 'https://someservices.com/getSomeStuff'
myToken = 'someBearerToken'

def getHeader():
    return {
        'Content-Type': 'application/json', 'user-agent': 'my-app/0.0.1',
        'Authorization': 'Bearer {0}'.format(myToken)),
        'user-agent': 'my-app/0.0.1'
    }

def informPayload(region):
return {
    'payload1': 'value1',
    'payload2': 'value2',
    'payload3': 'value3'
}

def makeCall():
    headers = getHeader()
    payload = informPayload()    
    response = requests.post(api_url, headers=headers, json=payload)

    #I'm also interested in giving feedback when it fails (code 422) so i also added it as a returned response
    if (response.status_code == 201 or response.status_code == 200 or response.status_code == 422):
        return json.loads(response.content.decode('utf-8'))
    else:
        print(response.content)
        return None 

#This is the default handle for the lambda, it will be the entry poing def lambda_handler(event, context):
    response = makeCall()

    if response is not None:
        print("Here's your response: ")
        print(response)

    else:
        print('[!] Request Failed')

    return {
        'statusCode': 200,
        'body': json.dumps('Call was successful!')
    } 
```

## 通过松弛度反馈

现在我们有了调用代码，我们可以使用调用外部服务的相同过程来调用 Slack API，并向我们的团队通道发送一条消息和响应。要获得这样做的凭证，您将必须在 Slack 上查看步骤，以创建一个新的 Salck 应用程序，并将其安装在您团队的 Slack 工作区中。你可以按照[官方 slack 文档](https://api.slack.com/messaging/sending)来做这个。
有了这些信息，我们将修改 *lambda_handler* 方法，并添加一个新的发送方方法，现在你有了新的方法。

```
#Updated def lambda_handler(event, context):
    response = makeCall()

    if response is not None:
        slack_msg = '@here is the result of your call: \n  \n'
        slack_msg += json.dumps(response)
        sendSlack(slack_msg)
        print("Here's your response: ")
        print(response)

    else:
        print('[!] Request Failed')

    return {
        'statusCode': 200,
        'body': json.dumps('Avinode Hook was executed!')
    }

#New Method def sendSlack(message):
    slack_post_message_url = 'https://slack.com/api/chat.postMessage'
    slack_bearer_token = 'yourOwnToken'
    headers = {
        'Content-Type': 'application/json',
        'Authorization': 'Bearer {0}'.format(slack_bearer_token)
    }
    body = {
        'channel': 'test2',
        'text': message
    }
    response = requests.post(slack_post_message_url, headers=headers, json=body) 
```

## 预定执行

现在，为了定期执行您的脚本，lambdas 可以由事件触发，这些事件可以配置为 [CRON 表达式](https://www.baeldung.com/cron-expressions)，这是调度任务的终极工具，并且由 Lambdas 提供本地支持。按照亚马逊官方文档中的这两个教程，我们可以很快安排好我们的功能。

*   这将帮助我们[建立一个事件](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/RunLambdaSchedule.html)。

*   下一个将向我们展示如何使事件成为 CRON 表达式。

**考虑到事件执行之间的最小时间间隔是 1 分钟，所以 CRON 表达式中的位置秒数是不使用的。**

# 最后的想法

我认为这是一种非常便宜可靠的方式来完成周期性的预定过程，特别是如果你没有基础设施或者你不想浪费一台机器来完成这项工作。另一方面，它可能不适合耗时的任务，因为 Lambda 服务是按处理时间收费的，它可能会变得非常昂贵。