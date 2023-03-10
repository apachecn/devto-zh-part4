# 用 OPA 挖掘 API 授权

> 原文：<https://dev.to/kenfdev/digging-into-api-authorization-with-opa-3jnf>

我最近一直在接触[开放策略代理](https://www.openpolicyagent.org/),寻找一种方法来为我的定制 API 服务获得细粒度的权限控制特性。

在这篇文章中，我假设你至少听说过什么是开放策略代理。如果你还没有看过，我强烈推荐你观看以下来自托林·桑达尔的演讲。

简介:开放策略代理
[https://www.youtube.com/embed/CDDsjMOtJ-c](https://www.youtube.com/embed/CDDsjMOtJ-c)

OPA 极其强大和灵活。你应该看看官方文档中的一些教程，从“[入门](https://www.openpolicyagent.org/docs/latest/get-started/)”开始。

我发现一个非常有趣的教程是 [HTTP API 授权](https://www.openpolicyagent.org/docs/latest/http-api-authorization/)。我想很多人都经历过 API 授权，都挣扎过所有的`if`语句到**允许**或**拒绝**用户做某事。有了 OPA，你可以**在你的代码**之外委托决策。

“把决定权委托给外面？”起初，这对我来说很难想象，所以我决定将 API 从非 OPA 版本迁移到 OPA 版本。

## TL；速度三角形定位法(dead reckoning)

你可以在[库](https://github.com/kenfdev/opa-api-auth-go)中查看完整代码。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [肯夫德夫](https://github.com/kenfdev) / [ opa-api-auth-go](https://github.com/kenfdev/opa-api-auth-go)

### 使用 OPA 作为策略决策点的示例 API。

<article class="markdown-body entry-content container-lg" itemprop="text">

# OPA API 授权

展示使用 OPA 作为策略决策点的区别的示例。

## 如何

主分支不包括 OPA 作为策略决策点。运算放大器被添加到 [add-opa 分支](https://github.com/kenfdev/opa-api-auth-go/tree/add-opa)中。

你可以比较一下[这个 PR](https://github.com/kenfdev/opa-api-auth-go/pull/1) 的区别

如果您想要提升 OPA 版本，请执行以下步骤:

```
git checkout add-opa
docker-compose up
```

#### 请求 API

该 API 在`GET /finance/salary/:username`端点中可用。您可以`GET`到这个端点，但是需要一个 JWT 来正确地发出请求。

你可以在 [jwt.io](https://jwt.io/) 轻松创建一个 JWT。这个应用程序假设算法是 HS256，秘密只是简单的`secret`。此外，有效负载看起来应该像这样:

```
{
  "sub": "1234567890"
  "iat": 1516239022
  "user": "bob"
  "subordinates
```

…</article>

[View on GitHub](https://github.com/kenfdev/opa-api-auth-go)

*   您可以从存储库的这个分支运行 OPA 授权的 API
*   非 OPA 版本和 OPA 版本的差异可以在[这里](https://github.com/kenfdev/opa-api-auth-go/pull/1/files)找到

## API

我们将要使用的 API 基于 OPA 官方文档中的教程。该教程使用 python，但我决定用 Go 和 labstack 的 [echo](https://echo.labstack.com/) 框架来构建它。

这是应用程序的概述(包括策略)。

*   成员如下:
    *   爱丽丝
    *   上下移动
    *   傻瓜
    *   贝蒂(女子名)
    *   大卫
*   鲍勃是爱丽丝的经理，贝蒂是查理的经理。大卫属于人力资源部。
*   用户可以查看自己的工资
*   经理可以查看其下属的工资
*   HR 可以查看每个人的工资

这是一个概览图。

[![](img/d04083e3be9251242e154eb31e2bb56d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wdVqwYrY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9z9ulzctqcwwv4hb4006.png)

让我们从非 OPA 版本开始。

## 没有 OPA 的决策

我已经创建了一个策略执行中间件来执行对 API 端点的每个请求的策略。在中间件中，我只是简单地请求一个`PolicyGateway`来检查请求者是否被允许调用 API。下面的代码显示了概述。( [GitHub](https://github.com/kenfdev/opa-api-auth-go/blob/40408e7b5b4d99df9e98d12e92de9cd503403919/middleware/policyenforcer.go#L14-L24) )

```
...
logrus.Info("Enforcing policy with middleware")

allow := gateway.Ask(c)

if allow {
    logrus.Info("Action is allowed, continuing process")
    return next(c)
} else {
    logrus.Info("Action was not allowed, cancelling process")
    return c.String(http.StatusForbidden, "Action not allowed")
}
... 
```

`PolicyGateway`的接口只是一个方法`Ask`，它返回一个表示允许或拒绝的`bool`。

```
type PolicyGateway interface {
    Ask(echo.Context) bool
} 
```

我将网关的实现称为`PolicyLocalGateway`，基本上下面的代码就是它的功能。( [GitHub](https://github.com/kenfdev/opa-api-auth-go/blob/master/gateway/policy.go#L25-L82) )。

```
func (gw *PolicyLocalGateway) checkGETSalary(c echo.Context, claims *entity.TokenClaims) bool {
    userID := c.Param("id")

    if yes := gw.checkIfOwner(userID, claims); yes {
        logrus.Info("Allowing because requester is the owner")
        return true
    }

    if yes := gw.checkIfSubordinate(userID, claims); yes {
        logrus.Info("Allowing because target is a subordinate of requester")
        return true
    }

    if yes := gw.checkIfHR(claims); yes {
        logrus.Info("Allowing because requester is a member of HR")
        return true
    }

    logrus.Info("Denying request")
    return false
} 
```

您可以看到许多`if`语句，并且可以假设从长远来看这将很容易增加代码量。同样，理解所有这些`if`语句和缩进(嗯...那可能是我的错，但你明白了。

让我们检查一些 HTTP 请求，看看会发生什么。

首先，让我们从爱丽丝开始。由于 Alice 是普通员工，所以她只能查看自己的工资。

```
$ curl -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwiaWF0IjoxNTE2MjM5MDIyLCJ1c2VyIjoiYWxpY2UiLCJzdWJvcmRpbmF0ZXMiOltdLCJociI6ZmFsc2V9.WTR-Or-vS1yFBFHk7UyqZxsNhtTNSWeazJ57SJ7V4qY" \
  http://localhost:1323/finance/salary/alice

100 
```

您可以看到值`100`返回时没有错误。让我们看看 Alice 是否可以查看 Bob 的工资。

```
$ curl -H "Authorization: Bearer <Alice's JWT>" \
  http://localhost:1323/finance/salary/bob

Action not allowed 
```

现在被禁止了，因为 Alice 是普通员工，不能查看其他员工的工资。日志显示请求也被拒绝。

```
app_1  | msg="Enforcing policy with middleware"
app_1  | msg="Checking GET salary policies" claims="&{alice [] false { 0  1516239022  0 1234567890}}" userID=bob
app_1  | msg="Denying request"
app_1  | msg="Action was not allowed, cancelling process" 
```

另一方面，让我们看看 Bob 是否可以查看 Alice 的工资。

鲍勃的 JWT 有效载荷如下:

```
{  "sub":  "1234567890",  "iat":  1516239022,  "user":  "bob",  "subordinates":  ["alice"],  "hr":  false  } 
```

他是他的下属，这意味着他是爱丽丝的经理。

```
$ curl -H "Authorization: Bearer <Bob's JWT>" \
  http://localhost:1323/finance/salary/alice

100 
```

您可以确认 Bob 被允许查看 Alice 的工资。这在上面代码中的逻辑`gw.checkIfSubordinate`中进行了检查。现在鲍勃查看大卫的工资怎么样？

```
$ curl -H "Authorization: Bearer <Bob's JWT>" \
  http://localhost:1323/finance/salary/david

Action not allowed 
```

当然，Bob 不允许查看 David 的工资，因为他没有权限这样做。

现在，让我们看看大卫作为 HR 的一员能做些什么？

大卫的 JWT 有效载荷如下。

```
{  "sub":  "1234567890",  "iat":  1516239022,  "user":  "david",  "subordinates":  [],  "hr":  true  } 
```

因为他是 HR 的成员，所以他的 JWT 有效载荷中有`hr: true`。

```
$ curl -H "Authorization: Bearer <David's JWT>" \
  http://localhost:1323/finance/salary/bob

200 
```

成功！他还可以看到贝蒂、爱丽丝、查理的工资。这是因为他通过了上面代码中的检查`gw.checkIfHR`。

这应该很简单。现在，让我们看看当 OPA 进入游戏时会发生什么。

## 决策用 OPA

添加 OPA 非常简单。`PolicyGateway`的实现会改变。我称之为`PolicyOpaGateway`，这一次，我没有实现任何与策略规则相关的东西(不再是那些`if`语句)，因为我想**将它们委托给 OPA。**

主要变化是:

*   我把减压阀的政策写到`*.rego`文件中
*   我在主应用程序旁边添加了 OPA 服务器
*   我已经实现了`PolicyGateway`,用足够的有效负载向 OPA 服务器发出外部 HTTP 请求，以便 OPA 做出决策

### 撰写减压阀政策

在减压阀写的政策如下。

```
package httpapi.authz

# io.jwt.decode_verify
# https://www.openpolicyagent.org/docs/latest/language-reference/#tokens
token = t {
  [valid, _, payload] = io.jwt.decode_verify(input.token, { "secret": "secret" })
  t := {
    "valid": valid,
    "payload": payload
  }
}

default allow = false

# Allow users to get their own salaries.
allow {
  token.valid

  some username
  input.method == "GET"
  input.path = ["finance", "salary", username]
  token.payload.user == username
}

# Allow managers to get their subordinate's salaries.
allow {
  token.valid

  some username
  input.method == "GET"
  input.path = ["finance", "salary", username]
  token.payload.subordinates[_] == username
}

# Allow HR members to get anyone's salary.
allow {
  token.valid

  input.method == "GET"
  input.path = ["finance", "salary", _]
  token.payload.hr == true
} 
```

它与[教程版本](https://www.openpolicyagent.org/docs/latest/http-api-authorization/)几乎相同。稍微不同的是，它使用密钥`secret`来验证 JWT 令牌。

#### 侧注:减压阀游乐场

关于 OPA，我想指出的一个很棒的特点是减压阀游乐场。

[![](img/eeb41af7cf62274b6aecebb9526f96a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d8yIPU_K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mqrb9d6ohgfqwm3x50iy.png)

您可以编写策略并在浏览器上快速评估它们！此外，您可以通过链接共享策略，供其他人查看(例如，他们可以调试您的策略)。这是上述政策的链接。输入设置为爱丽丝的 JWT 和她自己的工资路径。

[https://play.openpolicyagent.org/p/ww8qdEHdn0](https://play.openpolicyagent.org/p/ww8qdEHdn0)

您可以检查输出，看看结果是否允许。

### 添加 OPA 服务器

在用减压阀创建了一个策略之后，我通过在 [docker-compose.yml](https://github.com/kenfdev/opa-api-auth-go/blob/add-opa/docker-compose.yml#L11-L17) 中添加下面几行来设置主应用程序旁边的 OPA 服务。

```
 pdp:
    image: openpolicyagent/opa:0.12.0
    ports:
      - 8181:8181
    volumes:
      - ./opa:/etc/opt/opa
    command: ["run", "--server", "/etc/opt/opa/authz.rego"] 
```

> 仅供参考，PDP 代表政策决策点。我只是想给它一个通用的名字。

### 请求 OPA 做出决定

现在 OPA 已经准备好了，我已经实现了`PolicyGateway`,因此它向 OPA 服务器发出一个外部 HTTP 请求以进行决策。

#### 准备输入

OPA 需要足够的信息来做出决策。对于这个简单的应用程序，我需要告诉 OPA:

*   请求者是谁-包括属性(JWT 令牌)
*   请求了什么端点(请求路径和 HTTP 方法)

代码是这样的。我已经使用 echo 的 [JWT 中间件](https://echo.labstack.com/middleware/jwt)轻松提取 JWT 数据。

```
...
token := c.Get("token").(*jwt.Token)
// After splitting, the first element isn't necessary
// "/finance/salary/alice" -> ["", "finance", "salary", "alice"]
paths := strings.Split(c.Request().RequestURI, "/")[1:]
method := c.Request().Method

// create input to send to OPA
input := &opaInput{
    Token:  token.Raw,
    Path:   paths,
    Method: method,
}
// don't forget to wrap it with `input`
opaRequest := &opaRequest{
    Input: input,
}
... 
```

一个小问题是 HTTP 请求负载需要用一个`input`键包装，如上面的代码所示。当然，这是在[文件](https://www.openpolicyagent.org/docs/latest/rest-api/#get-a-document-with-input)中提到的。

#### HTTP 请求

我已经通过一个环境变量(这里的和这里的)将 OPA 的端点注入了 PolicyGateway。因此，可以通过调用`gw.endpoint`来访问它。因此，请求将类似于下面的代码。( [GitHub](https://github.com/kenfdev/opa-api-auth-go/blob/add-opa/gateway/policy.go) )

```
type opaResponse struct {
    Result bool `json:"result"`
}

...

// request OPA
resp, err := http.Post(gw.endpoint, "application/json", bytes.NewBuffer(requestBody))
...
body, err := ioutil.ReadAll(resp.Body)
...
var opaResponse opaResponse
err = json.Unmarshal(body, &opaResponse) 
```

它只是简单地询问 OPA 并将结果返回给调用者。既然代码准备好了，那就再来一次吧！

```
# David (a member of HR) viewing Betty's salary
$ curl -H "Authorization: Bearer <David's JWT>" http://localhost:1323/finance/salary/betty

200 
```

看起来不错。让我们看看日志。

```
app_1  | msg="Enforcing policy with middleware"
app_1  | msg="Requesting PDP for decision" method=GET path="[finance salary betty]" token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwiaWF0IjoxNTE2MjM5MDIyLCJ1c2VyIjoiZGF2aWQiLCJzdWJvcmRpbmF0ZXMiOltdLCJociI6dHJ1ZX0.TBXtM_p-VIlgx9NSLn4An6hELr2UB3CrqHUJmofQorM
pdp_1  | l":"info","msg":"Sent response.","req_id":2,"req_method":"POST","req_path":"/v1/data/httpapi/authz/allow","resp_bytes":15,"resp_duration":4.1901,"resp_status":200,"time":"2019-06-30T14:29:52Z"}
app_1  | msg=Decision result=true
app_1  | msg="Action is allowed, continuing process"
app_1  | msg="Processing salary request" id=betty
app_1  | msg="Fetched salary" id=betty salary=200 
```

应用程序正在请求 OPA 并接收结果`true`。你可以在下面的操场上看到同样的结果。

[https://play.openpolicyagent.org/p/w0kohf47we](https://play.openpolicyagent.org/p/w0kohf47we)

让我们看看 Alice 对 Bob(对她的经理)的请求是否会失败。

```
$ curl -H "Authorization: Bearer <Alice's JWT>" \
  http://localhost:1323/finance/salary/bob

Action not allowed 
```

厉害！日志呢？

```
app_1  | msg="Enforcing policy with middleware"
app_1  | msg="Requesting PDP for decision" method=GET path="[finance salary bob]" token=<Alice's JWT>
pdp_1  | l":"info","msg":"Received request.","req_id":1,"req_method":"POST","req_path":"/v1/data/httpapi/authz/allow","time":"2019-06-30T21:29:59Z"}
app_1  | msg=Decision result=false
app_1  | msg="Action was not allowed, cancelling process" 
```

一切都在按预期工作！(这里是[游乐场](https://play.openpolicyagent.org/p/j1xmddfd9R))

我们已经成功地将本地策略引擎替换为 OPA。您可以看到如何通过将策略决策委托给 OPA 来实现关注点的分离。

您可以在[我的 PR 这里](https://github.com/kenfdev/opa-api-auth-go/pull/1/files)查看代码的全部更改。

## 总结起来

这是一个非常简单的例子，展示了如何通过 HTTP API 服务器利用 OPA 的强大功能。

从这个简单的例子中我能想到的一些优点和缺点是:

**利弊**利害
分离。在我的主应用程序中，我可以专注于我需要实现的业务逻辑。我不必写所有那些`if`语句，也不必在每次策略改变时修改我的代码。如果我更改了代码，这意味着我必须再次部署它。至于 OPA，您可以动态地改变您的策略。当您更改策略时，不需要重新部署。

此外，我不能在这个帖子里提到它，但是减压阀也可以和减压阀一起测试。您应该看看"[我如何测试策略？](https://www.openpolicyagent.org/docs/latest/how-do-i-test-policies/)”。

我在 slack 频道听到的一些其他优点如下:

*   安全性/合规性可以独立于审计代码来审计策略。
*   决策的一致记录；有助于 SIEM 集成([决策日志](https://www.openpolicyagent.org/docs/latest/decision-logs/))
*   跨不同微服务的一致策略语言(减压阀)；帮助人们理解在一系列服务中什么是真正授权的
*   在构建 UI 时，它也需要实现相同的策略；如果跨服务解耦并保持一致，会更容易(例如，看看 Chef Automate 的[自省](https://github.com/chef/automate/tree/master/components/authz-service#introspection-how-to-query-permissions))
*   从一个专门构建的解决方案开始，您可以从简单开始，但随着您的 authz 需求的发展而增长。
*   避免对 authz 系统进行一系列的一次性扩展，这样最终看起来就像一个科学怪人。你可以看到我上面构建的 monolith 应用程序最终会变成什么样子。

我看不出使用 OPA 有什么大的缺点，但是正如这篇文章所展示的，标准的整体服务变成了微服务方法。这意味着它将比不使用 OPA 增加复杂性。但这更多的是微服务而不是 OPA 本身。此外，减压阀对新手来说是一条额外的学习曲线。我认为这是值得努力的，我认为随着 OPA 的发展，减压阀会越来越受欢迎。

最后，可能出现的一些问题是:

我是否必须将所有信息传递给 OPA 进行决策？
阿法克，最简单的方法就是通过一切，但这在很多情况下可能并不容易或不可行。一种方法是使用 [http.send 内置函数](https://www.openpolicyagent.org/docs/latest/language-reference/#http)从 OPA 内部发出外部请求。您还可以使用 OPA 的[包](https://www.openpolicyagent.org/docs/latest/bundles/)定期从外部服务获取数据。更多信息可以在官方文档“[指南:身份和用户属性](https://www.openpolicyagent.org/docs/latest/guides-identity/)”中找到。它有非常好的细节。

**我可以用 OPA 做库吗？**
也许你因为各种原因不想要额外的集装箱。是的，OPA 可以用作一个 Go 库，你可以看看一些使用 OPA 作为库的很棒的 OSS 项目。我知道的一些是:

*   厨师自动化 ( [GitHub](https://github.com/chef/automate/tree/master/components/authz-service/engine/opa) )
*   [ory/keto](https://www.ory.sh/docs/keto/) ( [GitHub](https://github.com/ory/keto/tree/master/engine/ladon) )
*   [conftest](https://github.com/instrumenta/conftest)

我可以用 OPA 创建类似 AWS IAM 的东西吗？
这个问题我在 OPA slack 听过好几次了(包括我自己)，如果你有同样的问题，你一定要看看[Chef Automate authorization service 文档](https://github.com/chef/automate/tree/master/components/authz-service)。这份文件太神奇了。我是真心的。肯定有很多方法可以用 OPA 实现类似 AWS IAM 的东西，但这肯定是一个很好的实例。

我假设还有很多问题，但获得支持的最佳方式是加入 [OPA slack 频道](https://slack.openpolicyagent.org/)。那里的人非常支持我，我在那里学到了很多知识。

感谢您阅读这篇长文！！！我非常感谢 Ash( [@ashtalk](https://twitter.com/ashtalk) )和 Tim( [@tlhinrichs](https://twitter.com/tlhinrichs) )提前评论了这篇文章！我希望我能为 OPA 社区贡献和回馈我所拥有的一切！

## 引用

*   深潜:开放策略代理[https://www.youtube.com/embed/Vdy26oA3py8](https://www.youtube.com/embed/Vdy26oA3py8)