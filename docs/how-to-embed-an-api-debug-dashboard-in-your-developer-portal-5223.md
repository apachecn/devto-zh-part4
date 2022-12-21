# 如何在开发人员门户中嵌入 API 调试仪表板

> 原文：<https://dev.to/moesif/how-to-embed-an-api-debug-dashboard-in-your-developer-portal-5223>

Stephen Dawson 在 Unsplash 上拍摄的封面图片。T3】

您已经创建了仪表板和报告。也许你也和你的同事合作过。现在，您希望您的客户能够像您的内部团队成员一样了解您的 API。通过在您的开发人员门户中嵌入 API 日志，您可以为您的客户提供轻松的入门体验，并轻松调试工具，使他们能够在没有问题的情况下尽快集成您的 API。

## 什么是 Moesif 工作区？

Moesif 平台有*公共工作区*的概念。这些是在 Moesif 平台中创建的仪表板，就像您为内部团队成员创建的仪表板一样。最大的区别是公共工作空间有自动约束来保护您的数据。数据可以通过像客户的*用户 id* 或 *API 密钥*这样的字段来沙箱化。此外，访问公共工作区的用户不需要登录 Moesif。相反，他们可以使用工作区访问令牌或*共享链接*来访问数据。

Moesif 支持多种工作空间类型，包括 API 事件日志、分段、时间序列、热图等。

## 创建工作区令牌

通常，我们可以通过登录从您的 API 数据创建一个公共工作区，但是对于这个练习，我们希望通过管理 API 以编程方式生成工作区。这使您能够将工作区数据嵌入到面向客户的门户和网站中。

首先，我们需要检索一个管理 API 令牌。该密钥必须通过每个请求旁边的`Authorization`报头发送。

为此，我们需要访问 Moesif 网站上的管理 API 页面。

[![api-keys.png](img/5706825937d82a79b9dbc613b008b00d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wyUBHSdd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/%7B%7B%20%img/posts/technical/shareable-workspaces/api-keys.png%22%20%7C%20absolute_url%20%7D%7D)

在该页面上，我们必须选择一个范围，然后单击*生成令牌*。

在我们创建工作空间的例子中，我们只需要选择`create:workspaces`范围。

现在我们将准备创建一个由特定的`userId`过滤的公共工作区的细节。

```
# Prepare Request body to create the workspace of all the API calls since last 1 week for the user my_user_id. 
# Relative date range lookup
# RELATIVE_RANGES_LOOKUP = {
#   '-1h': { 'label': 'Last hour' },
#   '-24h': { 'label': 'Last 24 hours' },
#   '-1w': { 'label': 'Last 7 days' },
#   '-4w': { 'label': 'Last 28 days' },
#   '-12w': { 'label': 'Last 12 weeks' },
#   '-52w': { 'label': 'Last year' },
# } 
body={
    "name" : "Live API Log for an User", # Name of the workspace
    "chart" : {
        "args" : "",
        "url_query" : "?userIdFilter%5B0%5D%5Bkey%5D=my_user_id&userIdFilter%5B0%5D%5Bflag%5D=or", # Encoded URL Query Param, replace my_user_id the with specific user id.
        "view" : "events", # type of chart - events, time, map, segment, table.
        "es_query" : { # Elastic search query to filter data
          "size": 50,
          "from": 0,
          "sort": [
            {
              "request.time": "desc"
            }
          ]
        },
        "from" : "-1w", # Relative start date, refer to relative ranges lookup
        "to" : "now" # Relative end date, refer to relative ranges lookup
    },
    "policy" : { # Restriction on the data to share
        "resource" : {"must":[{"range":{"request.time":{"gte":"now-1w","lte":"now"}}},{"terms":{"user_id.raw":["my_user_id"]}}]}, # Query to restrict search, replace my_user_id with the specific user id.
        "acl" : [ # Access Control List
            {
                "grantee" : "org|<org_id>", # Grant access to the team, Replace <org_id> with your Moesif org_id
                "permission" : "admin" # Assign admin permission to the team
            },
            {
                "grantee" : "public", # Grant access to the public
                "permission" : "read" # Assign read permission to the public
            }
        ]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将创建一个公共工作区并获取可共享的链接。

```
import requests

# Prepare request headers headers = {'Accept': 'application/json',
           'Authorization': 'Replace Management token retrieved from the previous step'}

# Create public workspace def create_public_workspace(body):

  # Request to create a public workspace
  workspace = requests.post('https://api.moesif.com/replay/<org_id>/workspaces',
                          params={ 'app_id': '<app_id>' },
                          headers = headers, 
                          json=body).json()

  # Return public workspace url
  return 'https://www.moesif.com/public/' + workspace['access_token']

# Fetch the shareable link shareable_link = create_public_workspace(body)
print('Shareable Link - ' + shareable_link) 
```

Enter fullscreen mode Exit fullscreen mode

*注意:您可以根据您需要的过滤器修改请求体，以限制共享的数据。*

## 将工作区嵌入您的客户门户

您可以将工作区嵌入到用户期望看到它们的门户或网站中。Embed 适用于任何支持使用 URL 或 iFrame 嵌入内容的门户或网站。

* * *

本文由 [Keyur Doshi](https://twitter.com/atKeyur) 为 [Moesif 博客](https://www.moesif.com/blog)撰写

* * *

对贴有白色标签的工作区感兴趣，请联系 [Moesif 团队](//mailto:team@moesif.com)了解更多信息。

**Moesif 是最先进的 API 分析平台，支持 REST、GraphQL 等。超过 2000 个组织使用 Moesif 来跟踪他们最忠实的客户使用他们的 API 做了什么。[了解更多](https://moesif.com?int_source=devto)**