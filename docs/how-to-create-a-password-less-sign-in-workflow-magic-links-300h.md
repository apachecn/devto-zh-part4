# 如何创建无密码登录工作流(神奇链接)

> 原文：<https://dev.to/cjwd/how-to-create-a-password-less-sign-in-workflow-magic-links-300h>

如果你曾经使用过 Slack 这样的应用程序，你会发现一种很棒的方式，只使用你的电子邮件地址登录，无密码或只使用电子邮件使用“魔法链接”登录。这尤其有用，因为人们通常很难记住密码，或者通过手机访问应用程序，并且很难输入复杂的密码。它的工作原理是给用户发送一个“神奇链接”,然后用户点击这个链接，就会自动通过验证并登录。

## 截屏

[https://www.youtube.com/embed/oTPsogsjahM](https://www.youtube.com/embed/oTPsogsjahM)

* * *

在这篇文章中，我将向你展示如何为你的应用程序创建一个简单的无密码登录。我将使用 ExpressJs 来构建它，但该逻辑可以应用于任何项目。

无密码登录的基本步骤如下:

1.  生成令牌并将其与用户相关联
2.  生成一个 url，它是令牌和查询参数的组合，例如电子邮件或 id
3.  通过电子邮件将 url 发送给用户
4.  当用户使用 url 时，验证令牌并对用户进行身份验证

## 建立数据库，搭建 app

在开始之前，您需要一个简单的数据库，其中至少有一个 users 表。该表至少应包含以下字段:

*   身份证明（identification）
*   名字
*   电子邮件
*   代币
*   令牌 _ 日期

我使用 [Airtable](https://airtable.com/) 作为数据库，因为它提供了一种快速简单的方法来获得一个简单的数据库，他们有一个很好的 [API](https://airtable.com/api) 来获得数据。

## 只是想要代码？

如果你想继续下去，你可以得到基本代码，我的 [Express Airtable Starter](https://github.com/cjwd/airtable-express-starter) 或[完整工作演示](https://github.com/cjwd/passwordless-signin)。

## 生成令牌并将其与用户关联

在控制器目录中，创建一个新的控制器来处理用户逻辑。遵循合理的命名约定示例 userController.js。

在这个文件中，您将需要依赖项，当然这些应该使用您首选的包管理器(npm 或 yarn)预先安装。您需要的包如下:

```
// Mandatory
const querystring = require("querystring");
const nodemailer = require("nodemailer");
const diffInMinutes = require("date-fns/difference_in_minutes");

//Only if you are using Airtable as I am
const Airtable = require("airtable"); 
```

### 可选:Airtable 配置

如果您跟我一起使用 Airtable 作为数据库，那么您需要在环境变量文件(variables.env)中存储 API 键和基本 ID，并配置您的应用程序以使用 Airtable API。

```
// userController.js
// After dependency declarations
const base = new Airtable({
  apiKey: process.env.AIRTABLE_API_KEY
}).base(process.env.AIRTABLE_BASE_ID);

const TABLE = base("users");
const VIEW = "Grid view"; 
```

我还抽象了一个助手函数来从 Airtable 获取记录，而不管是表还是视图。我将它存储在一个 controllers/dataController.js 文件中。请注意，您传入了两个参数，即表的名称和视图的名称。

```
// dataController.js

exports.getAirtableRecords = (table, view) => {
  let records = [];
  return new Promise((resolve, reject) => {
    // Cache results if called already
    if (records.length > 0) {
      resolve(records);
    }

    const processPage = (partialRecords, fetchNextPage) => {
      records = [...records, ...partialRecords];
      fetchNextPage();
    };

    const processRecords = err => {
      if (err) {
        reject(err);
        return;
      }

      resolve(records);
    };

    table
      .select({
        view
      })
      .eachPage(processPage, processRecords);
  });
}; 
```

为了使用我们的上述助手功能，我们需要在这个文件中要求在我们的用户控制器文件。

```
// userController.js
const data = require("./dataController.js"); 
```

然后我们将创建两个助手函数。一个生成令牌，另一个生成神奇链接。

```
// userController.js
const generateToken = (id, email) => {
  let source = `${id}${email}`
  let token = ''
  for (let i = 0; i &lt; source.length; i++) {
    token += source.charAt(Math.floor(Math.random() * source.length));
  }

  return token;
}

const generateLoginUrl = (token, email) => {
  let url = '';
  url = `login/magiclink/${token}?${querystring.stringify({email: email})}`
  return url
} 
```

### 用户注册

让我们为用户注册创建一个路线和视图。如果您一直在关注我的应用程序，所有路线都位于 routes/index.js 中。首先，让我们创建一条显示注册页面的路线。为了简单起见，我使用主页或 index.html 作为注册页面，所以路径应该是

```
// index.js
router.get("/", userController.getSignup); 
```

如果你不熟悉 routes，它只是告诉应用程序当一个请求(例如:用户访问一个页面或提交一个表单)被发送到一个特定的 url 时执行什么功能。所以在这里，如果我们收到一个对主页('/')的 get 请求，应用程序应该执行**用户控制器**中的 **getSignup** 函数。

现在，在 userController.js 文件中，我们创建了 **getSignup** 函数。

```
// userController.js
exports.getSignup = (req, res) => {
  res.render('index'});
}); 
```

上面只是加载了 index (homepage)模板，在我的例子中，我使用的是 pug，因此 index.pug 文件将被呈现。

```
extends ../layouts/base

block content
  h1.title Sign Up

  if(message)
    .notification= message

  form.form.form--signup(action="/signup", method="POST")
    .field
      label.label(for="name") Full Name
      .control
        input#name.input(type="text", name="name")

    .field
      label.label(for="email") Email Address
      .control
        input#email.input(type="email", name="email")

    .field
      .control
        input.button.is-primary(type="submit", value="Sign Up") 
```

请注意表单操作和方法。当用户提交时，我们向 url '/signup '发送 POST 请求。让我们创建注册的路线和功能。

注册流程的逻辑是

1.  创建新的用户记录
2.  如果成功，我们将使用用户 id 和电子邮件地址来生成令牌并更新用户记录
3.  然后，我们向用户发送一条消息到 4。查看他们的电子邮件中的链接
4.  生成神奇的链接
5.  通过电子邮件向他们发送神奇链接

```
// index.js
router.post("/signup", userController.signUp, userController.sendEmail);

// userController.js
exports.signUp = (req, res, next) => {
  // 1\. Create a new user record
  TABLE.create(
    {
      name: req.body.name,
      email: req.body.email
    },
    (err, record) => {
      if (err) {
        console.error(err);
        return;
      }

      // 2a On success, generate a token
      const token = generateToken(record.getId(), req.body.email);

      // 2b Update the user record to save the token and token creation date
      TABLE.update(
        record.getId(),
        {
          token,
          token_date: new Date().toISOString()
        },
        (err, record) => {
          if (err) {
            console.error(err);
            return;
          }

          // 3\. On success, flash a message
          res.render("index", {
            message: "Please check your email for you magic sign in link"
          });

          // 4\. Generate Magic Link
          req.body.url = generateLoginUrl(token, record.get("email"));

          // 5\. Send Email
          next(); // This call the next function in chain declared in route, in this case sendEmail()
        }
      );
    }
  );
};

exports.sendEmail = (req, res) => {
  const to = req.body.email;
  const subject = "Magic sign in link for My Sweet App";
  const body = `Hello,
  Here is your magic link for quickly signing into My Sweet App.
  &lt;a href="http://localhost:7777/${
    req.body.url
  }">Sign in to My Sweet App&lt;/a>
  You can also copy and paste this link in your brower url bar.
  &lt;a href="http://localhost:7777/${req.body.url}">http://localhost:7777/${
    req.body.url
  }&lt;/a>`;

  const transporter = nodemailer.createTransport({
    host: process.env.SMTP_HOST,
    port: process.env.SMTP_PORT,
    // secure: true,
    auth: {
      user: process.env.SMTP_USERNAME,
      pass: process.env.SMTP_PASSWORD
    }
  });

  const mailOptions = {
    from: process.env.FROM_EMAIL,
    to,
    subject,
    html: body
  };

  transporter.sendMail(mailOptions, (err, info) => {
    if (err) {
      console.log(err);
    } else {
      // email sent
      console.log(info);
      res.send(info.messageId);
    }
  });
}; 
```

请注意，我使用了 [nodemailer](https://nodemailer.com/about/) 来处理发送电子邮件。如果你正在使用我的 starter 文件，它已经包含在 package.json 中了。我还使用 [Mailtrap](https://mailtrap.io/) 作为 SMTP 服务器来测试电子邮件是否正在发送。去报名吧，他们有一个慷慨的免费计划。

## 用户登录

现在我们已经处理了用户登录，让我们为使用神奇链接登录创建路由、视图和逻辑。

让用户登录的步骤如下

1.  检查用户是否存在
2.  检查令牌是否有效，即它属于用户并且尚未过期
3.  让用户登录并进入他们的个人资料

为了完成上述任务，我们需要 3 条路线:

*   路由显示登录表单，获取请求至/登录
*   路由创建一个神奇的链接，并通过电子邮件发送给用户，如果他们的令牌过期，发布请求到/登录
*   使用 magic link、GET request to /login/magic/和一个动态参数:token 来验证和认证用户

```
// index.js
router.get('/login', userController.getLogin);
router.post('/login',
  userController.createMagicLink,
  userController.sendEmail);
router.get('/login/magiclink/:token', userController.authenticate);

// user.Controller
exports.getLogin = (req, res) => {
  res.render('login', { title: 'Sign in to your profile' });
};

exports.createMagicLink = async (req, res, next) => {
  // Check if the user exists
  const user = await getUserByEmail(req.body.email);

  // If the user does not exist, let them know
  if (Object.entries(user).length === 0) {
    res.render('index', { message: 'User does not exist.' });
  }

  // Generate a token
  const token = generateToken(user.id, user.email);

  // Update token and token date
  TABLE.update(user.id, {
    token,
    token_date: (new Date()).toISOString(),
  }, (err, record) => {
    if (err) { console.error(err); return; }

    res.render('index', { message: 'Please check your email for your magic sign in link', user: record });

    // Generate magic link
    const url = generateLoginUrl(token, user.email);

    // Send the magic link
    req.body.url = url;
    next();
  });
};

exports.authenticate = async (req, res) => {
  const token = req.params.token;
  const email = req.query.email;
  const user = await getUserByEmail(email);

  if (Object.entries(user).length === 0) {
    res.render('index', { message: 'User does not exist.' });
  }

  if (user.email === email &amp;&amp; user.token === token) {
    // Check if token is expired
    const isExpired = diffInMinutes(new Date(), user.date) > 5;

    if (isExpired) {
      res.render('login', { message: 'Magic link has expired. Enter your email to receive a new one.' });
    } else {
      res.render('profile', { user });
    }
  }
}; 
```

请注意，我们必须检查用户是否多次存在，并返回他们的数据，以便在其他函数中使用，并发送到视图。我将其抽象为一个助手函数 **getUserbyEmail** 。我们将把这个函数和我们的其他助手函数一起添加到用户控制器文件的顶部。

```
// userController.js
const getUserByEmail = async email => {
  let record = {};
  const users = await data.getAirtableRecords(TABLE, VIEW);
  users.filter(user => {
    if (user.get("email") === email) {
      record = {
        id: user.getId(),
        name: user.get("name"),
        email: user.get("email"),
        token: user.get("token"),
        date: user.get("token_date")
      };
      return true;
    }
    return false;
  });

  return record;
}; 
```

这个函数返回一个包含用户详细信息的对象。我们正在使用 dataController.js 中的另一个助手函数，它返回一个承诺。解决后，我们将从 Airtable 获得所有记录，然后可以过滤匹配电子邮件地址的结果。如果有很多记录，这可能需要一段时间，我们的应用程序将被卡住，因此使用承诺。

好了，这是一个为你的应用程序提供无密码登录的简单方法。