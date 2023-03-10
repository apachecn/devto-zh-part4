# 使用 GraphQL 和 Apollo 服务器进行 GitHub 授权

> 原文：<https://dev.to/eveporcello/github-authorization-with-graphql-and-apollo-server-3hf7>

*本文原载于[月球公路](https://moonhighway.com/github-authorization)。*

授权和认证用户是构建 web 应用程序的重要部分。我们需要知道用户是谁，他们可以看到什么。对于 GraphQL 应用程序，我们如何确保允许用户查看内容？我们需要对他们进行身份验证，以确保我们能够识别他们。我们还需要授权他们证明他们有权访问应用程序。

有许多策略可用于对用户进行身份验证和授权。在本文中，我们将仔细研究如何用 GitHub 处理授权。我们将依靠 GitHub 来提供帐户细节，并且我们将能够为每个试图登录的用户使用 GitHub 凭证。

## 设置 GitHub OAuth

设置 GitHub 授权的第一步是设置一个 GitHub app。完成此操作的步骤如下:

1.  前往[https://www.github.com](https://www.github.com)并登录。
2.  转到设置。![Settings](img/e645bfc73b2168462d844fb061b3f266.png)
3.  转到开发者设置。![Developer Settings](img/e650d19c611eeb9cbf99682fa49c8d0e.png)
4.  从左侧导航栏中选择 OAuth 应用程序。![OAuth](img/af03b090e160d0f5cb71943e10ce8014.png)
5.  选择新 Oauth 应用程序。![New OAuth App](img/e821268dfdb95943ad44d7bea9ad9b9a.png)
6.  添加这些设置:
    *   **应用名称**:本地主机 3000
    *   **首页网址**:[http://localhost:3000](http://localhost:3000)
    *   **应用描述**:使用 GitHub 授权用户的测试应用
    *   **授权回调网址**:[http://localhost:3000](http://localhost:3000)![App Settings](img/b664ff7e117942a7a0727fed1f3a8b15.png)
7.  单击注册应用程序。
8.  从帐户页面复制客户端 ID 和客户端密码。

你的 GitHub app 都设置好了！现在，您将客户端 ID 和客户端密码复制并粘贴到项目根目录下名为`.env`的新文件中。它看起来应该是这样的，带有您自己提供的变量:

```
CLIENT_ID=<Your_Client_ID_Here>
CLIENT_SECRET=<Your_Client_Secret_Here> 
```

## 授权流程

现在我们的应用程序已经用 GitHub 设置好了，是时候将授权和认证过程集成到我们的 GraphQL 应用程序中了。我们将通过一个包含 GitHub 客户端 ID 的 URL 向 GitHub 发送一个请求，请求用户允许访问他们的 GitHub 帐户。然后我们将获取响应代码，并将其传递回 GitHub 以获得一个令牌。令牌基本上是说“好的，这个用户被允许使用这个应用程序。”他们是经过认证的，他们是经过授权的。

我们将构建并测试所有这些步骤，但基本过程可以归结为以下几点:

1.  客户端:使用带有`client_id`的 url 向 GitHub 请求代码。
2.  用户:允许客户端应用程序访问 GitHub 上的帐户信息
3.  GitHub:发送代码到 OAuth 重定向 url: `http://localhost:3000?code=XYZ`
4.  客户端:发送带代码的 GraphQL 变异`authorizeWithGithub(code)`
5.  API:用凭证请求一个 GitHub`access_token`:`client_id`，`client_secret`和`client_code`
6.  GitHub:用可用于未来信息请求的`access_token`进行响应
7.  API:用`access_token`请求用户信息
8.  GitHub:响应用户信息:`name, githubLogin, avatar`

为了在服务器上进行设置，我们需要创建两个查询和一个变异:

1.  一个获取 GitHub 登录 url 的查询(这会给我们代码):`githubLoginUrl`。
2.  一个用 GitHub 授权用户的变种:`authorizeWithGithub`
3.  返回当前登录用户的查询:`me`。

### 第一步:创建`githubLoginUrl`查询

要授权一个用户，我需要将我的`CLIENT_ID`发送到 GitHub。一旦他们识别出客户端 ID，他们就会让我使用我的 GitHub 用户名和密码来授权这个应用程序。我需要粘贴到浏览器中的 URL 如下:

```
https://github.com/login/oauth/authorize?client_id=${Client-ID-here}&scope=user 
```

如果我可以发送一个返回该 URL 的查询，而不是从内存中键入这些内容，这不是很酷吗？所以我可以把它复制粘贴到浏览器里？是的，那会非常酷，所以让我们在我们的模式中设置它。当我发送`githubLoginUrl`查询时，它应该返回 URL 字符串:

```
type  Query  {  githubLoginUrl:  String!  } 
```

现在我们需要编写解析器来为我们构建 URL。我们已经有了链接的结构(`https://github.com/login/oauth/authorize?client_id=${Client-ID-here}&scope=user`)，所以我们需要做的就是使用模板字符串
从环境变量中提供`CLIENT_ID`

```
Query: {
  githubLoginUrl: () =>
    `https://github.com/login/oauth/authorize?client_id=${
      process.env.CLIENT_ID
    }&scope=user`;
} 
```

当我发送`githubLoginUrl`查询时，我会收到一个字符串，可以很容易地粘贴到浏览器中。该查询将如下所示:

```
query  {  githubLoginUrl  } 
```

并将返回:

```
{  "data":  {  "githubLoginUrl":  "https://github.com/login/oauth/authorize?client_id=YOUR-UNIQUE-CLIENT-ID&scope=user"  }  } 
```

当我访问这个 URL 时，浏览器中的 GitHub 应用程序会要求我允许这个应用程序访问我的 GitHub 帐户详细信息。接下来，我将授权应用程序并输入我的密码。一旦我授权的应用程序，一个新的网址将出现在地址栏。然后我会从 URL 字符串的末尾抓取代码:

```
http://localhost:3000/?code=<YOUR-UNIQUE-CODE> 
```

我们暂时保留这段代码，因为我们需要将它发送回 GitHub 以请求一个令牌。这个过程是相当手工的，但是稍后当我们构建一个客户端应用程序时，我们将通过 UI 元素来处理所有这些。换句话说，当我单击登录按钮时，所有这些都将在幕后发生。就目前而言，这是测试身份验证过程是否正常工作的好方法。

### 第二步:构建`authorizeWithGithub`突变

现在我们已经有了代码，我们需要将它传递回 GitHub，以获得令牌和用户信息。首先，我们将添加`AuthPayload`类型。这将返回令牌和所有用户详细信息。这是从`authorizeWithGithub`突变中返回的类型。让我们将两者都添加到模式中:

```
type  AuthPayload  {  githubToken:  String!  user:  User!  }  type  Mutation  {  ...  authorizeWithGithub(code:  String!):  AuthPayload!  } 
```

`AuthPayload`类型仅用作对授权突变的响应。它包含由变异授权的用户以及一个令牌，用户可以在将来的请求中使用该令牌来标识自己。

在我们编写`authorizeWithGithub`解析器之前，我们需要构建两个函数来处理 GitHub API 请求:一个请求令牌，另一个请求帐户信息。`requestGithubToken`将索要令牌:

```
const requestGithubToken = credentials =>
  fetch("https://github.com/login/oauth/access_token", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
      Accept: "application/json"
    },
    body: JSON.stringify(credentials)
  })
    .then(res => res.json())
    .catch(error => {
      throw new Error(JSON.stringify(error));
    }); 
```

`requestGithubToken`函数返回一个获取承诺。在 POST 请求的主体中，`credentials`被发送到 GitHub API URL。`credentials`由`client_id`、`client_secret`和`code`组成。一旦完成，GitHub 响应就被解析为 JSON。我们现在可以使用这个函数通过`credentials`请求一个 GitHub 访问令牌。

一旦我们有了 GitHub 令牌，我们将需要从当前用户的帐户访问信息。具体来说，我们需要他们的 GitHub 登录名、姓名和个人资料图片。为了获得这些信息，我们需要向 GitHub API 发送另一个请求，以及我们从上一个请求中获得的访问权限`token`。

```
const requestGithubUserAccount = token =>
  fetch(`https://api.github.com/user?access_token=${token}`).then(
    res => res.json
  ); 
```

这个函数也返回一个获取承诺。在这个 GitHub API 路径上，只要我们有一个访问令牌，我们就可以访问当前用户的信息。

现在让我们将这两个请求合并成一个异步函数，我们可以用它来授权和返回来自 GitHub 的用户，`requestGithubUser` :

```
const requestGithubUser = async credentials => {
  const { access_token } = await requestGithubToken(credentials);
  const githubUser = await requestGithubUserAccount(access_token);
  return { ...githubUser, access_token };
}; 
```

在这里使用 async/await 可以更容易地处理多个异步请求。首先，我们请求访问令牌并等待响应。然后，使用`access_token`，我们请求 GitHub 用户帐户信息并等待响应。一旦我们有了数据，我们将把它们放在一个对象中。

我们有用户的详细信息。现在我们需要把它存放在某个地方。在生产应用程序中，这将存储在数据库中，并添加到我们的 GraphQL 服务器的上下文中，以便所有解析器都可以访问该数据。可以把上下文想象成一个存储库，可以存放数据、身份验证细节或解析器功能需要了解的任何信息。在这种情况下，我们将保持简短，并将所有内容添加到我们将在该文件顶部创建的名为`currentUser`的变量中。

```
let currentUser; 
```

然后我们可以用`authorizeWithGithub`函数中的用户详细信息覆盖它。

我们已经创建了支持解析器功能的助手函数。现在，让我们实际编写解析器，从 GitHub:
获得一个令牌和一个用户帐户

```
async authorizeWithGithub(parent, { code }) {
  // 1\. Obtain data from GitHub
    let githubUser = await requestGithubUser({
      client_id: <YOUR_CLIENT_ID_HERE>,
      client_secret: <YOUR_CLIENT_SECRET_HERE>,
      code
    })
  // 2\. Package the results in a single object, write the value to currentUser global variable
    currentUser = {
      name: githubUser.name,
      githubLogin: githubUser.login,
      githubToken: githubUser.access_token,
      avatar: githubUser.avatar_url
    }
  // 3\. Return user data and their token
    return { user: currentUser, token: access_token }
  } 
```

解析器可以是异步的。在将操作结果返回给客户机之前，我们可以等待网络响应。`authorizeWithGithub`解析器是异步的，因为我们必须等待来自 GitHub 的两个响应，然后才能得到我们需要返回的数据。

是时候测试这个授权过程了，为了测试，你需要一个代码。要获得代码，您需要运行`githubLoginUrl`查询。

将 url 粘贴到新浏览器窗口的地址栏中。您将被引导至 GitHub，在那里您将同意授权此应用程序。当您授权该应用程序时，GitHub 会用一个代码将您重定向回`http://localhost:3000`:

`http://locahost:3000?code=XYZ`

在这里，代码是 **XYZ** 。从浏览器 URL 复制代码并发送给`authorizeWithGithub`变异:

```
mutation  {  authorizeWithGithub(code:  "XYZ")  {  token  user  {  githubLogin  name  avatar  }  }  } 
```

这种变异将授权当前用户，并返回一个令牌以及关于该用户的信息。保存令牌。我们需要在将来的请求中在标题中发送它。

**注意**:当你看到错误“Bad Credentials”时，意味着发送给 GitHub API 的客户端 id、客户端密码或代码不正确。检查客户端 id 和客户端密码，但通常是代码导致了这个错误。GitHub 代码只在有限的时间段内有效，并且只能使用一次。如果在请求凭据后解析器中出现错误，请求中使用的代码将不再有效。通常，您可以通过向 GitHub 请求另一个代码来解决此错误。

### 认证用户

为了在将来的请求中识别您自己，您需要在发送每个请求时在`Authorization`头中发送您的令牌。该令牌将用于通过查找用户的数据库记录来识别用户。

GraphQL Playground 有一个位置，您可以在那里为每个请求添加头。在右下角，紧挨着“查询变量”有一个名为“HTTP 头”的标签。您可以使用此选项卡向您的请求添加 HTTP 头。只需将标题作为 JSON:
发送

```
{  "Authorization":  "Bearer <YOUR_TOKEN>"  } 
```

用从`authorizeWithGithub`突变返回的令牌替换`<YOUR_TOKEN>`。现在，您在每个 GraphQL 请求中都发送了标识的密钥。

### 第三步:创建一个`me`查询

为了使这个认证过程完整，我们需要再创建一个查询。考虑一下你必须登录的任何应用程序的用户界面。一旦您提供了有效的凭证，屏幕上通常会出现一个新元素来反映登录状态。换句话说，如果我登录 Twitter，我会在右上角看到我的脸。让我们创建一个查询，返回当前登录用户的详细信息。

首先，我们需要修改我们的`typeDefs` :

```
type  Query  {  me:  User  ...  } 
```

`me`查询返回一个可空的用户。如果没有找到当前的授权用户，则为空。让我们为 me 查询添加解析器:

```
const resolvers = {
  Query: {
    me: () => currentUser,
    ...
  }
} 
```

我们已经完成了基于令牌查找用户的繁重工作。此时，您只需返回`currentUser`对象。同样，如果没有用户，这将为空。

如果正确的令牌已经添加到 HTTP Authorization 头中，您可以使用`me`查询:
发送一个请求来获得关于您自己的详细信息

```
query  currentUser  {  me  {  githubLogin  name  avatar  }  } 
```

当您运行此查询时，您将被识别。一个好的测试是尝试在没有授权头或使用不正确的令牌的情况下运行这个查询。给定一个错误的令牌或丢失的头，您将看到`me`查询为空。

使用 GraphQL 处理用户身份验证和授权没有“唯一正确的方法”,但是使用 GitHub 这样的社会化 OAuth 解决方案是一种快速入门的方法。对于脸书、谷歌或 Auth0，传递令牌的过程可能略有不同，但主要思想是一致的。我们可以使用突变来认证和授权用户，然后使用保存的关于有效用户的数据，我们可以编写对该用户数据的查询。

在本系列的下一部分中，我们将研究如何使用 GraphQL 客户端，特别是 Apollo 客户端来设置授权。