# 使 ASP.NET 窗体身份验证票证服务器端无效

> 原文：<https://dev.to/alexjitbit/invalidating-asp-net-forms-authentication-tickets-server-side-38n7>

有时候你需要“注销其他用户会话”。为了防止 cookie 重放攻击，或者一个非常常见的用例，当用户更改密码时注销其他会话。ASP.NET 没有内置的方法来做到这一点，但有一个简单的解决方案。

一个`FormsAuthenticationTicket`对象有一个名为`IssueDate`的内置属性。因此，您可以很容易地使所有“早于日期 X”的 forms-auth 票证无效。在我们的例子中，它将是“比最后一次密码更改旧”

例如，您可以读取`Application_AcquireRequestState`(在“global.asax”中)中的`IssueDate`属性，如果日期“太旧”(即比用户最后一次更改密码的时间更早)，则注销用户。

这里有一些代码给你:

```
protected void Application_AcquireRequestState(object sender, EventArgs e)
{
    //check if token should be invalidated
    if (User.Identity.IsAuthenticated)
    {
        var lastPswChange = GetPswChangeDate(User.Identity.Name);
        HttpCookie authCookie = Request.Cookies[FormsAuthentication.FormsCookieName];
        FormsAuthenticationTicket authTicket = FormsAuthentication.Decrypt(authCookie.Value);

        //psw changed since this auth-token has been issued
        if(authTicket.IssueDate < lastPswChange)
        {
            //log him out
            Logout();
            Response.Redirect("~/User/Login");
            return;
        }
    }
}

private void Logout()
{
    Session.Abandon();
    Session.Clear();
    FormsAuthentication.SignOut();
} 
```

您必须自己实现 GetPswChangeDate 方法。

“密码更改日期”只是一个例子。您可以在数据库中为每个用户保存一个和其他日期，并将其显式设置为您喜欢的任何值。