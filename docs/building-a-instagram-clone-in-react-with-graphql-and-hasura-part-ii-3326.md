# 用 GraphQL 和 Hasura 构建 Instagram 克隆-第二部分

> 原文：<https://dev.to/hasurahq/building-a-instagram-clone-in-react-with-graphql-and-hasura-part-ii-3326>

[![](img/53772afd2fe7eecaee7b1efb5d347cf8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PGqr5bA8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.hasura.io/conteimg/2019/09/Instagram-clone-1.png)

*本教程由 [Abhijeet Singh](https://github.com/abhi40308) 撰写，并作为 [Hasura 技术作者计划](https://blog.hasura.io/the-hasura-technical-writer-program/)的一部分发布，该计划旨在支持为开源 Hasura GraphQL 引擎编写指南和教程的作者。*

在本系列的 **[第一部分](https://blog.hasura.io/instagram-clone-react-graphql-hasura-part1/)** 中，我们设置了后端和 Auth0。在这一部分，我们将设置 React 应用程序并将其连接到我们的后端。

# React App 设置

我们将首先实现用户认证。我们将使用 JWT (JSON web 令牌)进行身份验证。让我们首先在 react 应用程序中创建一些基本的标题来显示登录按钮。

用[这个](https://github.com/abhi40308/instagram-clone/blob/master/src/styles/App.css)文件替换`styles/App.css`文件的内容。这些风格将在我们的整个应用程序中使用，所以你不必担心造型。同样下载[这个](https://github.com/abhi40308/instagram-clone/blob/master/src/styles/sprite.png)文件并把它放在你的`styles/`目录下。我们将使用它来显示我们的应用程序中的各种按钮。

### 设置 Apollo GraphQL 客户端

替换`App.js`的内容以使用 Apollo GraphQL 客户端，如下所示。([查看 apollo github 知识库获取更多帮助](https://github.com/apollographql/react-apollo))