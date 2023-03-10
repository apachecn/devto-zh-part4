# 使用 Airtable 第 3 部分创建用户数据库

> 原文：<https://dev.to/cjwd/creating-a-user-database-with-airtable-part-3-4fhc>

[第 1 部分](https://chinarajames.com/user-database-with-airtable-part-1/)我们学习了如何使用散列密码向 Airtable 数据库添加用户。
[第 2 部分](https://chinarajames.com/user-database-with-airtable-part-2/)我们学习了如何用会话持久化用户数据。

在第 3 部分，也就是本系列的最后一个教程中，我们将创建一个密码重置流程，所以让我们开始吧。

## 只是想要代码？

在 [Github](https://github.com/cjwd/user-database-airtable-tut) 上获得完整的工作演示。如果您想继续，下载 starter files 文件夹，并将其重命名为您想要的名称，然后运行 yarn 来安装依赖项。别忘了先看看[第一部](https://chinarajames.com/user-database-with-airtable-part-1/)和[第二部](https://chinarajames.com/user-database-with-airtable-part-2/)。

## 流程

密码重置流程有以下一般步骤:

1.  [用户请求重设密码](#step-1)
2.  [向用户发送密码重置电子邮件](#step-2)
3.  [用户提交新密码](#step-3)
4.  [用新密码](#step-4)更新用户记录
5.  [发送电子邮件确认密码更改](#step-5)

## 步骤 1 用户请求密码重置

创建一个视图 *forget.pug* ，其中包含一个用户请求密码重置的表单。

```
<!-- templates/views/forget.pug -->

extends ../layouts/base

block content
  h1.title Password Reset

  if (message)
    .notification.is-success
      button.delete
      =message

  form(action="/user/forgot" method="POST")
    .field
      label.label(for="username") Email or Username
      .control.has-icons-left.has-icons-right
        input.input(type='text' name="username" id="username" placeholder='Email input' value='hello@')
        span.icon.is-small.is-left
          i.fas.fa-user

    .field
      .control
        button.button.is-link Reset Password 
```

我们还将在登录表单中添加忘记密码链接。

```
<!-- templates/views/index.pug -->
small
    a(href="/forgot-password") Lost your password? 
```

现在让我们在 index.js 中创建一个路由，以显示忘记密码视图和 appController.js 文件中的相关中间件。这将是一个 GET 请求。

```
// index.js
router.get("/forgot-password", appController.getForgetPassword);

// appController.js
exports.getForgetPassword = (req, res) => {
  res.render("forgot");
}; 
```

## 第二步向用户发送密码重置邮件

如果用户存在，我们将向他们发送一封包含密码重置链接的密码重置电子邮件。该链接将包含一个令牌参数以及作为查询参数的电子邮件地址。
一个例子

```
http://localhost:7777/login/resetlink/liEvWzccnrWnnhEzaNca@iafiazpXozJZXJa0zn?email=chinarakinda%40gmail.com 
```

格式为

```
http://localhost:7777/[token]?user=[username] 
```

令牌将用于确认用户确实请求了重置，并将与数据库中存储的令牌进行比较。因此，我们需要在 Airtable 中添加一个令牌字段。这将是一个普通的文本字段。

### 创建密码重置链接

如上所述，我们将使用一个令牌来确认重置，这样人们就可以通过电子邮件输入任何字符串，并能够重置用户的密码。这个令牌将是用户的记录 ID 和他们的电子邮件地址的组合。

让我们创建两个助手函数来处理密码重置 URL 字符串的生成。第一个函数将负责根据用户的记录 ID 和电子邮件生成令牌，第二个函数将使用该令牌并生成 URL。

```
// userController.js

// Built in node module provides utilities for parsing and formatting URL query strings
const querystring = require("querystring");

// The token will be using the user's ID and email address to generate a random string
const generateToken = (id, email) => {
  const source = `${id}${email}`;
  let token = "";
  for (let i = 0; i < source.length; i++) {
    token += source.charAt(Math.floor(Math.random() * source.length));
  }

  return token;
};

const generateResetUrl = (token, email) => {
  let url = "";
  url = `login/resetlink/${token}?${querystring.stringify({ email })}`;
  return url;
}; 
```

当用户请求重置密码时，我们希望将令牌添加到用户记录中。然后，我们可以在他们输入新密码时进行确认。

```
// userController.js
exports.addToken = async (req, res, next) => {
  const { username } = req.body;
  // Check that the user exists. We wrote this helper function already in Part 1 but we need to refactor as it requires two parameters and we are only including one here
  const userExists = await findUser(username);

  if (userExists) {
    res.render("login", {
      message: "Username or Email already exists!"
    });
    return;
  }

  const options = {
    filterByFormula: `OR(email = '${username}', username = '${username}')`
  };

  // Get the user
  const users = await data.getAirtableRecords(table, options);

  const user = users.map(record => ({
    id: record.getId(),
    email: record.get("email")
  }));

  const token = generateToken(user[0].id, user[0].email);

  table.update(
    user[0].id,
    {
      token
    },
    (err, record) => {
      if (err) {
        console.error(err);
      }

      req.body.url = generateResetUrl(token, user[0].email);
      req.body.to = user[0].email;
      next();
    }
  );
}; 
```

### 重构 findUser

您会注意到在检查用户是否存在的代码前有一个注释。

```
// addToken function

// Check that the user exists. We wrote this helper function already in Part 1 but we need to refactor as it requires two parameters and we are only including one here
const userExists = await findUser(username); 
```

我们的功能需要用户名和电子邮件，但任何人或两者都可以提供。让我们考虑一下，这样才有可能。我们将为函数参数设置默认值，并有条件地检查它们是否存在。

```
const findUser = async (email = undefined, username = undefined) => {
  let recordExists = false;
  let options = {};

  if (email && username) {
    options = {
      filterByFormula: `OR(email = '${email}', username = '${username}')`
    };
  } else {
    options = {
      filterByFormula: `OR(email = '${email}', username = '${email}')`
    };
  }

  const users = await data.getAirtableRecords(table, options);

  users.filter(user => {
    if (user.get("email") === email || user.get("username") === username) {
      return (recordExists = true);
    }
    if (user.get("email") === email || user.get("username") === email) {
      return (recordExists = true);
    } else {
      return false;
    }
  });

  return recordExists;
}; 
```

现在，我们可以编写代码来发送密码重置电子邮件。

### 发送密码重置邮件

我们将使用 nodemailer 包来发送电子邮件，并使用 [Mailtrap](https://mailtrap.io/) web 服务来模拟我们的事务性电子邮件提供商。

让我们从在我们的应用程序中安装和要求 nodemailer 开始。

```
yarn add nodemailer 
```

```
// userController.js
const nodemailer = require("nodemailer"); 
```

现在让我们编写发送密码重置链接电子邮件的函数。

```
// userController.js

exports.sendEmail = async (req, res) => {
  const subject = "Password Reset link for My Sweet App";
  const { url, to } = req.body;
  const body = `Hello,
  You requested to have your password reset. Ignore if this is a mistake or you did not make this request. Otherwise, click the link below to reset your password.
  <a href="http://localhost:7777/${url}">Reset My Password</a>
  You can also copy and paste this link in your browser URL bar.
  <a href="http://localhost:7777/${url}">http://localhost:7777/${url}</a>`;

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
      res.render("forgot", {
        message: "Please check your email for your password reset link"
      });
    }
  });
}; 
```

您会注意到在 createTransport 函数中有一个对象，它带有几个以 process.env 为前缀的变量。我们不希望这些代码出现在代码库中，所以我们将它们存储在我们的 *variables.env* 文件中。

如果您一直在跟进，您应该已经将示例变量文件 *example.variables.env* 重命名为 *variables.env* 。

在 [Mailtrap](https://mailtrap.io/) 创建一个免费帐户，获取您的详细信息并输入保存到您的环境变量文件中。

## 第三步用户提交新密码

当用户单击电子邮件中的密码重置链接时，将显示一个表单供他们输入新密码。让我们创建这个视图，我们将把它叫做 *reset.pug* 。

```
<!-- templates/views/reset.pug -->

extends ../layouts/base
include ../mixins/_resetForm

block content
  h1.title Password Reset

  if (message)
    .notification.is-success
      button.delete
      =message

  form(action="/user/reset" method="POST")
    .field
      label.label(for="password") New Password
      .control.has-icons-left
        input.input(type='password' name="password" id="password" placeholder='Password')
        span.icon.is-small.is-left
          i.fas.fa-lock

      input(type="hidden" name="email" value=`${email}`)
      input(type="hidden" name="token" value=`${token}`)

    .field
      .control
        button.button.is-link Update Password 
```

让我们创建一个路由来显示上面的视图以及相关的中间件来处理请求。请注意两个隐藏字段 *email 和令牌*，它们将从密码重置 URL 获得。注意路由中的通配符参数*:令牌*，它将包含令牌和电子邮件的查询字符串。

```
// index.js
router.get("/login/resetlink/:token", appController.getResetPassword);

// appController.js
exports.getResetPassword = (req, res) => {
  res.render("reset", {
    token: req.params.token,
    email: req.query.email
  });
}; 
```

## 第 4 步用新密码更新用户记录

该表单将向 *'/user/reset'* 发送一个 POST 请求，因此让我们在 userController.js.
中设置该路由和相关的中间件来处理该请求

```
// index.js
router.post(
  "/user/reset",
  userController.resetPassword,
  userController.storePassword
  userController.sendConfirmResetPasswordEmail
);

// userController.js
exports.confirmToken = async (req, res, next) => {
  // Get Form Variables
  const { email, token } = req.body;

  const options = {
    filterByFormula: `OR(email = '${email}', token = '${token}')`
  };

  // Get the user
  const users = await data.getAirtableRecords(table, options);

  const user = users.map(record => ({
    id: record.getId()
  }));

  // hash and the update the user's password
  req.body.id = user[0].id;
  next();
}; 
```

第一个中间件 *confirmToken* 将通过确认电子邮件和令牌匹配来获取用户记录。然后，我们调用 next 来调用第二个中间件，*、storePassword* ，这是我们之前创建的，但是我们将进行一点重构。

因为我们是从两个不同的场景调用 *storePassword* ，所以让它更灵活一些。首先，让我们将下一个参数添加到函数定义中，其次，让我们创建一个通用助手来更新记录。

```
// dataController.js

// Update any record in any table
exports.updateRecord = (table, recordId, fieldsToUpdate) => {
  table.update(recordId, fieldsToUpdate, (err, record) => {
    if (err) {
      console.log(Error(err));
      return;
    }

    return record;
  });
};

// userController

// Refactored storePassword middleware
exports.storePassword = (req, res, next) => {
  const { password, id } = req.body;

  bcrypt.hash(password, 10, function(err, hash) {
    if (err) {
      console.error(err);
      return;
    }

    req.body.hash = hash;

    data.updateRecord(table, id, {
      password: hash
    });

    next();
  });
};

// Since we are now using next, we need to update our previous routes to call the next middleware

// index.js
router.post(
  "/user/add",
  userController.addUser,
  userController.storePassword,
  appController.getLogin
);

// Step 4,5
router.post(
  "/user/reset",
  userController.confirmToken,
  userController.storePassword,
  userController.sendConfirmResetEmail
); 
```

## 第五步发送电子邮件确认密码更改

```
// userController.js

exports.sendConfirmResetEmail = async (req, res) => {
  const subject = "Password successfully reset";
  const to = req.body.email;
  const body = `Hello, Your password was successfully reset.`;

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
      res.render("login");
    }
  });
}; 
```

这就是所有的人。关于使用 Airtable 创建用户数据库的系列文章到此结束。我应该注意到有一些事情需要做好生产准备，但这是一个好的开始。你可以在 [Github](https://github.com/cjwd/user-database-airtable-tut) 上找到分步完成的代码。