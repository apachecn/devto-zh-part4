# 如何在 Node.js 和 Docker 中使用 dotenv 管理机密和配置

> 原文：<https://dev.to/getd/how-to-manage-secrets-and-configs-using-dotenv-in-node-js-and-docker-2214>

# 什么是`dotenv`？

这是一个 javascript 包，它从一个`.env`文件中读取`KEY=VALUE`(下面的例子)并将每一对设置为 env 变量。

```
// 1\. Create a .env file:
DB_ADMIN_USERNAME=techbos
DB_ADMIN_PASSWORD=Pa$$w0rd

// 2\. In your node app, load dotenv to read .env and set env variables when app starts
require('dotenv').config();

// 3\. You can now use the env variables in your app
connectDatabase({
  username: process.env.DB_ADMIN_USERNAME, //techbos
  password: process.env.DB_ADMIN_PASSWORD, // Pa$$w0rd
}); 
```

# 为什么要用`dotenv`？

允许你从源代码中分离出秘密。这在协作环境(例如，工作或开放源代码)中很有用，在这种环境中，您可能不想与其他人共享您的数据库登录凭据。相反，您可以共享源代码，同时允许其他人创建他们自己的`.env`文件。

它也有助于动态配置应用程序，而无需更改源代码。例如，您可以将`DB_URL`设置为用于本地开发的 dev 数据库。或者，如果您想将日志打印到控制台用于本地开发，而不是在 prod 中，您可以:

```
// .env file
ENV=dev

// in your app
if (process.env.ENV === 'dev') console.log(...); 
```

对于部署到数百甚至数千个实例的真实世界的应用程序，使用`dotenv`(或使用其他类似的技术)允许所有实例共享相同的源代码，而每个实例都有不同的`.env`文件，因此它们可以使用不同的配置，例如，连接到不同的数据库或向不同的端点写入日志。

# 如何在我的项目中设置`dotenv`？

## 1。在 node.js 中预加载`dotenv`

用预加载的`dotenv`启动你的节点应用，这样你甚至不需要在你的源代码中要求 dotenv。

```
// use this
node -r dotenv/config index.js
// instead of
require('dotenv').config(); 
```

## [2](#2-add-raw-env-endraw-to-raw-gitignore-endraw-)。将`.env`添加到`.gitignore`

你不应该在源代码控制中共享`.env`文件。相反，创建一个单独的私有 repo 来存储您自己的`.env`文件，同时与他人共享其余的源代码。

```
//.gitignore
.env 
```

## 3。创建尽可能多的`.env`

通常会有多个`.env`文件。例如，您可能有一个用于开发，一个用于阶段，一个用于生产。签出源代码后，复制适合您环境的`.env`文件并启动应用程序。

## 3。启动你的应用

```
// E.g., use with babel-node for local development
nodemon --exec babel-node -r node_modules/dotenv/config src/index.js
// E.g., run with node in production
node -r dotenv/config src/index.js 
```

## 4。在码头运行

```
// Dockerfile
CMD node -r dotenv/config ./build/index.js 
```

# 使用`dotenv-safe`

一个很好的替代`dotenv`的方法是 [`dotenv-safe`](https://www.npmjs.com/package/dotenv-safe) ，它与`dotenv`完全相同，除了它允许你创建一个`.env.example`文件。这有两个目的:

1.  它允许您在示例文件中指定所有必需的 env 变量。在运行时，包检查一个`.env`文件是否定义了所有需要的变量，如果没有，抛出。
2.  您可以与其他人共享`.env.example`文件，这样每个人都知道需要什么 env 变量。

# 在 Docker 中使用

在某些情况下，将`.env`文件构建到 docker 映像中被认为是一种不好的做法，因为如果该映像与其他人共享，秘密可能会被泄露。

为了解决这个问题，您可以在运行时将一个`.env`文件映射到 docker:

```
volumes:
  - /local/file/path/to/.env:/app/.env 
```

另一种方法是使用`env_file` docker 选项。你可以在这里阅读更多