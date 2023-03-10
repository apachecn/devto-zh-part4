# 在 Apollo 服务器中编写自己的 GraphQL 指令

> 原文：<https://dev.to/wanzulfikri/learn-how-to-write-your-own-graphql-directive-in-apollo-server-3he>

你以前见过这样的模式吗:

```
type User {
  id: ID! @unique
  stories: [Story!]!
}

type Story {
  id: ID! @unique
  text: String!
} 
```

在 GraphQL 中，`@`用来表示一个*指令*。下面是[规范](https://graphql.github.io/graphql-spec/draft/#sec-Language.Directives)给出的简单描述:

> 指令提供了一种在 GraphQL 文档中描述可选运行时执行和类型验证行为的方式。

与解析器类似，GraphQL 规范没有指定
指令实现，而是由开发人员来处理。在上面的例子中，`@unique`用来告诉服务器生成一个惟一的 ID；ID 生成的实现是由服务器本身完成的。

如果我们将`@unique`放入`text`字段，我们将期望每个故事都有一个唯一的文本:

```
type User {
  id: ID! @unique
  stories: [Story!]!
}

type Story {
  id: ID! @unique
  text: String! @unique
} 
```

还有很多与指令相关的细节，如果你有兴趣了解它们，你可以从阅读以下章节的规范开始: [2.12](https://graphql.github.io/graphql-spec/June2018/#sec-Language.Directives) 、 [3.13](https://graphql.github.io/graphql-spec/June2018/#sec-Type-System.Directives) 和 [5.7](https://graphql.github.io/graphql-spec/June2018/#sec-Validation.Directives) 。

在这篇文章中，我将向您展示如何在 Apollo Server 中实现一个简单的指令，它将获取我们的 GraphQL 查询的结果并对其进行加密，而不是走进一个兔子洞。

## 强制镗孔设置段

与大多数教程一样，为这个项目创建一个目录。切换到该目录后，使用以下命令运行`npm`:

1.  用`npm init -y`初始化项目
2.  用`npm install graphql-yoga bcryptjs`安装 [graphql-yoga](https://github.com/prisma/graphql-yoga/) 和`bcryptjs`。`graphql-yoga`基于`apollo-server`，你不需要复杂的设置就能让它工作

接下来，在我们的项目目录的根目录下创建一个`app.js`文件。然后，在我们的`package.json`中，在“脚本”部分下添加`"start": "node app.js"`。

在`app.js`中，添加以下样板代码来创建我们的服务器:

```
const { GraphQLServer } = require("graphql-yoga")

const typeDefs = `
  type Query {
    receiveMessage(message: String!): String!
  }
`

const resolvers = {
  Query: {
    receiveMessage: (_, { message }) => message,
  },
}

const server = new GraphQLServer({ typeDefs, resolvers })
server.start(() => console.log("Server is running on localhost:4000")) 
```

运行`npm start`，GraphQL 游乐场将在`http://localhost:4000`可用。

尝试操场上的`receiveMessage`查询以确保一切正常:

```
query {
    receiveMessage(message: "A cryptic message")
} 
```

如果你不确定你做得对，检查这个[回购](https://github.com/wanzulfikri/directives-tuts)已完成的项目。

现在无聊的东西已经结束了，让我们写指令。

## 定义模式中的指令

任何指令都应该包含在我们的模式中。在`apollo-server`中，也就是`graphql-yoga`所基于的，我们可以将你的模式定义传递给`typeDefs`。

这是我们当前的`typeDefs` :

```
// app.js

const typeDefs = `
  type Query {
    receiveMessage(message: String!): String!
  }
` 
```

要添加新的指令，我们可以添加`directive @name-of-directive on FIELD_DEFINITION`。您的`typeDefs`现在将看起来像这样:

```
directive @encrypt on FIELD_DEFINITION

type Query {
    receiveMessage(message: String!): String!
 } 
```

类似于我们用`type`来表示查询的类型，我们用`directive`来表示可用的指令。

指令不能应用于模式中的任何东西，除非我们指定它的目标。

GraphQL 模式不仅仅由字段组成，它还可以包含枚举、接口、联合等等。Apollo 通过`SchemaDirectiveVisitor`的子类公开模式的那些部分(类型系统定义或位置)(在这里阅读可用的子类[)。](https://www.apollographql.com/docs/apollo-server/features/creating-directives/#implementing-schema-directives)

在我们的例子中，我们通过编写`on FIELD_DEFINITION`来表明`@encrypt`将应用于模式字段。稍后，如果我们想让这个指令应用到不同的位置，比如参数，我们可以把它改成`on ARGUMENT_DEFINITION`。

## 编写指令实现

我们需要使用`graphql-tools`中的`SchemaDirectiveVisitor`(包含在`graphql-yoga`中)来完成这一部分。

在项目的根目录下创建一个名为`directives.js`的文件，并导入以下内容:

```
const { SchemaDirectiveVisitor } = require("graphql-tools")
const { defaultFieldResolver } = require("graphql")
const bcrypt = require("bcryptjs") 
```

我们需要`SchemaDirectiveVisitor`，因为我们制定的任何指令都应该是`SchemaDirectiveVisitor`的子类。

如果我们的字段没有自己的解析器，则需要使用。

`bcrypt`将用于加密我们从查询解析器收到的结果。

一旦你完成了，请做好准备，因为我将吐出代表我们的指令实现的代码块:

```
class EncryptDirective extends SchemaDirectiveVisitor {
  visitFieldDefinition(field) {
    const { resolve = defaultFieldResolver } = field
    field.resolve = async function(...args) {
      const result = await resolve.apply(this, args)
      if (typeof result === "string") {
        return bcrypt.hashSync(result, 8)
      }
      return result
    }
  }
} 
```

这是怎么回事？

首先，我们创建一个指令，它是`SchemaDirectiveVisitor`的子类。如果我们愿意，我们可以将子类名称改为其他名称。

其次，记住我们的指令只能应用于我们在模式中指出的位置。一旦我们做到了这一点，我们将不得不写当这个指令被应用到一个指定的位置时会做什么。

如果我们在模式中使用`on FIELD_DEFINITION`，那么我们的指令实现应该在`visitFieldDefinition`中。这个方法将接受`field`对象，你可以用它做很多事情。在我们的例子中，我们想要覆盖字段的解析器。

第三，我们将使用[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)从`field`中提取`resolve`函数。如果
没有`resolve`功能，则默认为`defaultFieldResolver`。

第四，我们将覆盖字段的`resolve`函数。我们做的第一件事是运行字段自己的解析器，这样我们可以存储初始结果。如果你不确定`.apply`是什么意思，MDN 有一个精彩的解释[在这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)。

第五，在加密结果并返回之前，我们将对字符串进行简单的类型检查。如果不是字符串，我们将返回正常消息。

一切完成后，一定要导出类:

```
module.exports = {
  EncryptDirective,
} 
```

### 向我们的服务器添加指令定义

在使用该指令之前，让我们先导入它:

`const { EncryptDirective } = require("./directives");`

接下来，在`app.js` :
中查看您的服务器配置

```
const server = new GraphQLServer({
  typeDefs,
  resolvers,
}) 
```

除了类型定义和解析器，我们的服务器还可以通过`schemaDirectives`选项配置一个指令。设置起来很简单:

```
const server = new GraphQLServer({
  typeDefs,
  resolvers,
  schemaDirectives: {
    encrypt: EncryptDirective,
  },
}) 
```

您可以将 EncryptDirective 链接到您想要的任何名称。如果您使用`makeItGibberish: EncryptDirective`，那么您可以通过`@makeItGibberish`调用该指令。只需确保您也更改了模式中的指令定义。

好了，在我们将指令应用到我们的模式之前，重置服务器，打开 GraphQL playground，并测试`receiveMessage`查询以确保它仍然工作。

如果一切正常，将您的`typeDefs`更改为以下内容:

```
directive @encrypt on FIELD_DEFINITION

type Query {
    receiveMessage(message: String!): String! @encrypt
 } 
```

重置服务器并再次测试查询。如果您收到加密的(也就是看似无意义的)字符串，那么恭喜您——您已经构建了自己的自定义 GraphQL 指令。

如果你在任何时候遇到困难，一定要检查这个已完成项目的[报告](https://github.com/wanzulfikri/directives-tuts)。

如果您有兴趣在 Apollo Server 中构建更多自己的指令，请务必查看 Apollo 文档中的这些例子。您还可以在 [graphql-community](https://github.com/graphql-community) repo 中找到开源社区构建的指令。

* * *

本帖原载于我的[网站](https://www.wanzulfikri.com/how-to-write-your-own-directives/)。

如果你想要更多关于 React + GraphQL 的文章，请注册[我的简讯](http://eepurl.com/gqRrwT)。

* * *

感谢您的阅读。我真的很感激。

如果您需要任何帮助，请随时联系我或留下评论；如果我做错了什么，也留下批评(这是很有可能的)。