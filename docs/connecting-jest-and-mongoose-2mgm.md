# 连接笑话和猫鼬

> 原文：<https://dev.to/zellwk/connecting-jest-and-mongoose-2mgm>

测试后端应用程序的困难部分是建立一个测试数据库。这可能很复杂。

今天，我想分享我是如何设置 Jest 和 Mongoose 的。

## 用笑话设定猫鼬

如果你试图用猫鼬和 Jest 一起玩，Jest 会给你一个警告。

[![Warning if you try to use Mongoose with Jest](img/96d5b38996a490838b9d9432b3ea452f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0eg22ngM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2019/jest-and-mongoose/mongoose-jest-warning.png)

如果你不想看到这个错误，你需要在你的`package.json`文件中设置`testEnvironment`到`node`。

```
"jest": {
  "testEnvironment": "node"
} 
```

## 在测试文件中设置 Mongoose

您希望在开始任何测试之前连接到数据库。您可以使用`beforeAll`挂钩来完成此操作。

```
beforeAll(async () => {
  // Connect to a Mongo DB
}); 
```

要连接到 MongoDB，可以使用 Mongoose 的`connect`命令。

```
const mongoose = require("mongoose");
const databaseName = "test";

beforeAll(async () => {
  const url = `mongodb://127.0.0.1/${databaseName}`;
  await mongoose.connect(url, { useNewUrlParser: true });
}); 
```

这将创建一个到名为`test`的数据库的连接。您可以将数据库命名为任何名称。稍后您将学习如何清理它们。

注意:在测试之前，确保您有一个活动的本地 MongoDB 连接。如果没有活动的本地 MongoDB 连接，您的测试将会失败。阅读这个来学习如何创建一个本地 MongoDB 连接。

## 为每个测试文件创建数据库

测试时，您希望为每个测试文件连接到不同的数据库，因为:

1.  Jest 异步运行每个测试文件。你不会知道哪个文件先来。
2.  您不希望测试共享同一个数据库。您不希望一个测试文件中的数据溢出到下一个测试文件中。

要连接到不同的数据库，您需要更改数据库的名称。

```
// Connects to database called avengers
beforeAll(async () => {
  const url = `mongodb://127.0.0.1/avengers`;
  await mongoose.connect(url, { useNewUrlParser: true });
}); 
```

```
// Connects to database power-rangers
beforeAll(async () => {
  const url = `mongodb://127.0.0.1/power-rangers`;
  await mongoose.connect(url, { useNewUrlParser: true });
}); 
```

## 发送发布请求

假设您想要为您的应用程序创建一个用户。用户有一个名字和一个电子邮件地址。您的 Mongoose 模式可能如下所示:

```
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const userSchema = new Schema({
  name: String,
  email: {
    type: String,
    require: true,
    unique: true
  }
});

module.exports = mongoose.model("User", userSchema); 
```

要创建一个用户，需要将`name`和`email`保存到 MongoDB 中。您的路线和控制器可能看起来像这样:

```
const User = require("../model/User"); // Link to your user model

app.post("/signup", async (req, res) => {
  const { name, email } = req.body;
  const user = new User({ name, email });
  const ret = await user.save();
  res.json(ret);
}); 
```

要将用户保存到数据库中，您可以向`signup`发送 POST 请求。要发送 post 请求，可以使用`post`方法。要随 POST 请求一起发送数据，可以使用`send`方法。在你的测试中，它会像这样。

```
it("Should save user to database", async done => {
  const res = await request.post("/signup").send({
    name: "Zell",
    email: "testing@gmail.com"
  });
  done();
}); 
```

注意:如果您运行这段代码两次，您将得到一个`E1100 duplicate key error`。发生此错误的原因是:

1.  我们在上面的模式中说过`email`应该是`unique`。
2.  我们试图用`testing@gmail.com`创建另一个用户。即使数据库中已经存在一个。(第一个是在您发送第一个请求时创建的)。

[![Duplicate key error.](img/42f6452bf74ac5146062378460c9b7e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X8kjdisi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2019/jest-and-mongoose/duplicate-error.png)

## 在测试之间清理数据库

您希望在每次测试之间从数据库中删除条目。这确保您总是从空数据库开始。

你可以用`afterEach`钩来完成。

```
// Cleans up database between each test
afterEach(async () => {
  await User.deleteMany();
}); 
```

在上面的代码中，我们只清除了数据库中的`User`集合。在实际场景中，您希望清除所有集合。您可以使用下面的代码来实现这个目的:

```
async function removeAllCollections() {
  const collections = Object.keys(mongoose.connection.collections);
  for (const collectionName of collections) {
    const collection = mongoose.connection.collections[collectionName];
    await collection.deleteMany();
  }
}

afterEach(async () => {
  await removeAllCollections();
}); 
```

## 测试终点

让我们开始测试。在这个测试中，我们将向`/signup`端点发送一个 POST 请求。我们希望确保:

1.  用户被保存到数据库中
2.  返回的对象包含有关用户的信息

### 检查用户是否保存到数据库中

要检查用户是否被保存到数据库中，您需要在数据库中搜索该用户。

```
const User = require("../model/User"); // Link to your user model

it("Should save user to database", async done => {
  const res = await request.post("/signup").send({
    name: "Zell",
    email: "testing@gmail.com"
  });

  // Searches the user in the database
  const user = await User.findOne({ email: "testing@gmail.com" });

  done();
}); 
```

如果您是用户，您应该会看到类似这样的内容:

[![User object from MongoDB.](img/69923d217dfbb5de943cbfdbf22aad91.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9YN-rXVz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://zellwk.cimg/2019/jest-and-mongoose/user.png)

这意味着我们的用户被保存到数据库中。如果我们想确认用户有一个名字和一个电子邮件，我们可以做`expect`他们是真实的。

```
it("Should save user to database", async done => {
  // Sends request...

  // Searches the user in the database
  const user = await User.findOne({ email: "testing@gmail.com" });
  expect(user.name).toBeTruthy();
  expect(user.email).toBeTruthy();

  done();
}); 
```

### 检查返回的对象是否包含用户的信息

我们希望确保返回的对象包含用户的姓名和电子邮件地址。为此，我们检查 post 请求的响应。

```
it("Should save user to database", async done => {
  // Sends request...

  // Searches the user in the database...

  // Ensures response contains name and email
  expect(res.body.name).toBeTruthy();
  expect(res.body.email).toBeTruthy();
  done();
}); 
```

我们已经做完测试了。我们想从 MongoDB 中删除数据库。

## 删除数据库

若要删除数据库，您需要确保数据库中有 0 个集合。我们可以通过删除我们使用的每个集合来做到这一点。

我们将在所有测试运行完毕后，在`afterAll`钩子中执行。

```
afterAll(async () => {
  // Removes the User collection
  await User.drop();
}); 
```

要删除您的所有收藏，您可以使用这个:

```
async function dropAllCollections() {
  const collections = Object.keys(mongoose.connection.collections);
  for (const collectionName of collections) {
    const collection = mongoose.connection.collections[collectionName];
    try {
      await collection.drop();
    } catch (error) {
      // This error happens when you try to drop a collection that's already dropped. Happens infrequently.
      // Safe to ignore.
      if (error.message === "ns not found") return;

      // This error happens when you use it.todo.
      // Safe to ignore.
      if (error.message.includes("a background operation is currently running"))
        return;

      console.log(error.message);
    }
  }
}

// Disconnect Mongoose
afterAll(async () => {
  await dropAllCollections();
}); 
```

最后，您希望关闭 Mongoose 连接来结束测试。你可以这样做:

```
afterAll(async () => {
  await dropAllCollections();
  // Closes the Mongoose connection
  await mongoose.connection.close();
}); 
```

这就是用 Jest 设置 Mongoose 所需要做的一切！

## 重构

有很多代码被放入`beforeEach`、`afterEach`和`afterAll`钩子中。我们将在每个测试文件中使用它们。为这些钩子创建一个安装文件是有意义的。

```
// test-setup.js
const mongoose = require("mongoose");
mongoose.set("useCreateIndex", true);
mongoose.promise = global.Promise;

async function removeAllCollections() {
  const collections = Object.keys(mongoose.connection.collections);
  for (const collectionName of collections) {
    const collection = mongoose.connection.collections[collectionName];
    await collection.deleteMany();
  }
}

async function dropAllCollections() {
  const collections = Object.keys(mongoose.connection.collections);
  for (const collectionName of collections) {
    const collection = mongoose.connection.collections[collectionName];
    try {
      await collection.drop();
    } catch (error) {
      // Sometimes this error happens, but you can safely ignore it
      if (error.message === "ns not found") return;
      // This error occurs when you use it.todo. You can
      // safely ignore this error too
      if (error.message.includes("a background operation is currently running"))
        return;
      console.log(error.message);
    }
  }
}

module.exports = {
  setupDB(databaseName) {
    // Connect to Mongoose
    beforeAll(async () => {
      const url = `mongodb://127.0.0.1/${databaseName}`;
      await mongoose.connect(url, { useNewUrlParser: true });
    });

    // Cleans up database between each test
    afterEach(async () => {
      await removeAllCollections();
    });

    // Disconnect Mongoose
    afterAll(async () => {
      await dropAllCollections();
      await mongoose.connection.close();
    });
  }
}; 
```

您可以像这样导入每个测试的设置文件:

```
const { setupDB } = require("../test-setup");

// Setup a Test Database
setupDB("endpoint-testing");

// Continue with your tests... 
```

我还想给你看一样东西。

当您创建测试时，您希望在数据库中植入虚假数据。我想出了一个很容易植入数据库的方法。我将在下一篇文章中向您展示。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。