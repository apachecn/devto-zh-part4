# 创建您的第一个无服务器 API

> 原文：<https://dev.to/codetips/create-your-first-serverless-api-21o5>

[![Create your first Serverless API](img/d7fe8415be47401b5c9509110b7b4128.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6NrhIVAG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/markus-spiske-1614222-unsplash.jpg)

API 是互联网工作的基础。它们是您的应用程序与服务器交互的方式，因此，正如您所料，拥有一个可靠的 API 对您的应用程序的完整性至关重要。

## 那么什么是 API 呢？

API 代表应用程序编程接口，冒着低估其重要性的风险，它是一种通过一系列预定义的请求与服务器或第三方服务进行交互的方式，这些请求通常会返回数据。

我喜欢 API 与餐馆菜单的类比。你从菜单中挑选，然后餐馆返回订单。简单吧？

<figure>[![Create your first Serverless API](img/28878162a86ffd5c3cb34cdd8433281d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Rqhp6Z98--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_OcmVkcsM5BWRHrg8GC17iw.png)

<figcaption>API 是可以访问数据库的应用程序的接入点。[学分](https://medium.com/@perrysetgo/what-exactly-is-an-api-69f36968a41f)</figcaption>

</figure>

> 当人们谈到“一个 API”时，他们有时会泛指“一个公共可用的基于 web 的 API，它返回数据，可能是 JSON 或 XML 格式的”。API 不是数据库，甚至不是服务器，它是控制服务器的*访问点*的代码。——[佩里·艾森](https://medium.com/@perrysetgo)

## 如何构建一个 API？

好了，我们已经确定了 API 是什么，以及它对您的应用程序有多重要；让我们看看如何着手自己建造一个。

由于 AWS Lambda 非常适合 API，我们将使用它来构建我们的第一个 API。您可以免费构建一个，因为您可以免费获得前一百万个请求，所以它非常适合您的第一个 API。

虽然您可以不使用平台来完成这一切，但我们将使用最流行的无服务器平台，等等，这就是[无服务器](https://serverless.com)平台！

我知道你在想什么，他们之所以这么叫可能是因为他们发明了这整个无服务器的东西，但是，不，他们这么叫只是为了迷惑开发者，迷惑那些刚刚加入无服务器潮流的人。

您需要做的第一件事是安装和配置您的 AWS 和无服务器平台。为了简洁明了，我将链接最简单的教程[来设置无服务器](https://hackernoon.com/a-crash-course-on-serverless-with-node-js-632b37d58b44)。

## 让我们从你的第一个 API 开始吧

我假设你已经安装了`serverless`并且连接了你的账户。接下来我们将安装所有必要的东西来构建我们的 API——所以让我们直接开始吧。您所需要的只是一个终端，所以打开它并键入以下命令。

```
$ serverless create -t aws-nodejs -p rest-api 
$ cd rest-api 
```

这将创建一个新的样板服务。第二行只是导航到新创建的文件夹

现在我们需要安装将要使用的模块。我不会花太多时间解释这些，因为它们是不言自明的。

我们安装`serverless-offline`，模拟`serverless`平台，您安装`mongoose`，满足您的数据库相关需求。您需要确保您在我们之前创建的`rest-api`文件夹中。

```
$ npm init -y
$ npm i --save-dev serverless-offline
$ npm i --save mongoose dotenv 
```

接下来，您必须使用 Mongodb Atlas 来设置您的数据库；你可以使用的免费服务。我们将把它作为一个沙箱来玩我们的 API。

设置不太复杂，应该只需要几分钟。建立帐户后，你需要做的第一件事是创建一个组织并给它起个名字。为了保持简洁，我们称它为“rest-api”。

[![Create your first Serverless API](img/d8eeb813b740f80bcca8874ba8aa2732.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e-vQr6LZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_hM21Qo6sum_Bcj98giFYzQ.png)

之后，您需要创建一个项目。在“organization”部分点击“New Project”，然后创建另一个项目，我们巧妙地称之为“rest-api”。

[![Create your first Serverless API](img/a3032e1addea3198df205abcbc70aaef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QDRrcJGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_sOxiyVzJjVFF_cV5V7oO6w.png)

然后我们需要创建一个集群。在项目页面中，单击“Build a New Cluster”按钮，这将带您进入集群创建向导。在这里你会选择“M0”，这是他们的免费选项。您需要禁用备份，这可以在同一页面上完成。

[![Create your first Serverless API](img/ab5b44f57cb367f019882577a960f318.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3zecUAQI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_pYF8SbJsPASbLX2su7p8AQ.png)

[![Create your first Serverless API](img/a89307eff05b91e6f70c5e6c2c057252.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sXdrVfq---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_B0keX3l5GWSefyprHzoNOA.png)

不要忘记设置您的用户名和密码，并将它们保存在安全的位置。之后，我们开始写一些代码。

## 配置您的无服务器框架

虽然我们不使用无服务器框架也可以做到这一点，但是使用框架的众多好处之一是可以轻松地设置一个全新的项目。

初始化平台后(你之前已经完成了这一部分),你的文件夹里会有一堆文件。其中，你会看到`serverless.yml`和`handler.js`。

让我们从打开`serverless.yml`并添加以下代码开始:

```
service: rest-api

provider:
  name: aws
  runtime: nodejs6.10 # set node.js runtime
  memorySize: 128 # set the maximum memory of the Lambdas in Megabytes
  timeout: 10 # the timeout is 10 seconds (default is 6 seconds)
  stage: dev # setting the env stage to dev, this will be visible in the routes
  region: us-east-1

functions: # add 4 functions for CRUD
  create:
    handler: handler.create # point to exported create function in handler.js
    events:
      - http:
          path: notes # path will be domain.name.com/dev/notes
          method: post
          cors: true
  getOne:
    handler: handler.getOne
    events:
      - http:
          path: notes/{id} # path will be domain.name.com/dev/notes/1
          method: get
          cors: true
  getAll:
    handler: handler.getAll # path will be domain.name.com/dev/notes
    events:
     - http:
         path: notes
         method: get
         cors: true
  update:
    handler: handler.update # path will be domain.name.com/dev/notes/1
    events:
     - http:
         path: notes/{id}
         method: put
         cors: true
  delete:
    handler: handler.delete
    events:
     - http:
         path: notes/{id} # path will be domain.name.com/dev/notes/1
         method: delete
         cors: true

plugins:
- serverless-offline # adding the plugin to be able to run the offline emulation 
```

为了不使教程过于复杂，我将快速回顾一下我们刚刚做的事情。

我们将我们的服务命名为“rest api”，并将我们的 Lambdas 配置为使用最大 128MB 的 Ram。你可以从 50mb 增加到 3000mb，但是你增加的内存越多，成本就越高。

接下来，我们创建了一个简单的 CRUD 功能，使用了 5 个函数:`create`、`getOne`、`getAll`、`update`和`delete`，它们将指向我们的`handler.js`文件，我们马上就会看到这个文件。

最后，同样重要的是`serverless-offline`插件，我们将在上线之前用它来测试我们的服务。

## 给函数添加逻辑

我们的五个功能并不需要太多就能运行。一旦我们定义了它们做什么，剩下要做的就是添加数据库连接，我们就完成了。这里是你的`handler.js`文件需要的代码。

```
'use strict';

module.exports.create = (event, context, callback) => {
  context.callbackWaitsForEmptyEventLoop = false;

  connectToDatabase()
    .then(() => {
      Note.create(JSON.parse(event.body))
        .then(note => callback(null, {
          statusCode: 200,
          body: JSON.stringify(note)
        }))
        .catch(err => callback(null, {
          statusCode: err.statusCode || 500,
          headers: { 'Content-Type': 'text/plain' },
          body: 'Could not create the note.'
        }));
    });
};

module.exports.getOne = (event, context, callback) => {
  context.callbackWaitsForEmptyEventLoop = false;

  connectToDatabase()
    .then(() => {
      Note.findById(event.pathParameters.id)
        .then(note => callback(null, {
          statusCode: 200,
          body: JSON.stringify(note)
        }))
        .catch(err => callback(null, {
          statusCode: err.statusCode || 500,
          headers: { 'Content-Type': 'text/plain' },
          body: 'Could not fetch the note.'
        }));
    });
};

module.exports.getAll = (event, context, callback) => {
  context.callbackWaitsForEmptyEventLoop = false;

  connectToDatabase()
    .then(() => {
      Note.find()
        .then(notes => callback(null, {
          statusCode: 200,
          body: JSON.stringify(notes)
        }))
        .catch(err => callback(null, {
          statusCode: err.statusCode || 500,
          headers: { 'Content-Type': 'text/plain' },
          body: 'Could not fetch the notes.'
        }))
    });
};

module.exports.update = (event, context, callback) => {
  context.callbackWaitsForEmptyEventLoop = false;

  connectToDatabase()
    .then(() => {
      Note.findByIdAndUpdate(event.pathParameters.id, JSON.parse(event.body), { new: true })
        .then(note => callback(null, {
          statusCode: 200,
          body: JSON.stringify(note)
        }))
        .catch(err => callback(null, {
          statusCode: err.statusCode || 500,
          headers: { 'Content-Type': 'text/plain' },
          body: 'Could not fetch the notes.'
        }));
    });
};

module.exports.delete = (event, context, callback) => {
  context.callbackWaitsForEmptyEventLoop = false;

  connectToDatabase()
    .then(() => {
      Note.findByIdAndRemove(event.pathParameters.id)
        .then(note => callback(null, {
          statusCode: 200,
          body: JSON.stringify({ message: 'Removed note with id: ' + note._id, note: note })
        }))
        .catch(err => callback(null, {
          statusCode: err.statusCode || 500,
          headers: { 'Content-Type': 'text/plain' },
          body: 'Could not fetch the notes.'
        }));
    });
}; 
```

对于以前编写过`nodejs`函数的人来说，这应该很容易理解。这里没有特别的逻辑；我们定义了前面提到的五个功能，并为每个功能添加了逻辑。

## 数据库连接设置

这里涉及到两个步骤，第一步是创建一个动态连接，第二步是有办法重用相同的连接(如果可用的话)。听起来很棘手，但我会带你走一遍。您要做的是在您的`handler.js`文件旁边添加另一个文件——姑且称之为`database.js`，然后粘贴以下代码:

```
const mongoose = require('mongoose');
mongoose.Promise = global.Promise;
let isConnected;

module.exports = connectToDatabase = () => {
  if (isConnected) {
    console.log('=> using existing database connection');
    return Promise.resolve();
  }

  console.log('=> using new database connection');
  return mongoose.connect(process.env.DB)
    .then(db => { 
      isConnected = db.connections[0].readyState;
    });
}; 
```

***注*** *:此语法对`Mongoose 5.0.0-rc0`及以上有效。它将不会与低于 5 的任何版本的猫鼬一起工作。*

我们基本上是缓存数据库连接，确保它不会被创建，如果它已经存在。那样的话，我们就马上解决承诺。

创建了`databse.js`文件后，让我们在`handler.js`中要求它。只需将这段代码添加到处理程序的顶部。

```
// top of handler.js
const connectToDatabase = require('./db'); 
```

## 创建注释模型

我们之前已经在`handler.js`文件中引用了笔记模型，所以我们也必须创建它。我们将在根目录下创建一个名为`models`的文件夹，并在其中创建一个`Note.js`文件。将下面的代码粘贴到那里。

```
const mongoose = require('mongoose');
const NoteSchema = new mongoose.Schema({  
  title: String,
  description: String
});
module.exports = mongoose.model('Note', NoteSchema); 
```

现在我们必须在我们的`handler.js`文件中导出模型，我们通过在文件的顶部添加下面的代码来完成。

```
// top of handler.js
const connectToDatabase = require('./db');
const Note = require('./models/Note'); 
```

## 为环境变量创建 dotenv

用`dotenv`将配置文件和密钥放在一个完全独立的文件中非常容易，这是一个真正的生活保护程序。您可以将文件添加到`.gitignore`中，并确保您不会冒险泄露任何密钥。

您可以通过创建一个名为`variable.env`的新文件并将其放在根目录中来实现这一点。

```
DB=mongodb://<user>:<password>@mongodb.net:27017/db 
```

要获得您需要的信息，您必须返回 Atlas 并查看集群页面。单击“连接”按钮，然后点击“连接您的应用程序”按钮获取 URL。

[![Create your first Serverless API](img/8a11fcd57016bdebcec7e7a60b34f075.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M4yn4TK0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_kKS4lPXQxcW9JC4HnreM4Q.png)

按下“连接应用程序”后，系统会提示您“复制连接字符串”。按“我用的是 3.4 或更早的驱动”，你终于可以复制网址了。哇，这是一个令人厌倦的旅程。

[![Create your first Serverless API](img/1be29dafed7762eee6cebcc9d0e25151.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eZ1Rg4bi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_BWheGowWjS9eV3D9ayDPkA.png)

一旦你复制了它，回到`variables.env`文件并添加实际的连接 URL。

```
DB=mongodb://dbadmin:reallystrongpassword@cluster0-shard-00-00-e9ai4.mongodb.net:27017,cluster0-shard-00-01-e9ai4.mongodb.net:27017,cluster0-shard-00-02-e9ai4.mongodb.net:27017/test?ssl=true&replicaSet=Cluster0-shard-0&authSource=admin 
```

只需将密码和 url 替换为您在 Atlas 中使用的任何内容，就大功告成了。

***注意*** *:别忘了把`variables.env`加到`.gitignore`上！*

最后，在开始测试之前，我们需要使用`dotenv`模块并指向保存环境变量的文件。将这个片段添加到您的`handler.js`文件的顶部。

```
require('dotenv').config({ path: './variables.env' }); 
```

## 运行 API

此时，我们应该准备好测试我们的 API，为此我们需要运行`serverless-offline`模块，因为我们使用的是 Mongoose 模型定义，所以在运行时需要触发一个标志。

```
$ serverless offline start --skipCacheInvalidation 
```

一旦您在终端中运行了该命令，您应该会看到类似这样的内容。

[![Create your first Serverless API](img/85440c24169216c8414fe53bc05f3228.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UJ4VKTrL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/1_44QsPUia_wFWi7IYWHRVSA--1-.png)

要实际添加新的便笺，您可以使用[邮递员](https://www.getpostman.com/)向`POST`以下端点发送请求:

```
http://localhost:3000/notes 
```

正文包含标题和描述:

```
{
    "title": "some title"
    "description: "some description"
} 
```

响应应该是这样的。

```
{
    "_id": "48hfs9da3f9jfdhs8g"
    "title": "some title"
    "description: "some description"
    "__v":0
} 
```

## 部署

好了，我们已经看到了 API 在本地与`serverless-offline`一起工作，并对路线做了一些改动。现在让我们将它部署到 AWS Lambda 上，看看它是如何工作的。您只需运行以下命令:

```
serverless deploy 
```

这将提供所有的 AWS 资源，并将代码上传到 S3，Lambda 将从那里获取代码。这需要几秒钟的时间，但是您应该会看到这样的内容:

[![Create your first Serverless API](img/49821446b12b91b4eb65c1f2b52c0730.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NRr6cUKz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/06/Image001.png)

一旦完成，您将能够通过使用日志中写的端点来访问请求。如果你想弄乱 API，你可以使用`serverless deploy`重新部署

你必须理解 AWS Lambda 是如何工作的，并且熟悉它的限制，以便创建性能良好的函数。我建议在你开始之前做一点功课。

## 最后的想法

我怀疑有多少人会花时间完成所有这些步骤，但我想祝贺所有完成的人。创建一个 API 是一件严肃的事情，需要一些时间来做好，但是它是任何成功的应用程序的重要组成部分。

虽然本教程围绕着无服务器平台，但我想指出还有其他选择。作为无服务器的替代方案，我会研究一下 [Zappa](https://www.zappa.io/) ，他们是无服务器领域的新成员，但是项目背后的人正在用 Python 做一些非常酷的事情。

此外，AWS Lambda 不是你唯一的选择，微软 Azure 和 T2 谷歌云功能也是不错的选择。在草率行事之前，我会做的一件事是仔细看看我的项目需要什么，并根据云提供商提供的其他服务做出决定。

* * *

我想对帮助我创建这个教程的 Adnan Rahic 表示感谢。他写了很多与无服务器相关的很酷的东西，我不能向任何认真研究无服务器开发的人推荐他。他在过去的两个月里花了大部分时间写了这本关于节点监控的惊人的书，我建议你[去看看](https://sematext.com/resources/nodejs-monitoring-ebook/?utm_campaign=Node.js&utm_source=dev-to-ebook)。

在 [Unsplash](https://unsplash.com/search/photos/command?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 获得了这张很酷的特色图片。

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。