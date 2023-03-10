# 全堆叠:连接节点↔ MongoDB

> 原文：<https://dev.to/heymarkkop/fullstacking-connecting-nodejs-mongodb-32l4>

随着 [React Native](https://dev.to/heymarkkop/fullstacking-setting-up-react-native-1g21) 、 [NodeJS + KoaJS](https://dev.to/heymarkkop/fullstacking-setting-up-nodejs-koajs-2jhb) 和 [MongoDB](https://dev.to/heymarkkop/fullstacking-setting-up-mongodb-3oed) 的设置，我们可以开始将它们相互连接。

## KoaJS ↔ MongoDB

因为我想学习和实践**T**est-**D**riven**D**development，我们将首先创建测试，并且似乎 [Mocha](https://mochajs.org/#getting-started) 比 Jest 更推荐[来测试 Mongoose(我们将在后面使用)。
我们还将使用](https://mongoosejs.com/docs/jest.html)[超级测试](https://github.com/visionmedia/supertest)进行集成测试。

`yarn add mocha --dev`
T1】

像这样创建一个测试文件:

```
// server.test.js
const request = require('supertest');
const app = require('./koa');

describe('Server', () => {
  it('is running', done => {
    request(app)
      .get('/')
      .expect(200, done);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

并将`package.json`更改如下:

```
// package.json
...
  "scripts": {
    ...
    "test": "mocha server.test.js --watch",
    ...
  },
... 
```

Enter fullscreen mode Exit fullscreen mode

我使用了`mocha './server/*.test.js' --recursive --watch`,所以它运行服务器文件夹中的所有测试文件。我们以后可能会改变这一点。

运行`yarn test`并找到`TypeError: app.address is not a function`，因为 app 还不存在，因此是时候写实际的代码了

```
// server.js
const Koa = require('koa');

const app = new Koa();

app.use(async ctx => {
  ctx.body = "Hello World, I'm Koa";
});

module.exports = app.listen(3000, () =>
  console.log('Running on http://localhost:3000/'),
); 
```

Enter fullscreen mode Exit fullscreen mode

别忘了把它带走。
现在我们的第一个测试通过了，但是当试图再次运行测试时，它可能会抛出`Uncaught Error: listen EADDRINUSE: address already in use :::3000`或者 **-看着**它。
我们必须在每次测试后[关闭](https://github.com/visionmedia/supertest/issues/437)服务器，所以在`describe()`块
中添加这个

```
// server.test.js
...
 afterEach(() => {
    app.close();
  });
... 
```

Enter fullscreen mode Exit fullscreen mode

随着 Koa 的运行和测试，我们现在可以尝试从 MongoDB 实例中读取一些信息。

让我们试着通过添加这个测试
来找到我们的[印记](https://dev.to/heymarkkop/fullstacking-setting-up-mongodb-3oed)

```
// server.test.js
...
  it('finds our stampler', done => {
    request(app)
      .get('/')
      .expect(/Stampler/)
      .expect(200, done);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

它将返回`Error: expected body 'Hello World, I\'m Koa' to match /stampler/`，因为`ctx.body`是一个纯文本，而不是我们数据库中的数据。
要访问它，我们将使用[猫鼬](https://mongoosejs.com/docs/index.html)
`yarn add mongoose`或`npm install mongoose`

创建一个`Product.js`来定义一个新的模式

```
// Product.js
var mongoose = require('mongoose');

const ProductSchema = mongoose.Schema({
  title: String,
});

module.exports = mongoose.model('Product', ProductSchema); 
```

Enter fullscreen mode Exit fullscreen mode

并按如下方式使用它

```
// server.js
const Koa = require('koa');
const mongoose = require('mongoose');
const Product = require('./Product');

const app = new Koa();

mongoose.connect('mongodb://127.0.0.1:27017/test', {useNewUrlParser: true});

app.use(async ctx => {
  //ctx.body = "Hello World, I'm Koa";
  ctx.body = await Product.find({});
});

module.exports = app.listen(3000, () =>
  console.log('Running on http://localhost:3000/'),
); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您的 [MongoDB 应该正在运行](https://dev.to/heymarkkop/fullstacking-setting-up-mongodb-3oed)，否则您将得到

```
MongoNetworkError: failed to connect to server [127.0.0.1:27017] on first connect [MongoNetworkError: connect ECONNREFUSED 127.0.0.1:27017] 
```

Enter fullscreen mode Exit fullscreen mode

您可以在终端中运行`mongo`并运行
T1 来启动它。

起初它应该可以工作，但是你可能会注意到一个错误，如果 **-观看**测试套件:
`OverwriteModelError: Cannot overwrite 'Product' model once compiled.`
要修复它，像你对 afterEach 所做的那样在 beforeEach 中添加它，所以它在再次测试之前删除产品模型。

```
// server.test.js
...
  beforeEach(async () => {
    const url = 'mongodb://127.0.0.1:27017/test';
    await mongoose.connect(url, {useNewUrlParser: true});
    delete mongoose.connection.models.Product;
  });
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们试着添加一个新产品，检查它是否存在，然后清理它。让我们也分开一些`describes`

```
// server.test.js
const request = require('supertest');
const mongoose = require('mongoose');
const app = require('./app');
const Product = require('./Product');

describe('Server', () => {
  describe('without acessing MongoDB', () => {
    afterEach(() => {
      app.close();
    });

    it('is successful', done => {
      request(app)
        .get('/')
        .expect(200, done);
    });

    it('finds our stampler', done => {
      request(app)
        .get('/')
        .expect(/Stampler/)
        .expect(200, done);
    });
  });

  describe('acessing MongoDB direcly', () => {
    afterEach(() => {
      Product.deleteOne({title: 'skate'}).exec();
    });

    beforeEach(async () => {
      const url = 'mongodb://127.0.0.1:27017/test';
      await mongoose.connect(url, {useNewUrlParser: true});
      delete mongoose.connection.models.Product;
    });

    it('creates and finds a skate', done => {
      const skate = new Product({title: 'skate'});
      skate.save();
      request(app)
        .get('/')
        .expect('Content-Type', /json/)
        .expect(/skate/)
        .expect(200, done);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这可能不是测试集成的最佳和最优雅的方式，但是我会在评论中留下提示和建议

## 参考文献

[Hackernoon -使用 SuperTest](https://hackernoon.com/api-testing-using-supertest-1f830ce838f1)
[零碎的 API 测试-用 Mocha 和 mongose](https://blog.bitsrc.io/build-a-unit-testing-suite-with-mocha-and-mongoose-eba06c3b3625)
[Zellwk-连接 Jest 和 mongose](https://zellwk.com/blog/jest-and-mongoose/)
[smooth terminal-用 Koa.js 构建 API](https://www.smoothterminal.com/articles/build-an-api-with-koa-js)