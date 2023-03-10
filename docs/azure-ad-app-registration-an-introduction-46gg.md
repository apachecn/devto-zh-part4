# Azure 广告应用程序注册简介

> 原文：<https://dev.to/omiossec/azure-ad-app-registration-an-introduction-46gg>

需要处理企业认证内部业务应用程序。企业用户不希望为每个新应用程序使用新的登录名/密码。运营部门希望应用程序能够安全地使用 Active Directory，并且不会危及其基础架构的安全性。Dev 希望专注于应用程序的商业价值，而不是登录过程。

接下来是微软身份平台和 Azure 广告应用注册。它为开发人员提供了一种简单而安全的方式来提供对应用程序的安全登录或/和对 Graph API、SharePoint 等其他 Azure 资源的访问……它还能够保护内部 API。它让 Ops 管理分配并快速授予或撤销权限。

为此，您需要在 Azure Active Directory 中注册应用程序，并使用微软提供的一个身份验证/授权库。

但是这个过程并不总是顺利的。如何做到这一点，如何让运营部门理解流程，如何管理流程，获得开发人员所需的速度，以及运营部门和安全团队所需的安全性和稳定性？

首先，我们需要了解 app 注册的背后是什么，概念是什么，场景是什么。接下来，我们需要看看如何执行注册，然后如何管理它们。当然，如何在门户之外自动化整个流程。

让我们从应用程序开始。在微软身份平台或 MIP 中，应用程序可以是任何东西，网页、API、移动应用程序，甚至脚本也可以是应用程序。MIP 的目的是为应用程序和 API 提供安全登录，并为 Graph API 或其他微软服务提供许可。

让我们举一些例子；

想象一个场景，一个内部的 web 应用程序向企业用户以及拥有微软账户或 Twitter 账户的人开放。在这种情况下，我们可以谈论一个认证过程。MIP 充当身份服务来验证用户是她/他所说的那个人。

设想第二种情况，一个移动应用程序可以提供一些关于您当前位置的信息，并从他们的 outlook 日历中列出最近的 5 次会议以及他们从当前位置前往会议室所需的时间。这里我们有相同的身份验证概念，验证用户声称她/他是她/他所假装的。但是还有一个概念，授权，我们需要用户的同意才能访问一些信息(即他的名字，邮箱，…)和访问用户的日历。

这里有两个概念，身份验证或 AuthN，当您挑战一个身份来证明您是谁时，以及授权或 AuthZ，当您在对资源进行身份验证之后被授权时。

使用 MIP，授权可以分为同意和许可两部分。

同意是来自用户或目录管理的动作，以查看应用程序将从他们那里看到什么，并接受或拒绝

权限是赋予应用程序在一个或多个资源上的特权的行为，这里是读取用户日历的能力。

权限可以描述为应用程序如何代表登录用户访问资源。

MIP 平台有两种类型的权限。用户在场时的委托权限，以及用户或管理员(取决于要使用的资源)同意使用资源时的委托权限。永久权限，如果用户没有连接，应用程序可以使用资源事件，这里只有管理员可以授予权限。

MIP 使用 OAuth 2.0 和 OpenID 授权流模型，该模型定义了身份验证流程中的四个角色:

*   用户，资源所有者，他/她使用应用程序/网页并给予许可

*   应用程序充当 OAuth 客户端，它代表用户请求令牌并消耗资源

*   微软身份平台，授权服务器。它颁发令牌，授予或拒绝对资源的访问，并验证用户和应用程序声明

*   用户的资源，在资源服务器上，它是用户数据的访问点。它通常基于图形 API

OAuth 2.0 和 OpenID 是一枚硬币的两面。OpenID 用于认证( **AuthN** )，OAuth 2.0 用于授权( **AuthZ** )。

流程使用端点。您可以通过使用 PowerShell 和 AZ 模块查询 oAuth 2.0 元数据文档来获取信息。

```
$TennantId  =  (Get-AzTenant).id[0]  $oAuth2TenantInfmation  =  Invoke-RestMethod  -Uri  "https://login.microsoftonline.com/$($TennantId)/v2.0/.well-known/openid-configuration"  -Method  get 
```

Enter fullscreen mode Exit fullscreen mode

$ oAuth2TenantInfmation 包含您需要的所有端点和信息。

获取授权端点:

```
$oAuth2TenantInfmation.authorization_endpoint 
```

Enter fullscreen mode Exit fullscreen mode

在 AuthN 阶段，用户被重定向到 Azure 中的一个端点([https://login.microsoftonline.com/*](https://login.microsoftonline.com/*)*/oauth 2/v 2.0/authorize，其中租户 ID 用于当前租户，通用于任何 works 或 Microsoft 帐户，消费者仅用于 Microsoft 帐户，组织仅用于 works 帐户)。

用户输入他/她的凭证，同意许可并获得一个令牌(ID_token)以返回到应用程序(通过返回 URL)。应用程序可以验证令牌以识别用户。

这是 OpenID/oAuth 2.0 的基本认证/授权过程

微软提供了几个库和框架来处理授权/认证过程

为了能够登录和访问资源，应用程序需要注册到 Azure Active Directory。但是在进一步学习应用程序注册之前，还有一些其他的概念需要理解。

首先，您需要确定谁将使用该应用程序？有几种情况:

*   限于一个 Azure AD 租户，这是大多数内部应用程序的情况

    端点 URI 包含租户 ID

*   对任何 Azure AD 租户开放，这是多租户应用的情况
    URI 包含组织的端点

*   对任何人开放，包括其他 Azure AD 租户和微软帐户，这是公共应用程序的情况
    端点 URL 包含公共(或消费者，如果应用程序只接受微软帐户)

另一件事是应用程序如何工作和行为。它将决定使用哪个客户端库以及权限如何同意。

我们需要了解应用程序如何与用户交互。应用程序是直接与用户交互，还是不需要任何用户交互？

换句话说，对于一个读取用户日历的应用程序，它是只在用户打开应用程序时读取它，还是每次在服务器端的后台任务中读取它。

同样，应用程序如何工作，它是前端/后端应用程序、API 还是桌面应用程序？

这两个问题有助于选择正确的库以及如何设置许可和同意。

| 应用场景 | 图书馆 | 权限分配 | 观众 | 登记 |
| --- | --- | --- | --- | --- |
| 单页 App 或前/后 app (React.js，...) | MSAL.js | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 重定向 URI |
| Node.js web 应用程序 | Passport.js | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 重定向 URI 并注销 URI |
| 。net web 应用程序 | 的身份模型扩展。网络与 MSAL.net | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 重定向 URI 并注销 URI |
| Python web 应用程序 | MSAL.python | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 重定向 URI 并注销 URI |
| Java web 应用程序 | MSAL.java | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 重定向 URI 并注销 URI |
| Web 应用程序调用 web API | MSAL 与[confidential client 应用](https://docs.microsoft.com/en-us/dotnet/api/microsoft.identity.client.confidentialclientapplication?view=azure-dotnet) | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 秘密或证书 |
| 调用 web API 的桌面应用程序 | MSAL 与 [PublicClientApplication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.identity.client.publicclientapplication?view=azure-dotnet) | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 重定向 URI |
| RestFull API | -你好 | 用户或租户管理员 | Works 或 Microsoft 帐户 | 使用 AccessTokenAcceptedVersion = 2 更改应用程序清单 |
| 移动应用程序 | MSAL 与 [PublicClientApplication](https://docs.microsoft.com/en-us/dotnet/api/microsoft.identity.client.publicclientapplication?view=azure-dotnet) | 用户或租户管理员 | Works、Microsoft 帐户或 Azure B2C | 需要添加平台(IOS/Android)，重定向 URI 将由系统计算 |
| 守护进程 | MSAL 与[confidential client 应用](https://docs.microsoft.com/en-us/dotnet/api/microsoft.identity.client.confidentialclientapplication?view=azure-dotnet) | 租户管理员 | 仅工作账户 | 秘密或证书 |

我们如何注册应用程序，因为我们看到我们可能需要一个 URI，有时需要一个证书或一个秘密？

应用程序注册可以在 Azure 门户网站中完成，也可以使用 PowerShell。对于 PowerShell，您将需要 AZ 模块。它与 Windows PowerShell 5.1 和 PowerShell core
一起工作

```
install-module  –name  AZ  –scope  CurrentUser  Import-module  –name  AZ  Login-AzAccount  $ApplicationName  =  "DemoDev-To"  $ApplicationUri  =  "https://DemoDev-To.azurewebsites.net"  $ApplicationReplyUri  =  @("https://DemoDev-To.azurewebsites.net/signin-oidc","https://localhost:44321/signin-oidc")  $AzureApplicationObject  =  New-AzADApplication  -DisplayName  $ApplicationName  -IdentifierUris  $ApplicationUri  -ReplyUrls  $ApplicationReplyUri  –HomePage  $ApplicationUri 
```

Enter fullscreen mode Exit fullscreen mode

ApplicationName 将显示在 Azure 门户中，并在以后标识该应用程序。

对于 ReplyUrls 参数，我使用了数组而不是字符串。一个应用程序可以有多个环境(开发/UAT/生产)并响应不同的 URI。

$AzureApplicationObject 包含配置结果。您将获得两个重要的 ID，ObjectID 和 ApplicationID。配置客户端库需要这两个对象。

主页不是必需的，但它有助于稍后在门户中识别应用程序。

现在让我们尝试创建一个基于秘密的应用程序注册。

```
$ApplicationName  =  "DemoDev-To-Key"  $ApplicationUri  =  "https://DemoDev-To-Key.azurewebsites.net"  $ApplicationReplyUri  =  @("https://DemoDev-To-Key.azurewebsites.net/signin-oidc","https://localhost:44321/signin-oidc")  $AzureApplicationObject  =  New-AzADApplication  -DisplayName  $ApplicationName  -IdentifierUris  $ApplicationUri  -ReplyUrls  $ApplicationReplyUri  –HomePage  $ApplicationUri  $AppPlainTextPassWord  =  "SomethingYouCanType"  $SecureStringPassword  =  ConvertTo-SecureString  -String  $AppPlainTextPassWord  -AsPlainText  -Force  $StartDate  =  get-date  $EndDate  =  $startDate.AddYears(2)  New-AzADAppCredential  -ObjectId  $AzureApplicationObject.ObjectID  -Password  $SecureStringPassword  -startDate  $StartDate  -enddate  $EndDate  Get-AzADApplication  -ObjectId  $AzureApplicationObject.ObjectID  |  New-AzADServicePrincipal  -startDate  $StartDate  -enddate  $EndDate 
```

Enter fullscreen mode Exit fullscreen mode

应用程序现在在 Azure AD 中创建，您现在可以开始分配用户。

关于应用程序注册的简短介绍到此结束。如您所见，这个主题可能很复杂，它涉及开发人员和运营人员。

下一次我们将看到如何为一个注册的应用程序设置微软 API 的权限，以及如何管理用户和其他东西。