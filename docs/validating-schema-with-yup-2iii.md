# 用 Yup 验证模式！

> 原文：<https://dev.to/joaohencke/validating-schema-with-yup-2iii>

你认为在 JavaScript 中验证类型很无聊吗？你听说过[是的](https://github.com/jquense/yup)吗？验证**模式**的 JavaScript 库令人惊叹！

让我们想象一下:

我们有一个`list products`端点，它将`page number`和`limit`作为来自`querystring`
的参数

```
//productApi.js
const { Router } = require('express');
const productController = require('./controller');

const router = new Router();

module.exports = app => app.use('/products', router);

router.get('/', (req, res, next) => {
  const { page, limit } = req.query;

  if (isNaN(page) || isNaN(limit)) return res.status(400).end();

  productController.list({ page: Number(page), limit: Number(limit) })
    .then(products => res.json(products))
    .catch(error => res.status(400).json({ error }));
} 
```

Enter fullscreen mode Exit fullscreen mode

问题是:`page`和`limit`可以是`undefined`和...`anything else`...是 javascript 宝贝。

一个非常有效的方法是创建一个中间件，使用 [Yup](https://github.com/jquense/yup) 来验证您的参数。

```
//validate.js
const yup = require('yup');

module.exports = ({ shape, path = 'query' }) => async (req, res, next) => {
  const schema = yup.object().shape(shape);

  try {
    const validData = await schema.validate(req[path]);
    req.validData = validData;
    return next();
  } catch (error) {
    return res.status(400).json({ error });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

之后，在路线上使用

```
//productApi.js
const { Router } = require('express');
const yup = require('yup');
const validate = require('./validate');
const productController = require('./controller');

const router = new Router();

module.exports = app => app.use('/products', router);

router.get(
  '/',
  validate({
    shape: {
      page: yup.number().default(0),
      limit: yup.number().default(15),
    }
  }),
  (req, res, next) => {
    productController
      .list(req.validData)
      .then(products => res.json(products))
      .catch(error => res.status(400).json({ error }));
  },
); 
```

Enter fullscreen mode Exit fullscreen mode

当然，您可以用[是的](https://github.com/jquense/yup)创建复杂的模式。它按照你的需要工作。

希望对某个人有帮助！