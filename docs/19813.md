# React Hooks by example: useState、useCallback、useEffect、useReducer

> 原文：<https://dev.to/kewah/react-hooks-by-example-usestate-usecallback-useeffect-usereducer-4l1i>

*照片由 [@ckirby](https://unsplash.com/@ckirby) 在[Unsplash](https://unsplash.com)T5 拍摄*

在本文中，我们将涉及如何使用`useCallback`、`useEffect`、`useReducer`和`useState`钩子。

我们将构建一个组件，让用户能够搜索用户列表。该组件将存储关于请求状态(如果正在加载)和响应(用户列表或错误信息)的数据。它将侦听表单提交事件，并用输入的值调用后端来获取用户列表。有不同的方法来实现它，比如使用 Redux，但是我们将保持它的基本性，因为我们将把重点放在钩子上。

## 类方式(无挂钩)

使用一个类组件，它可能是这样的:

```
class UserSearch extends React.Component {
  constructor(props, ...rest) {
    super(props, ...rest);

    this.state = {
      loading: false,
      error: undefined,
      users: undefined,
    };
  }

  componentWillUnmount() {
    if (this.request) {
      this.request.abort();
    }
  }

  handleFormSubmit = event => {
    this.setState({ loading: true });

    this.request = superagent.get(
      `http://localhost:8080/users/${event.target.elements.username.value}`
    );
    this.request
      .then(response => {
        this.setState({
          loading: false,
          users: response.body.items,
        });
      })
      .catch(error => {
        this.setState({
          loading: false,
          error,
        });
      });
  };

  render() {
    const { loading, error, users, searchValue } = this.state;

    return (
      <form onSubmit={this.handleFormSubmit}>
        {error && <p>Error: {error.message}</p>} 
        <input type="text" name="username" disabled={loading} />
        <button type="submit" disabled={loading}>
          Search
        </button> 
        {loading && <p>Loading...</p>} 
        {users && (
          <div>
            <h1>Result</h1>
            <ul>
              {users.map(({ id, name }) => (
                <li key={id}>{name}</li>
              ))}
            </ul>
          </div>
        )}
      </form>
    );
  }
} 
```

## 功能方式

我们将一步一步地重构`UserSearch`组件，并在过程中引入钩子。

当我们使用钩子时，我们不再需要使用类。第一步是将 render 方法提取到基于函数的组件中。我们还内嵌了状态和事件处理程序，但是目前，它们什么都不做。

```
const UserSearch = () => {
  const loading = false;
  const users = undefined;
  const error = undefined;

  const handleFormSubmit = () => {
    // TODO
  };

  return (
    <form onSubmit={handleFormSubmit}>
      {error && <p>Error: {error.message}</p>} 
      <input type="text" name="username" disabled={loading} />
      <button type="submit" disabled={loading}>
        Search
      </button> 
      {loading && <p>Loading...</p>} 
      {users && (
        <div>
          <h1>Result</h1>
          <ul>
            {users.map(({ id, name }) => (
              <li key={id}>{name}</li>
            ))}
          </ul>
        </div>
      )}
    </form>
  );
}; 
```

## 引入挂钩

### 使用状态

我们可以使用`useState`钩子来存储组件中的不同状态(加载、用户、错误)。`useState`以初始值为参数，返回状态值的元组和更新值的函数。

```
const [value, setValue] = useState(initialValue); 
```

让我们使用`setState`来更新我们的状态。目前，我们只初始化状态，但是我们需要实现逻辑。

```
const UserSearch = () => {
  const [loading, setLoading] = userState(false);
  const [users, setUsers] = useState();
  const [error, setError] = useState();

  const handleFormSubmit = () => {
    // TODO
  };

  return (
    <form onSubmit={handleFormSubmit}>
      {error && <p>Error: {error.message}</p>} 
      <input type="text" name="username" disabled={loading} />
      <button type="submit" disabled={loading}>
        Search
      </button> 
      {loading && <p>Loading...</p>} 
      {users && (
        <div>
          <h1>Result</h1>
          <ul>
            {users.map(({ id, name }) => (
              <li key={id}>{name}</li>
            ))}
          </ul>
        </div>
      )}
    </form>
  ); 
```

### 使用回调

基于函数的组件没有生命周期，React 为每个新的渲染调用函数，这意味着每次重新渲染时，每个提升的对象都将被重新创建。例如，每次都会创建一个新的`handleFormSubmit`函数。其中一个问题是它使树无效，因为`<form onSubmit={handleFormSubmit}>`在渲染之间是不同的(上一个`handleFormSubmit` ≠下一个`handleFormSubmit`因为`() => {} !== () => {}`)。

这就是`useCallback`发挥作用的地方。它缓存该函数，并仅在依赖关系改变时创建一个新函数。依赖项是在组件中创建的值，但是在`useCallback`范围之外。

```
const fn = useCallback(() => {}, [dependencies]); 
```

在[文档](https://reactjs.org/docs/hooks-reference.html#usecallback)中，他们建议“回调中引用的每个值也应该出现在依赖数组中。”但是，您可以从依赖项中省略`dispatch`(来自`useReducer`)、`setState`和`useRef`容器值，因为 React 保证它们是静态的。但是，指定它们也无妨。注意，如果我们为依赖项传递一个空数组，它将总是返回相同的函数。

我建议您使用[eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)来帮助您了解我们需要在依赖项中包含哪些值。

你也应该看看 Kent C. Dodds 写的关于什么时候使用`useCallback` 的文章，因为在内联回调中使用它也会带来性能损失。剧透:对于引用等式和依赖列表。

因此，如果我们遵循类是如何完成的，我们可以直接在`useCallback`中执行`GET`请求。

```
const UserSearch = () => {
  const [loading, setLoading] = userState(false);
  const [users, setUsers] = useState();
  const [error, setError] = useState();

  const handleFormSubmit = useCallback(
    event => {
      event.preventDefault();

      setLoading(true);

      const request = superagent.get(
        `http://localhost:8080/users/${event.target.elements.username.value}`
      );
      request
        .then(response => {
          setLoading(false);
          setUsers(response.body.items);
        })
        .catch(error => {
          setLoading(false);
          setError(error);
        });
    },
    [setLoading, setUsers, setError]
  );

  return (
    <form onSubmit={handleFormSubmit}>
      {error && <p>Error: {error.message}</p>} 
      <input type="text" name="username" disabled={loading} />
      <button type="submit" disabled={loading}>
        Search
      </button> 
      {loading && <p>Loading...</p>} 
      {users && (
        <div>
          <h1>Result</h1>
          <ul>
            {users.map(({ id, name }) => (
              <li key={id}>{name}</li>
            ))}
          </ul>
        </div>
      )}
    </form>
  );
}; 
```

⚠️:这是可行的，这样做不会有什么问题。当 React 卸载组件时，不会像我们在`componentWillUnmount`中那样中止请求。此外，因为请求是挂起的，所以 React 会保存一个对未安装组件的引用。因此，它浪费了浏览器资源，因为用户永远不会与之交互。

### 使用效果

为基于功能的组件带来生命周期。是`componentDidMount`、`componentDidUpdate`、`componentWillUnmount`的组合。当一个依赖关系被更新时，`useEffect`的回调被执行。所以，第一次渲染组件时，会执行`useEffect`。在我们的例子中，我们希望在搜索值更新时开始请求(在表单提交时)。我们将引入一个在`handleFormSubmit`处理程序中更新的新状态`searchValue`，并且我们将使用该状态作为钩子的依赖。因此，当`searchValue`被更新时，也将执行`useEffect`钩子。

最后，`useEffect`回调必须返回一个用于清理的函数，对我们来说，这是我们中止请求的地方。

```
const UserSearch = () => {
  const [loading, setLoading] = userState(false);
  const [users, setUsers] = useState();
  const [error, setError] = useState();
  const [searchValue, setSearchValue] = useState();

  const handleFormSubmit = useCallback(
    event => {
      event.preventDefault();
      setSearchValue(event.target.elements.username.value);
    },
    [setSearchValue]
  );

  useEffect(() => {
    let request;

    if (searchValue) {
      setLoading(true);

      request = superagent.get(
        `http://localhost:8080/users/${event.target.elements.username.value}`
      );
      request
        .then(response => {
          setError(undefined);
          setLoading(false);
          setUsers(response.body.items);
        })
        .catch(error => {
          setLoading(false);
          setError(error);
        });
    }

    return () => {
      if (request) {
        request.abort();
      }
    };
  }, [searchValue, setLoading, setUsers, setError]);

  return (
    <form onSubmit={handleFormSubmit}>
      {error && <p>Error: {error.message}</p>} 
      <input type="text" name="username" disabled={loading} />
      <button type="submit" disabled={loading}>
        Search
      </button> 
      {loading && <p>Loading...</p>} 
      {users && (
        <div>
          <h1>Result</h1>
          <ul>
            {users.map(({ id, name }) => (
              <li key={id}>{name}</li>
            ))}
          </ul>
        </div>
      )}
    </form>
  );
}; 
```

丹·阿布拉莫夫写了一篇关于`useEffect`钩子的精彩博文:[使用效果](https://overreacted.io/a-complete-guide-to-useeffect/)的完整指南。

### useReducer

我们现在有了一个使用 React 钩子的组件的工作版本🎉。我们可以改进的一点是，当我们必须跟踪几个状态时，比如在请求的响应中，我们更新三个状态。在我们的例子中，我认为使用当前版本就可以了。然而，如果我们需要添加更多的州，`useReducer`将是一个更好的选择。这允许我们在代码的相同区域收集相关的状态，并有一种方法来更新状态。

`useReducer`需要一个 reducer 函数(该函数执行一个操作并返回一个新状态)和初始状态。类似于`useState`,它返回一个包含状态和我们用来分派动作的分派函数的元组。

```
const [state, dispatch] = useReducer(reducer, initialState); 
```

```
const initialState = {
  loading: false,
  users: undefined,
  error: undefined,
  searchValue: undefined,
};

const SET_SEARCH_VALUE = 'SET_SEARCH_VALUE';
const FETCH_INIT = 'FETCH_INIT';
const FETCH_SUCCESS = 'FETCH_SUCCESS';
const ERROR = 'ERROR';

const reducer = (state, { type, payload }) => {
  switch (type) {
    case SET_SEARCH_VALUE:
      return {
        ...state,
        searchValue: payload,
      };

    case FETCH_INIT:
      return {
        ...state,
        error: undefined,
        loading: true,
      };

    case FETCH_SUCCESS:
      return {
        ...state,
        loading: false,
        error: undefined,
        result: payload,
      };

    case ERROR:
      return {
        ...state,
        loading: false,
        error: payload,
      };

    default:
      throw new Error(`Action type ${type} unknown`);
  }
};

const UserSearch = () => {
  const [state, dispatch] = useReducer(reducer, initialState);

  const handleFormSubmit = useCallback(
    event => {
      event.preventDefault();

      dispatch({
        type: SET_SEARCH_VALUE,
        payload: event.target.elements.username.value,
      });
    },
    [dispatch]
  );

  useEffect(() => {
    let request;

    if (state.searchValue) {
      // highlight-next-line
      dispatch({ type: FETCH_INIT });

      request = superagent.get(
        `http://localhost:8080/users/${state.searchValue}`
      );
      request
        .then(response => {
          // highlight-next-line
          dispatch({ type: FETCH_SUCCESS, payload: response.body.items });
        })
        .catch(error => {
          // highlight-next-line
          dispatch({ type: ERROR, payload: error });
        });
    }

    return () => {
      if (request) {
        request.abort();
      }
    };
  }, [state.searchValue, dispatch]);

  return (
    <form onSubmit={handleFormSubmit}>
      {state.error && <p>Error: {state.error.message}</p>} 
      <input type="text" name="username" disabled={state.loading} />
      <button type="submit" disabled={state.loading}>
        Search
      </button> 
      {state.loading && <p>Loading...</p>} 
      {state.users && (
        <div>
          <h1>Result</h1>
          <ul>
            {state.users.map(({ id, name }) => (
              <li key={id}>{name}</li>
            ))}
          </ul>
        </div>
      )}
    </form>
  );
}; 
```

如前所述，好处并不明显，因为在我们的例子中没有太多的状态需要处理。比`useState`版本有更多的样板文件，但是所有与调用 API 相关的状态都在 reducer 函数中管理。