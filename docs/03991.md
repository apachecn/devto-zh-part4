# 使用 Faker 为 Mongo 播种真实的记录

> 原文：<https://dev.to/cameronblandford/seeding-mongo-with-realistic-records-using-faker-509j>

所以你的应用程序基本上已经启动并运行了，有一个可用的 API，但是你仅有的数据是你自己添加到数据库中的手动条目。你不希望必须手动注册一万个不同的用户，每个人都有自己的几百个相关项目(博客帖子、待办事项等)，来了解你的应用在中等规模下的外观和工作方式。

输入 faker。 [Faker](https://github.com/marak/Faker.js/) 是一个库，用它自己的话说，*设计来产生大量的假数据*。通过连接到我们的 mongo 数据库，我们可以上传大量独特的用户和项目。

## 播种一个 Mongo 数据库

首先，安装我们将使用的两个依赖项`npm i -D faker mongodb lodash`。接下来，在您的服务器文件夹中创建一个新文件，最好是在脚本的子目录中，名为类似于`seed.js`的东西。

```
/* mySeedScript.js */
// require the necessary libraries
const faker = require("faker");
const MongoClient = require("mongodb").MongoClient;
const assert = require("assert");
const _ = require("lodash");
// Connection URL
const url = "mongodb://localhost:27017";

// Database Name
const dbName = "my_dev_database";

// Use connect method to connect to the server
MongoClient.connect(url, function(err, client) {
  assert.equal(null, err);

  const db = client.db(dbName);

  // get access to the relevant collections
  const usersCollection = db.collection("users");
  const postsCollection = db.collection("posts");
  // make a bunch of users
  let users = [];
  for (let i = 0; i < 5000; i += 1) {
    const firstName = faker.name.firstName();
    const lastName = faker.name.lastName();
    let newUser = {
      email: faker.internet.email(firstName, lastName),
      firstName,
      lastName,
      password: "password123"
    };
    users.push(newUser);

    // visual feedback always feels nice!
    console.log(newUser.email);
  }
  usersCollection.insertMany(users);

  // make a bunch of posts
  let posts = [];
  for (let i = 0; i < 5000; i += 1) {
    let newPost = {
      title: faker.lorem.words(7),
      body: faker.lorem.words(500),

      // use lodash to pick a random user as the author of this post
      author: _.sample(users),

      // use lodash to add a random subset of the users to this post
      likes: _.sampleSize(users, Math.round(Math.random * users.length)).map(
        user => user._id
      )
    };
    posts.push(newPost);

    // visual feedback again!
    console.log(newPost.title);
  }
  postsCollection.insertMany(posts);
  console.log("Database seeded! :)");
  client.close();
}); 
```

## 其他数据库

对于其他数据库来说，最好的方法是将您正在使用的 ORM 或数据库连接`require`到 seed 脚本中，然后使用 faker.js 和 ORM 的组合来完成上述操作。如果有兴趣，我很乐意写一个例子！

* * *

你最喜欢用什么方法为你的 API 生成假的/测试数据？下面让我知道！