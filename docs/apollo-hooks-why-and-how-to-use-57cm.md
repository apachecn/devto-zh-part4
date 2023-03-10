# 阿波罗钩子:为什么和如何使用

> 原文：<https://dev.to/jkettmann/apollo-hooks-why-and-how-to-use-57cm>

*本帖最初发表于[jkettmann.com](https://jkettmann.com/apollo-hooks-why-and-how-to-use/)T3】*

阿波罗的`Query`和`Mutation`组件很好用。但是因为它们使用渲染属性模式，所以经常会降低可读性。当您需要嵌套它们时尤其如此，例如当一个组件既需要查询数据又需要改变数据时。

另一个问题是，您通常最终会得到一个负责获取数据的容器组件，该组件包装了一个包含业务逻辑的组件。一个例子是当你想使用一个依赖于获取数据的钩子，如下所示。

```
<Query query={SOME_QUERY}>
  {({ data }) => {
    const transformedData = useMemo(() => transform(data));

    return <div>...</div>;
  }}
</Query> 
```

Enter fullscreen mode Exit fullscreen mode

在这里使用钩子是不可能的，所以我们需要提取内部组件。现在我们有了一个只呈现`Query`的组件和一个呈现来自查询的数据的组件。这摧毁了 GraphQL 和 Apollo 的一个巨大优势:定义数据呈现之后的数据需求。

但最后，我们有一个更好的方法来解决这类问题。随着 Apollo 版本的新发布，支持 3 个钩子！这是向前迈出的一大步。阿波罗团队显然也很兴奋，因为他们用钩子重写了文档。

因为我第一次使用它们时遇到了一些小问题，所以我想给其他人提供一个关于如何移植到 Apollo hooks 的小指南。

当然，最好能看到他们的行动。所以让我们从一个简单的 React 应用程序开始，它包含一个`Query`和一个`Mutation`组件。

首先，我们简单地使用`apollo-boost`初始化 Apollo。

```
import React from "react";
import ReactDOM from "react-dom";
import ApolloClient from "apollo-boost";
import { ApolloProvider } from "react-apollo";
import "./index.css";
import App from "./App";

const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
});

ReactDOM.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们定义 App 组件。它包含一个请求书籍列表的`BOOKS_QUERY`和一个向列表添加书籍的`ADD_BOOK_MUTATION`。

然后这些书被呈现。当点击一个按钮时，突变被调用。它将把斯蒂芬·金斯的《闪灵》加入书单，并重新抓取`BOOKS_QUERY`。

```
import React from "react";
import { Query, Mutation } from "react-apollo";
import gql from "graphql-tag";

const BOOKS_QUERY = gql`
  query books {
    books {
      id
      title
      author
    }
  }
`;

const ADD_BOOK_MUTATION = gql`
  mutation addBook($title: String!, $author: String!) {
    addBook(title: $title, author: $author) {
      id
      title
      author
    }
  }
`;

function App() {
  return (
    <Query query={BOOKS_QUERY}>
      {({ loading, error, data }) => {
        if (loading) return <div>Loading</div>;
        if (error) return <div>Error: {JSON.stringify(error)}</div>; 
        return (
          <div>
            {data.books.map(({ id, title, author }) => (
              <div key={id}>
                "{title}" by "{author}"
              </div>
            ))}

            <Mutation
              mutation={ADD_BOOK_MUTATION}
              variables={{
                title: 'The Shining',
                author: 'Steven King'
              }}
              refetchQueries={[{ query: BOOKS_QUERY }]}
            >
              {addBook => <button onClick={addBook}>Add book</button>}
            </Mutation>
          </div>
        );
      }}
    </Query>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

这看起来不是很漂亮，是吗？例如，我们在按钮组件中有 11 个缩进。当然，我们可以提取更小的成分。同时，感觉组件不应该如此复杂。

所以，让我们看看当我们迁移到 Apollo hooks 时会是什么样子。

阿波罗 3 版引入了三个包来分离高阶组件(`@apollo/react-hoc`)、渲染道具组件(`@apollo/react-components`)和钩子(`@apollo/react-hooks`)。这允许我们有更小的束尺寸。钩子包是最小的，因为其他包都依赖于它。

最初的`react-apollo`作为一个伞状包，允许我们并行使用所有的模式。

作为迁移的第一步，我们需要安装新的依赖项。我们将模拟一个逐步迁移到钩子的过程，就像您对一个更大的真实应用程序所做的那样。这意味着我们将只在第一步中用`useQuery`钩子替换`Query`组件，并且仍然并行使用旧的`Mutation`组件。因此我们也需要升级`react-apollo`包。

```
npm i @apollo/react-hooks react-apollo@3 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以用`useQuery`钩子替换`Query`组件。这样，我们可以在返回 JSX 之前将所有查询逻辑上移。

```
import React from 'react';
import { Mutation } from 'react-apollo';
import { useQuery } from '@apollo/react-hooks';
import gql from 'graphql-tag';

const BOOKS_QUERY = ...;
const ADD_BOOK_MUTATION = ...;

function App() {
  const { loading, error, data } = useQuery(BOOKS_QUERY);

  if (loading) return <div>Loading</div>;
  if (error) return <div>Error: {JSON.stringify(error)}</div>; 
  return (
    <div>
      {
        data.books.map(({ id, title, author }) => (
          <div key={id}>
            "{title}" by "{author}"
          </div>
        ))
      }

      <Mutation
        mutation={ADD_BOOK_MUTATION}
        variables={{
          title: 'The Shining',
          author: 'Steven King',
        }}
        refetchQueries={[{ query: BOOKS_QUERY }]}
      >
      {
        (addBook) => (
          <button onClick={addBook}>
            Add book
          </button>
        )
      }
      </Mutation>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

这看起来已经好多了。我们不需要改变太多，但我们已经摆脱了四个缺口。此外，嵌套在 JSX 代码中的丑陋的条件句也不见了。可读性大提升！好消息是:即使我们只是部分迁移到 hooks，这个应用程序仍然可以工作。

现在我们也可以用`useMutation`钩子代替`Mutation`组件。

```
import React from 'react';
import { useQuery, useMutation } from '@apollo/react-hooks';
import gql from 'graphql-tag';

const BOOKS_QUERY = ...;
const ADD_BOOK_MUTATION = ...;

function App() {
  const { loading, error, data } = useQuery(BOOKS_QUERY);
  const [addBook] = useMutation(ADD_BOOK_MUTATION, {
    variables: {
      title: 'The Shining',
      author: 'Steven King',
    },
    refetchQueries: [{ query: BOOKS_QUERY }],
  });

  if (loading) return <div>Loading</div>;
  if (error) return <div>Error: {JSON.stringify(error)}</div>; 
  return (
    <div>
      {
        data.books.map(({ id, title, author }) => (
          <div key={id}>
            "{title}" by "{author}"
          </div>
        ))
      }

      <button onClick={addBook}>
        Add book
      </button>
    </div>
  );
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

这看起来真干净！我们有一个组件，看起来简单，但实际上做了很多。它从服务器获取数据，呈现数据，并且能够改变数据。

我最喜欢的是组件中关注点的清晰分离。在组件的上部，我们处理数据。接下来是加载和错误状态的条件呈现。最后，我们渲染实际的组件。

最后但同样重要的是，我们还可以通过从依赖项中移除`react-apollo`包来提高包的大小。现在我们只需要从入口文件的钩子包中导入`ApolloProvider`。

```
import React from "react";
import ReactDOM from "react-dom";
import ApolloClient from "apollo-boost";
import { ApolloProvider } from "@apollo/react-hooks";
import "./index.css";
import App from "./App";

const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
});

ReactDOM.render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>,
  document.getElementById("root")
); 
```

Enter fullscreen mode Exit fullscreen mode

如果你喜欢这篇文章，我很乐意在[我的简讯](https://jkettmann.com/subscribe/)或[推特](https://twitter.com/j_kettmann)上看到你。