# 如何用 QueryComponent 烘干 Apollo GraphQL

> 原文：<https://dev.to/fuksito/react-querycomponent-for-apollo-graphql-1p28>

官方的 Apollo React 指南举例说明了如何加载查询并以这种方式显示数据:

```
import gql from "graphql-tag";
import { Query } from "react-apollo";

const GET_DOGS = gql`
  {
    dogs {
      id
      breed
    }
  }
`;

const Dogs = () => (
  <Query query={GET_DOGS}>
    {({ loading, error, data }) => {
      if (loading) return "Loading...";
      if (error) return `Error! ${error.message}`;

      return (
        <select name="dog" onChange={onDogSelected}>
          {data.dogs.map(dog => (
            <option key={dog.id} value={dog.breed}>
              {dog.breed}
            </option>
          ))}
        </select>
      );
    }}
  </Query> ); 
```

这是一个很好的开始，但是我不想在每次使用 Query 时都复制所有的错误处理，我还想编写更多有趣的加载器，而不仅仅是文本，所以在所有地方都复制这些代码看起来像是样板文件。

所以我用 QueryComponent 来干燥它，所以上面的例子会变成:

```
class Dogs extends QueryComponent {
  query(){
    return `
      {
        dogs {
          id
          breed
        }
      }    
    `
  }

  content(){
    const data = this.state.data
    return (
      <select name="dog">
        {data.dogs.map(dog => (
          <option key={dog.id} value={dog.breed}>
            {dog.breed}
          </option>
        ))}
      </select>
    );    
  }
} 
```

现在我可以配置好的加载器，让它在整个站点上保持一致，并集中处理错误，我不需要每次都导入`gql`。

QueryCompoment 的代码应该是这样的:

```
import React from "react";
import PropTypes from "prop-types";
import gql from "graphql-tag";

import Loader from "./Loader";

class QueryComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { loading: true, error: null };
  }

  componentDidMount() {
    this.loadQuery({ variables: this.queryVariables() });
  }

  query() {
    throw "query() should be redefined on extended component";
  }

  queryVariables() {
    return {};
  }

  loadQuery({ variables }) {
    const client = this.context.client;
    const query = gql(this.query());

    try {
      client
        .watchQuery({ query, variables }) // performs query as well
        .subscribe(
          ({ data, loading, error, errors, networkStatus }) => {
            // window.console.log("query subscribe fired")
            if (loading) return;
            if (error) {
              this.queryFailed({ error, errors, networkStatus });
            } else {
              this.queryLoaded(data);
            }

            if (this.state.loading) {
              this.setState({ loading: false });
            }
          },
          error => {
            console.log("Query Failed");
            console.dir(error);
            this.queryFailed({ error });
          }
        );
    } catch (error) {
      console.log("query error");
      console.log(error);
      this.setState({
        loading: false,
        error
      });
    }
  }

  loading() {
    return <Loader />;
  }

  queryLoaded(data) {
    this.setState(data);
  }

  queryFailed({ error }) {
    this.setState({
      loading: false,
      error
    });
  }

  handleError(error) {
    const message = error.message;
    return <div>{message}</div>;
  }

  render() {
    const { loading, error } = this.state;
    if (loading) return this.loading();
    if (error) return this.handleError(error);
    return this.content();
  }
}

QueryComponent.contextTypes = {
  client: PropTypes.object
};

export default QueryComponent; 
```

为了清晰起见，我稍微简化了代码。

您可以在 CodeSandbox 中查看更高级的工作示例，该示例也传递了查询变量:

[https://codesandbox.io/embed/modest-keldysh-utqk5](https://codesandbox.io/embed/modest-keldysh-utqk5)