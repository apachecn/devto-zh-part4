# GraphQL 是新的 REST —第 1 部分

> 原文：<https://dev.to/thisdotmedia/graphql-is-the-new-rest-part-1-2aj6>

GraphQL 是一种新的 API 标准，为构建数据驱动的应用程序提供了一种革命性的方法。该项目最初是由脸书创建的，当时他们正在将其移动应用从 HTML5 转移到原生移动应用。

直到 2015 年，GraphQL 才作为开源项目向公众开放。

GraphQL 的主要特性和优势促使您重新思考构建客户端应用程序的方式，以及它们与后端服务器通信以查询或改变数据的方式。例如，GraphQL 是:

*   一种功能强大的查询语言，用于在客户端浏览器和服务器之间传递数据。

*   应用程序级查询语言，而不是数据库查询语言。

*   与平台无关，无论是在服务器端还是客户端，GraphQL 都可以无缝地嵌入许多编程语言(C#、Go、Java、Python、Node.js 等)。).

*   与数据库无关，通过 GraphQL 提供所需的挂钩，使您能够将 GraphQL 连接到任何选择的数据库。

*   专用于声明性数据获取方法。在 GraphQL 查询中，您可以准确地定义要查询的数据或字段，以及要发送的输入过滤器。您可以根据需要从对象和子对象组成查询。

*   RESTful 的替代方案，更灵活地管理应用程序中的数据。

## RESTful 方法 vs GraphQL 方法

比方说，在我们的应用程序中，我们正在跟踪关于书籍和作者的数据。使用 RESTful，您将定义一个 REST API 并公开多个客户端应用程序可以与之通信的端点。

例如，您可以定义以下 API 端点:

*   /my-domain/Books —获取所有书籍

*   /my-domain/Books/1 —获取 ID = 1 的图书

*   /my-domain/Authors-获取所有作者

*   /my-domain/Authors/1-获取 ID = 1 的作者

这个循环从请求图书馆中的所有书籍(/Books)开始。对于一本书，您会收到作者 ID。为了获得作者的详细信息，您向另一个端点(/Authors/1)上的 REST API 发出一个新的请求来获取作者。对于作者，您会收到由该作者编写的图书 id 列表。为了获得某本书的详细信息，您向另一个端点(/Books/1)上的 REST API 发出一个新的请求。

使用 REStful，应用程序与服务器保持持续的通信模式，以查询和遍历数据。

GraphQL 是您的可取之处。客户机和服务器之间的上述通信可以用一个 GraphQL 查询来总结。

```
query {
    books {
        id
        name
        genre
        author {
            id
            name
            age
            books {
                id
                name
            }
        }
    }
} 
```

GraphQL 标准允许您以最有效和最简洁的方式构建相关数据的图表。

通过一个查询，您可以检索关于书籍、每本书的作者以及该作者创作的所有书籍的信息。

在这一系列文章(第 1 部分和第 2 部分)中，我们将一起构建一个 GraphQL 服务器 API 和一个 GraphQL 客户端应用程序，以一种 graph QL 的方式进行通信。

## 以身作则

学习 GraphQL 语法和概念的最好方法是构建自己的 GraphQL 服务器 API 和客户端应用程序。我将带您了解在 Node.js 和 Express engine 之上构建 GraphQL 服务器 API 的过程。同时，我将使用 Angular 7 构建一个客户端应用程序，以利用 GraphQL 服务器 API 并执行 CRUD 操作来管理书籍和作者。

在本文的第 1 部分，我们将构建 GraphQL 服务器 API。在第 2 部分中，我们将构建 GraphQL Angular 客户端应用程序。

你可以在 Github 上找到这篇文章的源代码，点击这个链接 [GraphQL CRUD](https://github.com/thisdot/graphql-crud) repo。

## 构建 GraphQL 服务器 API

GraphQL API 服务器将是一个 Node.js 应用程序。按照以下步骤启动并运行您的 GraphQL 服务器。

## 为应用程序创建包装文件夹

首先创建一个名为 **graphql-crud** 的新文件夹。这个文件夹将保存客户机和服务器的源代码。

## 为 Node.js 应用程序添加主 JavaScript 文件

在 graphql-crud 文件夹中添加一个名为 **server** 的新文件夹。在这个新文件夹中，创建一个名为 **app.js** 的新 JS 文件。这个文件是启动整个服务器端应用程序的主 Node.js 文件。

现在让我们添加一些构建服务器时需要的 NPM 包。发出以下命令:

```
npm install express express-graphql graphql mongoose cors — save 
```

**[express](https://expressjs.com/)** :启用 Node.js app 监听请求和服务器响应。

**[express-graphql](https://graphql.org/graphql-js/running-an-express-graphql-server/)** :使 Node.js app 能够理解并处理 graphql 请求/响应。

:graph QL 的 JavaScript 参考实现。

**[mongose](https://mongoosejs.com/)**:在 Node.js app 内部使用 MongoDB 的 ORM。我们将使用免费的在线服务 [mLab](https://mlab.com/) 将我们的数据存储在一个 MongoDB 实例中。

**[cors](https://github.com/expressjs/cors)**:node . js 包，用于提供一个连接/快捷中间件，可用于启用具有各种选项的 CORS。

## 要求将需要的库放入 app.js 文件中

在 app.js 文件的开头需要以下库:

```
const express = require('express');

// Import GraphQL middleware
const expressGraphQL = require('express-graphql');

// Import Mongo Db Client API
const mongoose = require('mongoose');

// Import CORs middleware to allow connections from another URL:PORT
const cors = require('cors'); 
```

## 创建 App 实例并连接到 MongoDB 实例

创建 Node.js 应用程序实例:

```
const app = express(); 
```

要在 Node.js 应用程序实例上启用 CORS:

```
app.use(cors()); 
```

要连接到 MongoDB 实例:

```
mongoose.connect(‘{CONNETION_STRING}’); 
```

想知道如何获得 MongoDB 实例的连接字符串吗？如果您正在阅读这篇文章并使用 mLab 在线服务，我建议您在 [mLab](https://mlab.com/signup/) 上为自己创建一个帐户，然后创建一个新的 MongoDB 实例。不要忘记为新创建的数据库实例创建一个数据库用户和密码。一旦创建了用户名和密码，mLab 就会为您提供一个连接字符串，您可以将它放入上面的代码行中。

最后，配置 Node.js 应用程序在端口 4000 上监听传入的请求。

```
app.listen(4000, () => {
    console.log('Listening for requests on port 4000');
}); 
```

## 添加图形 QL 中间件

Express 允许外部 API 或包通过一个[中间件](https://expressjs.com/en/guide/using-middleware.html)与请求/响应模型挂钩。要将所有来自 **/graphql** 的请求重定向到 graphql 中间件，使用以下代码配置 Node.js 应用程序:(将代码添加到 **app.listen()** )
的正上方

```
app.use('/graphql', expressGraphQL({
    schema,
    graphiql: true
})); 
```

任何对 **/graphql** 的请求现在都由 **expressGraphQL 中间件处理。**这个中间件需要将 GraphQL 服务器 API 使用的模式作为参数传递。我们将在下面定义这个模式。**[GraphQL:true](https://github.com/graphql/graphiql)**选项支持浏览器内 IDE 针对 graph QL 服务器运行您的查询和变更。在将实际的客户端应用程序连接到服务器之前，测试您的 GraphQL 服务器 API 非常有用。

## GraphQL 模式

GraphQL 的灵活性取决于 API 提供的灵活性，即哪些对象类型可以被查询或改变，哪些属于对象类型的字段可以由 API 返回并由客户端使用。这完全取决于您使用 GraphQL 服务器构建和配置的模式和对象类型。最终，GraphQL 将根据 API 开发人员根据特定模式提供的一组规则来验证查询或变异。这就是 GraphQL 的真正强大之处。

GraphQL 模式或多或少类似于 RESTful API 路由树。定义模式意味着:

*   您告诉 GraphQL 服务器预期的对象类型，无论是在查询/变异的主体中，还是在这些查询/变异生成的响应中。

*   您构建了 GraphQL 服务器向客户端应用程序公开的有效“端点”。

## 建立一个 GraphQL 模式

在名为 **schema** 的新文件夹中创建一个新的 JavaScript 文件 **schema.js** 。在文件顶部导入 GraphQL 库，如下所示:

```
const graphQL = require(‘graphql’); 
```

接下来，导入一些 GraphQL 类型，以便稍后在整个代码中使用:

```
const {
  GraphQLObjectType,
  GraphQLString,
  GraphQLSchema,
  GraphQLID,
  GraphQLInt,
  GraphQLList,
  GraphQLNonNull
} = graphQL; 
```

我们使用 [ES6 析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)特性从 graphql-js 包中提取类型。我们将使用这些类型来定义下面的模式。

模式由**查询**和**变异**定义。

查询是一个根端点，向客户端应用程序公开所有可用的查询 API 端点。

突变是一个根端点，它向客户端应用程序展示所有可用的突变(更新、创建或删除)API 端点。

```
module.exports = new GraphQLSchema({
  query: RootQuery,
  mutation: Mutation
}); 
```

我们将**根查询**定义如下:

```
const RootQuery = new GraphQLObjectType({
  name: 'RootQueryType',
  fields: {
    book: {
      // book() {} endpoint
      type: BookType,
      args: {
        id: {
          type: GraphQLID
        }
      },
      resolve(parent, args) {
        // mongoose api call to return a book by ID
      }
    },
    author: {
      // author() {} endpoint
      type: AuthorType,
      args: {
        id: {
          type: GraphQLID
        }
      },
      resolve(parent, args) {
        // mongoose api call to return the book author
      }
    },
    books: {
      // books {} endpoint
      type: new GraphQLList(BookType),
      resolve(parent, args) {
        // mongoose api call to return all books
      }
    },
    authors: {
      // authors {} endpoint
      type: new GraphQLList(AuthorType),
      resolve(parent, args) {
        // mongoose api call to return authors
      }
    }
  }
}); 
```

RootQuery 对象的类型为 **GraphQLObjectType** 。它有一个名为**的 RootQueryType** 和一个字段数组。字段数组实际上是客户端应用程序可以用来查询 GraphQL 服务器 API 的端点数组。

让我们探索其中一个端点。剩下的就差不多了。

```
book: {
      // book() {} endpoint
      type: BookType,
      args: {
        id: {
          type: GraphQLID
        }
      },
      resolve(parent, args) {
        // mongoose api call to return a book by ID
      }
    } 
```

要定义端点，请提供以下字段:

**type** :该端点返回的数据。在本例中，它是**图书类型**。我们将很快回到这种类型。

**args** :该端点接受的参数，用于相应地过滤数据并返回响应。在这种情况下，代码定义了一个名为 **id** 的参数，其类型为 **GraphQLID。**这是 GraphQL 提供的一种特殊类型，用来表示这个字段是对象的 ID 而不是普通字段。

**resolve** :每当执行**图书**查询端点时，GraphQL 都会调用这个函数。该函数应该由开发人员实现，以便根据传递给该查询的 id 参数返回图书类型。稍后，当我们连接到 MongoDB 实例以根据其 id 检索一本书时，我们将填补这些空白。resolve()函数接收**父**对象(如果有的话)和打包在 **args** 输入参数下的参数列表作为参数。因此，要访问传递给这个查询的图书 id，可以通过 **args.id** 来完成。

这就是 GraphQL 的强大之处。它允许作者使用 resolve()函数形式的指令构建对象图，告诉 GraphQL 如何通过执行 resolve()函数来查询子对象，等等。

图书类型定义如下:

```
const BookType = new GraphQLObjectType({
  name: 'Book',
  fields: () => ({
    // Fields exposed via query
    id: {
      type: GraphQLID
    },
    name: {
      type: GraphQLString
    },
    genre: {
      type: GraphQLString
    },
    author: {
      // How to retrieve Author on a Book object
      type: AuthorType,
      /**
       * parent is the book object retrieved from the query below
       */
      resolve(parent, args) {
        // mongoose api call to return the book author by authorId
      }
    }
  })
}); 
```

使用 **GraphQLObjectType** 定义一个对象类型。在我们的例子中，通过提供名称 **Book** 来定义类型。此外，在 **fields()** 函数中，返回一个包含图书类型上所有可用字段的对象。在对象类型上定义的字段是唯一可用于查询响应的字段。

本例中的图书类型定义了以下字段:

*   类型为 **GraphQLID** 的 id 字段

*   类型为 **GraphQLString** 的名称字段

*   类型为 **GraphQLString** 的流派字段

*   类型为 **AuthorType** 的作者字段。 **AuthorType** 是另一个我们自己定义的自定义对象类型。这个字段很特殊，因为它定义了一个 **resolve()** 函数。这个函数接收两个输入参数:parent 和 args。父参数是 GraphQL 查询的实际图书对象。当 GraphQL 想要返回一个自定义对象类型的字段时，它将执行 resolve()函数来返回这个对象。稍后，我们将添加 mongoose API 调用，通过 Book.authorId 字段从数据库中检索作者。这就是 GraphQL 的美妙之处，它让 API 的开发人员能够定义不同的路径，GraphQL 可以使用这些路径来遍历对象模型，并为每个查询返回一个响应，无论查询有多复杂。

让我们定义模式使用的变异对象。

```
const Mutation = new GraphQLObjectType({
  name: 'Mutation',
  fields: {
    addBook: {
      // addBook() {} endpoint
      type: BookType,
      args: {
        name: {
          type: new GraphQLNonNull(GraphQLString)
        },
        genre: {
          type: new GraphQLNonNull(GraphQLString)
        },
        authorId: {
          type: new GraphQLNonNull(GraphQLID)
        }
      },
      resolve(parent, args) {
        // mongoose api call to insert a new book object and return it as a reponse
      }
    },
  }
}); 
```

类似于 **RootQuery** 对象，**变异**对象定义了 API 端点，客户端应用程序可以使用这些端点来变异或更改数据。我们将在这里讨论其中一个突变，其余的可以在 Github repo 上找到。

代码片段定义了 **addBook** 端点。此端点返回 BookType 类型的数据。它接收**名字**、**流派**和 **authorId** 作为输入参数。注意我们如何通过使用 **GraphQLNonNull** 对象来强制输入参数。此外，这个突变定义了 **resolve()** 函数。当这个端点被调用时，这个函数由 GraphQL 执行。resolve()函数应该包含向 MongoDB 插入或添加新的 Book 对象的代码。

现在已经定义了 GraphQL，让我们探索一个如何使用 mongoose 客户端 API 连接到 MongoDB 的示例。

## 通过 mongoose API 连接到 MongoDB

让我们从定义 mongoose 将连接到的数据库的模式开始。为此，创建一个名为 **models** 的新文件夹。然后，对于关系数据库术语中的每个**集合**或表，添加一个 JavaScript 文件并定义 mongoose 模型对象。

现在，用下面的代码添加 **book.js** 文件:

```
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
const bookSchema = new Schema({
   /**
   * No need to add "id" column
   * It is being created by mLab as "_id"
   */
   name: String,
   genre: String,
   authorId: String
});
module.exports = mongoose.model('Book', bookSchema); 
```

这是在 MongoDB 中定义集合的 mongoose 方式。这个集合将在 MongoDB 数据库中创建，当它第一次连接到数据库实例并且在那里没有找到集合时。

在我们的例子中，图书收藏定义了**名称**、**流派、**和 **authorId** 字段。

让我们将这个集合导入到我们的 **schema.js** 文件中，并连接到 MongoDB。其余的收藏可以在 Github repo 上找到。

首先要求或导入图书收藏，如下所示:

```
const Book = require(‘../models/book’); 
```

现在让我们重新访问一下 **book** 查询端点，并实现它的 resolve()函数，从数据库中返回一本实际的书:

```
book: {
      // book() {} endpoint
      type: BookType,
      args: {
        id: {
          type: GraphQLID
        }
      },
      resolve(parent, args) {
        return Book.findById(args.id);
      }
    } 
```

只需在图书模型上调用 **findById()** 函数，就可以根据 Id 字段检索图书对象。

其余的 mongoose API 调用可以在 Github repo 上获得。

既然 GraphQL 模式已经准备好了，并且我们已经用这个模式配置了 Express-GraphQL 中间件，现在是时候了！

## 演示图形 IDE

通过发出以下命令运行 server 应用程序:

```
node app.js 
```

服务器现在启动并运行在端口 4000 上。让我们打开浏览器，访问这个 URL:[http://localhost:4000/graphiql](http://localhost:4000/graphiql)

[![](img/a5bed67c37475d29b59339868c87dc55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mf1rgKTf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2082/0%2ARYLdzNJagLrc2TPL)

在左边你可以输入你的查询或突变。

中间是 **results** 面板，显示查询或变异的结果。

在右侧，您会发现**文档浏览器。**您可以使用它来诊断 GraphQL 服务器 API 的文档。在这里，您可以看到可用的查询和变异，以及它们的详细信息，以及如何构建查询和哪些字段对您可用。

让我们通过键入以下 GraphQL 查询来添加一本书。查看 GraphQL 网站，获得关于如何构建[查询和变异](https://graphql.org/learn/queries/)的完整参考。

```
mutation addBook($name: String!, $genre: String!, $authorId: ID!) {
    addBook(name: $name, genre: $genre, authorId: $authorId) {
        id
        name
    } 
} 
```

在左侧面板的**查询变量**部分，添加以下变量:

```
{
  "name":"Learning GraphQL",
  "genre": "APIs", 
  "authorId": "cjktxh5i50w53yul6lp4n"
} 
```

上面的变异创建了一个具有特定名称、流派和 authorId 的新图书记录，并返回新创建图书的 Id 和名称。

上述突变产生的反应如下:

```
{
  id: ecjkv56ab219vireudxw
  name: “Learning GraphQL”
} 
```

要查询这本书，可以发出以下命令:

```
query {
    book (id: “ecjkv56ab219vireudxw”) {
        Name
        Genre
    }
} 
```

现在，GraphQL 服务器 API 已经启动并运行。在本系列的第 2 部分中，让我们换个方式构建 GraphQL Angular 客户端应用程序。

## 结论

GraphQL 被认为是最近才加入的技术。凭借其迅猛的发展势头，看起来它将在应用程序中访问和操作数据方面取代 REStful。

在 GraphQL 系列的第一部分中，我们在 Node.js Express 的基础上构建了一个 GraphQL 服务器 API。在本系列的第 2 部分，我们将使用 Angular 构建一个 GraphQL 客户端应用程序。

敬请期待！

*这篇文章是由[比拉勒·海达尔](https://www.twitter.com/bhaidar)写的，他是[这个圆点](http://thisdot.co/labs)的导师。*

需要 JavaScript 咨询、指导或培训帮助吗？在 [This Dot Labs](//thisdot.co) 查看我们的服务列表。