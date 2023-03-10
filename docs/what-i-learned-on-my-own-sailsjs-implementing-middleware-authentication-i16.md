# 我自己学到的|在 SailsJS 上实现 JWT 认证(v1)

> 原文：<https://dev.to/christopheek/what-i-learned-on-my-own-sailsjs-implementing-middleware-authentication-i16>

**这篇文章将教会你什么:**

*   如何在你的 SailsJS *v1* 后端应用 JWT 认证？

**这篇文章不会教你的:**

*   如何创建 SailsJS 应用程序？
*   如何理解这些功能背后的机制？

我之所以不深入介绍一些函数的机制以及它们在后台的工作方式，是因为有很多文章和文档是由比我更有资格的人撰写的。

这篇文章是写给谁的？任何只需要在 SailsJS 中实现一些 JWT 认证的人，因为他们有一个截止日期，没有时间做长时间的研究。

如果你一直在使用 Sails 进行开发，你将会意识到你不可能得到你想要的所有答案，主要是因为 Sails 从`v0`到`v1`的迁移，它们不那么[的 laravel](https://laravel.com/docs/5.8) ish 文档以及伴随 Sails 的相当小的社区。

闲聊到此为止。让我抽根烟，然后我再回到技术细节上来。

* * *

*免责声明:这段代码的部分灵感来自[科里·伯恩鲍姆](https://github.com/vonWolfehaus/sails-jwt-login)写的。所以给这家伙点支持。*

这将被实现的方式如下，在一个一般的，非帆术语的句子中:

*   HTTP 请求(需要认证)到达您的后端
*   请求被[中间件](https://stackoverflow.com/questions/2904854/what-is-middleware-exactly)拦截
*   验证有效-重定向到控制器
*   认证无效- [401](https://httpstatuses.com/401)

很好，但是我们如何在 Sails 中实现它呢？为此，我假设你已经设置好了你的[路线和行动](https://sailsjs.com/documentation/concepts/actions-and-controllers/routing-to-actions)。

简单来说，Sails 中的一个*中间件*被称为一个[策略](https://sailsjs.com/documentation/concepts/policies)。这意味着，你要给一个控制器/动作分配一组规则，基本上是告诉那个控制器/动作“嘿！我知道您希望这个 HTTP 请求被重定向给您，但是首先我需要做一些检查。如果这些支票通过，我会把它送过去。否则，我就把那块糖从你身边拿走。”

先决条件:对于这里的 JWT 认证，我使用一个名为 [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) 的包。请确保将其安装在您的项目目录中。

因此，通过转到`config/policies.js`并在`module.exports.policies`主体中添加下面一行来定义该策略:

```
{
   controllerName: {
      'action-name': 'isAuthenticated'
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这样，您告诉`action-name``isAuthenticated`将对发送给您的 HTTP 请求进行一些检查，然后再决定是否对该请求采取行动。

下一步，您将不得不实际编写策略。Sails 的 CLI 提供了一个命令来[生成](https://sailsjs.com/documentation/reference/command-line-interface/sails-generate)任何东西，包括策略。所以在你的命令行中运行下面的命令:
`sails generate policy isAuthenticated`
如果一切顺利，你会在`api/policies/`目录中看到一个`isAuthenticated.js`文件。

在`isAuthenticated.js`策略中编写以下代码:

**代号**

```
/**
 * isAuthenticated
 *
 * @module      :: Policy
 * @description :: Simple policy to require an authenticated user, or else redirect to login page
 *                 Looks for an Authorization header bearing a valid JWT token
 * @docs        :: http://sailsjs.org/#!documentation/policies
 *
 */

module.exports = async function (req, res, next) {
    sails.helpers.verifyJwt.with({
        req: req,
        res: res
    })
        .switch({
            error: function (err) {
                return res.serverError(err)
            },
            invalid: function (err) {
                // if this is not an HTML-wanting browser, e.g. AJAX/sockets/cURL/etc.,
                // send a 401 response letting the user agent know they need to login to
                // access this endpoint.
                if (req.wantsJSON) {
                    return res.sendStatus(401)
                }
                // otherwise if this is an HTML-wanting browser, do a redirect.
                return res.redirect('/login')
            },
            success: function () {
                // user has been attached to the req object (ie logged in) so we're set, they may proceed
                return next()
            }
        })
} 
```

Enter fullscreen mode Exit fullscreen mode

**解释**
首先，这个策略得到了一个叫做`verifyJwt`的助手的帮助，我们一会儿就来写这个助手。它也给了它两个参数。请求`req`和响应`res`。起初，我对如何将这些参数从我的`policies.js`定义传递给`isAuthenticated`感到困惑。事实证明，sails 会自动处理这个问题，因为从本质上来说，策略是用来处理`req`和`res`的，它们只在 HTTP 请求中需要。

`verifyJwt`将返回`error`，或者`invalid`，或者`success`。每一种可能的回报都有自己的处理方式。如果一个`error`被返回，坏消息。您的代码或请求中有问题。如果返回一个`invalid`，对你来说是个好消息。您的代码正在工作，但是请求不会被转发到您的操作，因为请求没有经过身份验证。
如果返回一个`success`，那么用户通过了身份验证，请求被转发到您的操作，大家都很高兴。

现在来看一下`verifyJwt`助手函数。这将是您的身份验证逻辑的主要部分。

为此，我们必须创建一个助手
`sails generate helper verify-jwt`。

在`api/helpers/verify-jwt.js`里面，我们将编写
**代码**

```
var jwt = require('jsonwebtoken')

module.exports = {
  friendlyName: 'Verify JWT',
  description: 'Verify a JWT token.',
  inputs: {
    req: {
      type: 'ref',
      friendlyName: 'Request',
      description: 'A reference to the request object (req).',
      required: true
    },
    res: {
      type: 'ref',
      friendlyName: 'Response',
      description: 'A reference to the response object (res).',
      required: false
    }
  },
  exits: {
    invalid: {
      description: 'Invalid token or no authentication present.',
    }
  },
  fn: function (inputs, exits) {
    var req = inputs.req
    var res = inputs.res
    if (req.header('authorization')) {
      // if one exists, attempt to get the header data
      var token = req.header('authorization').split('Bearer ')[1]
      // if there's nothing after "Bearer", no go
      if (!token) return exits.invalid()
      // if there is something, attempt to parse it as a JWT token
      return jwt.verify(token, process.env.JWT_KEY, async function (err, payload) {
        if (err || !payload.sub) return exits.invalid()
        var user = await User.findOne(payload.sub)
        if (!user) return exits.invalid()
        // if it got this far, everything checks out, success
        req.user = user
        return exits.success(user)
      })
    }
    return exits.invalid()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**解释**

*   首先，助手检查请求中的`authorization`头。如果它不存在，则该请求未经身份验证，并将`返回 exits . invalid()；
*   如果`authorization`报头**存在**，我们提取`token`，并运行`jsonwebtoken`的`verify`函数。这个函数将返回一个`payload`。
*   根据 [JWT 解剖](https://jwt.io/)，在`payload`里面应该存在一个`sub`属性
*   如果有，它将很可能保存用户的密钥标识符(例如 ID)。
*   一旦我们有了这个`sub`，我们需要使用它来尝试找到一个用户，这个 JWT 属于谁。
*   如果找到该用户，将其分配到您的`req`。我们这样做的原因是，如果出于代码中的某种原因，我们需要验证用户 a 请求的某个操作，而用户 a 确实已经通过了身份验证，可以对用户 b 的一些财产进行操作。因此，即使用户 a 通过了身份验证，也不应该允许他们执行与用户 b 相关的任何操作(例如，编辑用户 b 的博客帖子)。

瞧。您已经启动并运行了身份验证。

如果你有任何问题，反馈，纠正我可能提供的任何错误信息(包括错别字)，我的直接消息对任何人开放，但我强烈建议你在评论区张贴它们，让每个人从中受益。

快乐编码伙计们！