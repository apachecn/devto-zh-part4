# 更新联合用户的 Azure AD 密码

> 原文：<https://dev.to/merill/update-the-azure-ad-password-of-a-federated-user-p0e>

有时您需要更新从 Active Directory 同步的用户的 Azure AD 密码。但是，运行 Set-azureadauserpassword 或 Set-MsolUserPassword 失败，并出现以下错误之一。

*   Set-MsolUserPassword:您不能为联合用户重置密码。
*   Set-AzureADUserPassword:执行 SetUser 代码时出错:Request_BadRequest

有一个简单的方法可以解决这个限制。您需要做的只是临时将用户的 UserPrincipalName 更改为受管域的用户名，更新密码，然后将 UserPrincipalName 更改回联盟域。

```
# Change UPN to managed domain  Set-AzureADUser  -ObjectId  xxxxx  -UserPrincipalName  user@domain.onmicrosoft.com  # Update the password  Set-AzureADUserPassword  -ObjectId  xxxxx  # Change UPN back to the federated domain  Set-AzureADUser  -ObjectId  xxxxx  -UserPrincipalName  user@domain.com 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。用户最终会被注销，然后必须重新登录。

新密码将一直保留，直到用户在 Active Directory 中更改其本地密码，然后该密码将同步到 Azure Active Directory。