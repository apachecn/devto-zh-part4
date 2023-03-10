# 如何使用 C#在 Dynamics CRM 365 Online 中获取 OAuth 访问令牌

> 原文：<https://dev.to/benjsoft/how-to-obtain-an-oauth-access-token-in-dynamics-crm-365-online-using-c-283e>

以下代码片段演示了如何从 Dynamics CRM online 实例获取 OAuth 访问令牌，当您在客户端或服务器端代码中访问 Dynamics CRM 365 web 服务时，您可以使用该令牌传递身份验证和授权。

```
using (HttpClient client = new HttpClient())
{
   client.BaseAddress = new 
       Uri("https://login.microsoftonline.com/common/oauth2/token");

   HttpRequestMessage request = new HttpRequestMessage();
   request.Method = HttpMethod.Post;

   var keysValues = new List<KeyValuePair<string, string>>();
   keysValues.Add(new KeyValuePair<string, string>("client_id", 
       "2ad88395-b77d-4561-9441-d0e40824f9bc"));
   keysValues.Add(new KeyValuePair<string, string>("resource", url));
   keysValues.Add(new KeyValuePair<string, string>("username", username));
   keysValues.Add(new KeyValuePair<string, string>("password", password));
   keysValues.Add(new KeyValuePair<string, string>("grant_type", "password"));

   request.Content = new FormUrlEncodedContent(keysValues);

   HttpResponseMessage response = client.SendAsync(request).Result;
   return response.StatusCode == HttpStatusCode.OK;
} 
```

在上面的代码片段中，唯一要更改的是用户名和密码，这是用于对您的实例进行身份验证和授权的 Dynamics 365 凭据，以及您的 Dynamics CRM 实例的 URL(例如 https://benjsoftdemo . CRM 3 . Dynamics . com)。

client_id 是针对 Dynamics 365 Online 实例设置的默认客户端 id。

您可以使用 Postman 测试以下代码片段。

![](img/ce32df254ce297affd1079d1587b8a17.png)

如果成功，响应将包含 JSON 响应负载，该负载包含您的 access_token。

![](img/528eb7753388d4255977bd81f43f70ea.png)

然后，您可以通过编程使用 access_token 在访问 CRM webservice 调用时对您进行身份验证和授权。

更多信息可以参考以下链接 [Dynamics 365 在线认证用户凭证](https://community.dynamics.com/crm/b/magnetismsolutionscrmblog/posts/dynamics-365-online-authenticate-with-user-credentials)。