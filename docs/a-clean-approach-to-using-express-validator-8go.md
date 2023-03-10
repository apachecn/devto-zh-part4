# 一种使用 Express Validator 的简洁方法

> 原文：<https://dev.to/nedsoft/a-clean-approach-to-using-express-validator-8go>

Express validator 是用于验证 Express 应用程序请求的众多 npm 包之一。

我最近在一个项目中使用了`express validator`,偶然发现了一些挑战，我将在本文中分享。

> 注意:本文假设读者已经有了一个 express 项目，并且只想使用`express validator`实现验证。因此，一些细节可能会被跳过。

当您访问 [express validator 文档](https://express-validator.github.io/docs/index.html)时，您会注意到示例中使用 validator 的方式，如下面的代码片段所示:

```
// ...rest of the initial code omitted for simplicity.
const { check, validationResult } = require('express-validator')

app.post(
  '/user',
  [
    // username must be an email
    check('username').isEmail(),
    // password must be at least 5 chars long
    check('password').isLength({ min: 5 }),
  ],
  (req, res) => {
    // Finds the validation errors in this request and wraps them in an object with handy functions
    const errors = validationResult(req)
    if (!errors.isEmpty()) {
      return res.status(422).json({ errors: errors.array() })
    }

    User.create({
      username: req.body.username,
      password: req.body.password,
    }).then(user => res.json(user))
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

查看上面的代码片段，您会注意到验证与路由定义紧密相关。这种模式对于一个非常简单的用例来说可能是可以的，但是当使用规模扩大时，就很难维护代码库，而且还会使路由定义不可读。

在本文中，我将展示如何使上面的验证更加易读和易于维护。

**第一步**

在`validator.js`中创建一个名为`validator.js`
的文件，我们将添加两个函数，其中一个函数将保存验证规则，而第二个函数将包含进行实际验证的函数。

将下面的片段复制到`validator.js`中

```
const { body, validationResult } = require('express-validator')
const userValidationRules = () => {
  return [
    // username must be an email
    body('username').isEmail(),
    // password must be at least 5 chars long
    body('password').isLength({ min: 5 }),
  ]
}

const validate = (req, res, next) => {
  const errors = validationResult(req)
  if (errors.isEmpty()) {
    return next()
  }
  const extractedErrors = []
  errors.array().map(err => extractedErrors.push({ [err.param]: err.msg }))

  return res.status(422).json({
    errors: extractedErrors,
  })
}

module.exports = {
  userValidationRules,
  validate,
} 
```

Enter fullscreen mode Exit fullscreen mode

**第二步**

现在重写上面最初的片段，我们会:

```
const { userValidationRules, validate } = require('./validator.js')
app.post('/user', userValidationRules(), validate, (req, res) => {
  User.create({
    username: req.body.username,
    password: req.body.password,
  }).then(user => res.json(user))
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您试图注册一个不符合用户数据规范的用户，验证错误响应将如下所示:

```
{  "errors":  [  {  "username":  "username must be an email"  },  {  "password":  "password must be at least 5 chars long"  },  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

有了这个方法，您就可以在一个单独的文件中为每个路由或模块定义您认为合适的验证规则，然后用`validate middleware`将它链接起来。这样代码看起来更干净，更容易阅读和维护。

这篇文章有很多假设，因此跳过了一些细节。但是，如果你有任何问题，请随时联系我。

*你可以在官方文档[网站](https://express-validator.github.io/docs/)* 上了解更多关于`express validator`的信息

> 这篇文章最初发表在我的博客上