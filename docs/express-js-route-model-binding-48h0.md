# Express.js:路由模型绑定

> 原文：<https://dev.to/mirkojotic/express-js-route-model-binding-48h0>

我使用 express.js 已经有一段时间了，但是直到前几天我才知道这个漂亮的`router.param`方法。如果某个占位符出现在路由中，它允许您执行回调。

```
const express = require("express");
const app = express();

const router = express.Router();

route.param('user', function(req, res, next) {
  // if ":user" placeholder is in any of the router's route definitions
  // it will be intercepted by this middleware
  const user = { id: 1, name: 'Mirko' };
  req.user = user;
  next();
});

router.get("/:user", function(req, res) {
  // req.user will be populated with { id: 1, name: 'Mirko' }
  return res.json({ result: req.user });
});

app.use("/api/users", router);

app.listen(3000); 
```

这是一个非常有用的特性，因为您经常会有一个路由器不断地从数据库中获取模型以进行进一步的操作。如果没有别的，它真的清理代码。

但是如果我们有点创意呢？我想到的第一件事是有某种“绑定注册过程”,然后通过应用程序动态绑定参数。有了像 Laravel 这样的框架(顺便说一句，Laravel 已经支持这一点，并且是这个后信贷时代信贷到期的灵感),关于模型和它们的位置有某些约定。我们将依赖配置而不是约定，并指定模型获取函数。

最终结果如下:

```
const express = require("express");
const app = express();
const assert = require("assert");

const router = express.Router();

function getUser(id) {
  // these functions can do a variety of things 
  // and if an error is throws it will be picked up by 
  // express error handler
  return Promise.resolve({ id: 1, name: "Mirko" });
}
function getPost(id) {
  // imagine we execute a database query here :)
  return Promise.resolve({ id: 1, title: "Express.js is cool" });
}

const bindings = [
  { param: "user", handler: getUser },
  { param: "post", handler: getPost }
];

function handleParam({ param, handler }) {
  // just a sanity check to make sure we have what we need
  assert(param, "Binding mush have a param");
  assert(handler, "Binding must have a handler");
  // second argument to `route.param` must be a function 
  // of similar signature to a normal middleware with exception of
  // having an additional parameter which represents the value of placeholder
  return function(req, res, next, id) {
    return handler(id)
      .then(model => {
        // we assign the db model to request object for future use
        req[param] = model;
        next();
      })
      .catch(err => {
        // any errors thrown by handler will be passed to express error handler
        next(err);
      });
  };
}

bindings.forEach(function(binding) {
  router.param(binding.param, handleParam(binding));
});

router.get("/:user/posts/:post", function(req, res) {
  return res.json({ user: req.user, post: req.post });
});

router.get("/:user", function(req, res) {
  return res.json({ result: req.user });
});

app.use("/api/users", router);

app.listen(3000); 
```

如果您正在本地运行此程序，请在浏览器中导航到[http://localhost:3000/API/users/1/posts/1](http://localhost:3000/api/users/1/posts/1)并查看结果。