# 如何使用 Express 设置 Google OAuth2 登录

> 原文：<https://dev.to/aidanlovelace/how-to-setup-google-oauth2-login-with-express-2d30>

我最近在做 SubScrubber，我必须允许用户登录 Google 并请求许可，以便访问他们的 YouTube 订阅信息。他们的服务器端 API 的 Google 文档不包括代码示例或节点示例。完全没有 JS。他们的 Github 上有一个例子，但是它使用的是普通节点。JS standard `http`库添加了如此多的样板代码，以至于很难辨别哪些部分是谷歌特有的。为了填补这个空白，下面是如何用 Express 设置 Google OAuth2 登录。请注意，本指南假设您知道如何设置节点。JS 项目和安装依赖项，并且已经在 Google 开发人员控制台中创建了项目。

如果你只是想看看代码，在 [Github](https://github.com/AidanLovelace/Express-Google-OAuth2-Tutorial) 上有一个示例项目。

1.  设置您的节点。JS 项目，并安装以下依赖项:

*   cookie 分析器
*   ejs
*   表达
*   谷歌授权图书馆
*   谷歌 API
*   jsonwebtoken

1.  在 Google Developer Console 的 Credentials 部分，创建一个 Web 应用程序类型的 OAuth 客户端 ID 凭据。

2.  用以下内容创建一个名为`config.js`的文件，

```
const port = 3002;
const baseURL = `http://localhost:${port}`;
module.exports = {
  // The secret for the encryption of the jsonwebtoken
  JWTsecret: 'mysecret',
  baseURL: baseURL,
  port: port,
  // The credentials and information for OAuth2
  oauth2Credentials: {
    client_id: "",
    project_id: "", // The name of your project
    auth_uri: "https://accounts.google.com/o/oauth2/auth",
    token_uri: "https://oauth2.googleapis.com/token",
    auth_provider_x509_cert_url: "https://www.googleapis.com/oauth2/v1/certs",
    client_secret: "",
    redirect_uris: [
      `${baseURL}/auth_callback`
    ],
    scopes: [
      'https://www.googleapis.com/auth/youtube.readonly'
    ]
  }
}; 
```

1.  用您的项目信息填充`client_id`、`project_id`和`client_secret`属性。

2.  创建一个`main.js`。我在下面包括了所有的 includes 和样板文件，因为这超出了本文的范围。

```
const express = require('express');
const google = require('googleapis').google;
const jwt = require('jsonwebtoken');
// Google's OAuth2 client
const OAuth2 = google.auth.OAuth2;
// Including our config file
const CONFIG = require('./config');
// Creating our express application
const app = express();
// Allowing ourselves to use cookies
const cookieParser = require('cookie-parser');
app.use(cookieParser());
// Setting up EJS Views
app.set('view engine', 'ejs');
app.set('views', __dirname);
// Listen on the port defined in the config file
app.listen(CONFIG.port, function () {
  console.log(`Listening on port ${CONFIG.port}`);
}); 
```

注意，我使用 [EJS](https://ejs.co) 作为模板，因为它非常接近 html。

1.  为`/`创建获取路线。我们将在这里放置登录 google 的链接。

```
app.get('/', function (req, res) {

}); 
```

6a。我们在第 6 行的`main.js`中包含的`OAuth2`类来自于`google-auth-library`模块。它只是我们的 OAuth2 客户端的一个对象。在这条路线中，我们希望创建 OAuth2 客户机的一个实例，这样我们就可以用它来验证我们对 Google API 的请求。

```
// Create an OAuth2 client object from the credentials in our config file
const oauth2Client = new OAuth2(CONFIG.oauth2Credentials.client_id, CONFIG.oauth2Credentials.client_secret, CONFIG.oauth2Credentials.redirect_uris[0]); 
```

6b。现在，我们想获得当用户点击登录按钮时我们将发送给他们的链接。为此，我们需要在 OAuth2 客户机上调用`generateAuthUrl`方法，向它传递访问类型和我们需要的访问范围。访问范围告诉谷歌它到底需要征求用户的同意。例如，如果我们想要访问用户的 YouTube 数据，我们需要请求范围`https://www.googleapis.com/auth/youtube.readonly`，这样 Google 就会询问他们是否想与我们分享他们的 YouTube 数据。

```
// Obtain the google login link to which we'll send our users to give us access
const loginLink = oauth2Client.generateAuthUrl({
  access_type: 'offline', // Indicates that we need to be able to access data continously without the user constantly giving us consent
  scope: CONFIG.oauth2Credentials.scopes // Using the access scopes from our config file
}); 
```

6c。最后，我们需要使用登录链接呈现我们的索引模板(我们将在 6d 中创建它)。

```
return res.render("index", { loginLink: loginLink }); 
```

此时，这应该就是你的`/`路线:

```
app.get('/', function (req, res) {
  // Create an OAuth2 client object from the credentials in our config file
  const oauth2Client = new OAuth2(CONFIG.oauth2Credentials.client_id, CONFIG.oauth2Credentials.client_secret, CONFIG.oauth2Credentials.redirect_uris[0]);
  // Obtain the google login link to which we'll send our users to give us access
  const loginLink = oauth2Client.generateAuthUrl({
    access_type: 'offline', // Indicates that we need to be able to access data continously without the user constantly giving us consent
    scope: CONFIG.oauth2Credentials.scopes // Using the access scopes from our config file
  });
  return res.render("index", { loginLink: loginLink });
}); 
```

6d。创建一个名为`index.ejs`的基本 html(ish)文件，其中包含一个指向我们传递给该文件的页面的登录链接。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Express Google OAuth2 Tutorial by Aidan Lovelace
</head>
<body>
  <a href="<%= loginLink %>">Login</a>
</body>
</html> 
```

1.  此时，您可以运行`node main.js`并访问 [http://localhost:3002/](http://localhost:3002/) ，看到一个链接到 Google 错误的小登录按钮。这个错误是由于我们没有告诉 Google 我们希望它在`http://localhost:3002/auth_callback`将用户重定向回我们，所以现在我们需要这样做。在 Google Developer Console 中，点击您为此项目创建的 Web 应用程序凭证，并将`http://localhost:3002/auth_callback`添加到授权重定向 URL 列表中。现在，您应该能够使用您的 Google 帐户登录，并被重定向到 404 错误。

2.  这个 404 错误是由于我们没有实现`auth_callback`路由，所以我们现在应该做了。当谷歌重定向用户时，它要么用一个我们可以用来获得永久凭证的代码进行重定向，要么在用户决定不给我们访问权时出错。该数据将包含在 GET 参数中。这里我们需要一个 OAuth2 客户机，所以首先要添加它。我们还需要检查错误参数。如果有的话，让我们把用户重定向到主页。否则，我们需要获得永久的用户凭证，并将它们存储在 cookie 中，以便以后使用。下面的代码将用户重定向到`/get_some_data`，这是一个我们还没有创建的页面，但是会显示一些关于用户的数据。

```
app.get('/auth_callback', function (req, res) {
  // Create an OAuth2 client object from the credentials in our config file
  const oauth2Client = new OAuth2(CONFIG.oauth2Credentials.client_id, CONFIG.oauth2Credentials.client_secret, CONFIG.oauth2Credentials.redirect_uris[0]);
  if (req.query.error) {
    // The user did not give us permission.
    return res.redirect('/');
  } else {
    oauth2Client.getToken(req.query.code, function(err, token) {
      if (err)
        return res.redirect('/');

      // Store the credentials given by google into a jsonwebtoken in a cookie called 'jwt'
      res.cookie('jwt', jwt.sign(token, CONFIG.JWTsecret));
      return res.redirect('/get_some_data');
    });
  }
}); 
```

1.  让我们创建`/get_some_data`页面。在我的例子中，它将显示用户订阅的 5 个频道。它需要创建一个 OAuth2 客户端，并向其中添加用户凭证，以便访问任何内容。然后，它将获取订阅并将其发送到模板。

```
app.get('/get_some_data', function (req, res) {
  if (!req.cookies.jwt) {
    // We haven't logged in
    return res.redirect('/');
  }
  // Create an OAuth2 client object from the credentials in our config file
  const oauth2Client = new OAuth2(CONFIG.oauth2Credentials.client_id, CONFIG.oauth2Credentials.client_secret, CONFIG.oauth2Credentials.redirect_uris[0]);
  // Add this specific user's credentials to our OAuth2 client
  oauth2Client.credentials = jwt.verify(req.cookies.jwt, CONFIG.JWTsecret);
  // Get the youtube service
  const service = google.youtube('v3');
  // Get five of the user's subscriptions (the channels they're subscribed to)
  service.subscriptions.list({
    auth: oauth2Client,
    mine: true,
    part: 'snippet,contentDetails',
    maxResults: 5
  }).then(response => {
    // Render the data view, passing the subscriptions to it
    return res.render('data', { subscriptions: response.data.items });
  });
}); 
```

最后，我们需要创建`data.ejs`模板来显示数据。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  Express Google OAuth2 Tutorial by Aidan Lovelace
</head>
<body>
  <ul>
    <% subscriptions.forEach(function (subscription) { %>
      <li><%= subscription.snippet.title %></li>
    <% }) %>
  </ul>
</body>
</html> 
```

感谢阅读！如果我犯了什么错误，请不要犹豫，在评论中告诉我。