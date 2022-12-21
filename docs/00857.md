# 使用 Firebase auth 保护您的 express/Node.js API

> 原文：<https://dev.to/emeka/securing-your-express-node-js-api-with-firebase-auth-4b5f>

许多应用程序，无论是移动应用程序还是 web 应用程序，都有某种形式的身份验证。如果你使用过各种应用程序，处理认证可能会成为一项重复的任务，可能会变得很无聊，这就是为什么我喜欢利用 auth0 或 firebase 等外部服务来使认证变得轻而易举。这些服务还可以处理社交认证，这可以节省我们很多代码。我们要担心的只是整合。

在本文中，我将介绍如何使用 firebase 来保护我们的 API，以便只有经过授权的用户才能访问我们的资源。保护 API 的一种常见方法是使用 JWT 令牌，该令牌是在用户提供有效的身份验证凭证后生成的，并且该令牌在每次请求时都会得到验证。这与我们将要用 firebase 做的事情非常相似。我们将利用 firebase 来处理这个令牌的生成和验证。

请注意，本文并不打算教您如何创建/启动 express 服务器。如果您不熟悉 Node.js 或 express 的使用，我建议您在阅读本文之前检查一下。

该是我们钻研代码的时候了。

访问你的 [firebase 控制台](https://console.firebase.google.com)并创建一个新项目，如果你还没有这样做的话。

## 服务器端

对于服务器端，我们将使用 firebase admin SDK，因为它更适合我们要完成的任务。

使用此命令在您的服务器上安装 admin SDK:

```
npm i firebase-admin 
```

为了验证您是从一个可信的环境中调用 API，google 建议您为您的项目生成并下载一个服务帐户密钥，并将其添加到您环境中的一个路径中。因此，到您的控制台，生成一个服务帐户密钥，下载它(最好是 JSON ),并将其位置添加到您将运行服务器的环境中的一个路径(GOOGLE_APPLICATION_CREDENTIALS)中。

```
exports GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account-file.json 
```

查看[此处](https://firebase.google.com/docs/admin/setup#initialize_the_sdk)了解更多相关信息。

现在，我们可以在项目中创建一个服务，用我们的凭证初始化我们的 SDK 并导出它。

```
import * as admin from 'firebase-admin';

admin.initializeApp(
  credential: admin.credential.applicationDefault(),
  databaseURL: 'https://<DATABASE_NAME>.firebaseio.com'
);

export default admin 
```

接下来，我们将编写处理新用户创建的逻辑。我们可以将这个逻辑放在我们的 auth controller 中，或者您认为合适的任何地方。

```
import admin from './firebase-service';

export const createUser = async (req, res) => {
const {
      email,
      phoneNumber,
      password,
      firstName,
      lastName,
      photoUrl
    } = req.body;

    const user = await admin.auth().createUser({
      email,
      phoneNumber,
      password,
      displayName: `${firstName}  ${lastName}`,
      photoURL: photoUrl
    });

    return res.send(user);
} 
```

既然我们创建用户的逻辑已经到位。我们需要确保传入的请求来自经过身份验证的用户。我们可以通过创建中间件来保护我们想要保密的路由来实现这一点。

我们将创建一个认证中间件来确保在请求头中有一个有效的 firebase 令牌。

```
import admin from './firebase-service';

const getAuthToken = (req, res, next) => {
  if (
    req.headers.authorization &&
    req.headers.authorization.split('  ')[0] === 'Bearer'
  ) {
    req.authToken = req.headers.authorization.split('  ')[1];
  } else {
    req.authToken = null;
  }
  next();
};

export const checkIfAuthenticated = (req, res, next) => {
 getAuthToken(req, res, async () => {
    try {
      const { authToken } = req;
      const userInfo = await admin
        .auth()
        .verifyIdToken(authToken);
      req.authId = userInfo.uid;
      return next();
    } catch (e) {
      return res
        .status(401)
        .send({ error: 'You are not authorized to make this request' });
    }
  });
}; 
```

有了这个中间件，用户每次试图未经身份验证就访问私有资源时，都会得到一个“未授权”错误。

现在我们已经创建了我们的中间件，让我们用它来保护我们的私有路由。

```
import {Router} from 'express';
import {createUser} from './controllers/authcontroller';
import {checkIfAuthenticated} from './middlewares/auth-middleware';
import {articles} from from './data';

const router = Router();

router.post('/auth/signup', createUser);

router.get('/articles', checkIfAuthenticated, async (_, res) => {
  return res.send(articles);
});  

export default router; 
```

在上面的代码中，我们定义了两条路线。一个用于创建我们的用户，另一个用于仅在用户通过身份验证时获取文章。现在让我们转向客户端，看看如何使用这个 API。

## 客户端

我们可以使用任何用于 web 或移动应用程序的 javascript 客户端库或框架来使用我们的 API，所以我不会指定任何内容，而是将重点放在 firebase javascript SDK 上。尽管特定于各种 javascript 库/框架的 SDK 可能存在一些差异，但 API 仍然与官方的 web SDK 非常相似。

所以，我们在客户端安装 firebase。

```
npm i firebase 
```

注意:您的平台可能需要不同的 SDK 和安装方法，例如 angular-fire 和 react-native-firebase。

为了保持整洁，我们还可以在客户机上创建一个服务，用我们的配置初始化 firebase。

```
import * as firebase from 'firebase/app';
import 'firebase/auth';

const config = {
  apiKey: "api-key",
  authDomain: "project-id.firebaseapp.com",
  databaseURL: "https://project-id.firebaseio.com",
  projectId: "project-id",
  storageBucket: "project-id.appspot.com",
  messagingSenderId: "sender-id",
  appID: "app-id",
}

firebase.initializeApp(config);

export const auth = firebase.auth

export default firebase; 
```

您的凭证可在您的 [firebase 控制台](https://console.firebase.google.com)上获得。如果你没有在网上使用 javascript，你应该看看如何在你的特定平台上初始化 firebase。

我们将创建一个 auth 服务，用于调用注册端点和用户登录。

```
import axios from 'axios';
import {auth} from './firebase-service';

export const createUserAccount = (data) => {
  return axios.post('https://your-api-url/auth/signup', data)
    .then(res => res.data)
}

export const loginUser = (email, password) => {
  return auth().signInWithEmailAndPassword(email, password);
} 
```

我们已经定义了创建用户并让他们登录我们的应用程序的逻辑。这就是我们如何用 firebase 检查用户是否已经登录。

```
 firebase.auth().onAuthStateChanged(user => {
   if (user) {
     return user;
   }
}); 
```

现在，我们已经注册并登录了，让我们继续在客户端生成一个令牌，以便在服务器上进行身份验证。这可以通过一行代码轻松完成。耶！你没听错，一句台词。

```
 const token = await firebase.auth.currentUser.getIdToken(); 
```

您可以在一个异步函数中使用它，或者解析 promise 来获得令牌值。我们将向我们的 API 发出一个请求，将令牌附加到请求头，以访问文章资源。

```
import {auth} from './firebase-service';

export const getArticles = async () => {
const token = await auth.currentUser.getIdToken();

return axios.get('https://your-api-url/articles', {headers:  
  { authorization: `Bearer ${token}` }})
  .then(res => res.data);
} 
```

我们只是将 firebase 令牌传递给了授权头。它将在服务器端被提取，并用于验证我们的用户。这些都将由我们之前创建的中间件来处理

## 用户角色

用户认证的一个非常重要的部分是角色管理。如果我们希望拥有不同级别的授权，并将对特定资源的访问权限限制在具有特定角色的用户，该怎么办？这也很容易用 firebase 身份验证来实现。

我们将在我们的服务器上管理角色，这是我们可以做的。

```
import admin from './firebase-service';

export const makeUserAdmin = async (req, res) => {
  const {userId} = req.body; // userId is the firebase uid for the user

  await admin.auth().setCustomUserClaims(userId, {admin: true});

  return res.send({message: 'Success'})
} 
```

既然我们可以为用户分配角色，那么我们如何检查用户是否有特定的角色呢？很简单，当我们在中间件中验证用户的令牌时，我们可以很容易地访问返回数据中的信息。我们将添加一个中间件来检查我们的用户是否有管理员角色。

```
import admin from './firebase-service';

const getAuthToken = (req, res, next) => {
  if (
    req.headers.authorization &&
    req.headers.authorization.split('  ')[0] === 'Bearer'
  ) {
    req.authToken = req.headers.authorization.split('  ')[1];
  } else {
    req.authToken = null;
  }
  next();
};

export const checkIfAdmin = (req, res, next) => {
 getAuthToken(req, res, async () => {
    try {
      const { authToken } = req;
      const userInfo = await admin
        .auth()
        .verifyIdToken(authToken);

      if (userInfo.admin === true) {
        req.authId = userInfo.uid;
        return next();
      }

      throw new Error('unauthorized')
    } catch (e) {
      return res
        .status(401)
        .send({ error: 'You are not authorized to make this request' });
    }
  });
}; 
```

我们现在可以用这个中间件保护我们的管理资源。以下是我们更新的路线

```
import {Router} from 'express';
import {createUser} from './controllers/authcontroller';
import {checkIfAuthenticated, checkifAdmin} from './middlewares/auth-middleware';
import {articles} from from './data';
import {records} from './data/admin';

const router = Router();

router.post('/auth/signup', createUser);

router.get('/stories', checkIfAuthenticated, async (_, res) => {
  return res.send(articles);
});  

router.get('/admin/records', checkIfAdmin, async (_, res) => {
  return res.send(records);
});

export default router; 
```

如果任何没有分配管理员角色的令牌试图访问我们的管理员资源，将会出现“未授权”错误。

还有更多的内容可以涵盖，但这是我们将在本文中涵盖的所有内容。希望这足以推动您在服务器上开始使用 firebase auth。您可以通过探索 [firebase 文档](https://firebase.google.com/docs/auth)了解更多可能性。