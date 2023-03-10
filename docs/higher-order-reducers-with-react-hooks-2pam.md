# 带反作用钩的高阶减速器

> 原文：<https://dev.to/changoman/higher-order-reducers-with-react-hooks-2pam>

*本帖最初发表于[codebushi.com](https://codebushi.com/react-hooks-higher-order-reducers/)T3】*

当使用新的`useReducer`钩子管理 React 状态时，您可能会发现在您的 reducer 函数中有一些经常重复的动作类型和逻辑。这里有一个使用高阶函数的简单方法，可以让你的定制钩子和 reducer 逻辑更加灵活和可重用。

为了演示这一点，让我们假设我们正在获取一些`Todos`，并将控制加载和错误状态。我们还可以使用`id`删除待办事项。

```
import React, { useEffect, useReducer } from 'react';

const initialState = {
  loading: false,
  error: false,
  data: []
};

function todosReducer(state, action) {
  switch (action.type) {
    case 'LOADING':
      return {
        ...state,
        loading: action.loading // Should be true/false
      };
    case 'ERROR':
      return {
        ...state,
        loading: false,
        error: action.error
      };
    case 'SET_DATA':
      return {
        loading: false,
        error: false,
        data: action.data
      };
    case 'DELETE_DATA':
      return {
        ...state,
        data: state.data.filter(datum => datum.id !== action.id)
      };
    default:
      return {
        ...state
      };
  }
}

const TodosPage = () => {
  const [state, dispatch] = useReducer(todosReducer, initialState);
  return (
    <div>
      {state.data.map(todo => (
        <TodoComponent key={todo.id} />
      ))}
    </div>
  );
};

export default TodosPage; 
```

Enter fullscreen mode Exit fullscreen mode

为了使这个例子简单，我不打算实际获取任何数据，我们只是假设它看起来像这样:

```
// Sample Todos Data
const todos = [
  {
    id: 1,
    title: 'Go Shopping'
  },
  {
    id: 2,
    title: 'Go To Gym'
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

在处理任何类型的获取数据时，这都是非常标准的。如果有多个页面需要这个 reducer 逻辑，我们可以把它放到一个定制的钩子中。

```
// Our useFetchData Custom Hook
import React, { useEffect, useReducer } from 'react';

const initialState = {
  loading: false,
  error: false,
  data: []
};

function dataReducer(state, action) {
  switch (action.type) {
    case 'LOADING':
      return {
        ...state,
        loading: action.loading
      };
    case 'ERROR':
      return {
        ...state,
        loading: false,
        error: action.error
      };
    case 'SET_DATA':
      return {
        loading: false,
        error: false,
        data: action.data
      };
    case 'DELETE_DATA':
      return {
        ...state,
        data: state.data.filter(datum => datum.id !== action.id)
      };
    default:
      return {
        ...state
      };
  }
}

const useFetchData = ({ url }) => {
  const [state, dispatch] = useReducer(dataReducer, initialState);

  useEffect(() => {
    const getInitialData = async () => {
      try {
        const response = await fetch(url);
        const data = await response.json();
        dispatch({
          type: 'SET_DATA',
          data
        });
      } catch (error) {
        dispatch({ type: 'ERROR', error });
      }
    };
    getInitialData();
  }, [url]);

  return [state, dispatch];
};

export default useFetchData; 
```

Enter fullscreen mode Exit fullscreen mode

在原来的`TodosPage`中使用自定义钩子看起来像这样:

```
import useFetchData from '../hooks/useFetchData';

const TodosPage = () => {
  const [state, dispatch] = useFetchData({
    url: 'https://someTodosApi'
  });

  return (
    <div>
      {state.data.map(todo => (
        <TodoComponent key={todo.id} />
      ))}
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止，我们还没有做任何棘手的事情。如果我们有一个不同的页面，我们可以通过为 api 传入一个不同的 url 来轻松地重用自定义钩子。然而，如果在另一页上数据看起来有点不同呢？而不是`Todos`，如果有`Contacts`需要显示和删除呢？

```
// Sample Contacts Data
const contacts = [
  {
    contactId: 1,
    name: 'John Doe'
  },
  {
    contactId: 2,
    name: 'Jane Doe'
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，按键现在是`contactId`而不仅仅是`id`。这只是数据可能略有不同的众多例子之一。我们仍然可以使用大多数自定义钩子，但是当我们删除数据时，我们需要使用`contactId`而不是`id`。

```
case 'DELETE_DATA':
  return {
    ...state,
    data: state.data.filter(datum => datum.contactId !== action.id)
  }; 
```

Enter fullscreen mode Exit fullscreen mode

我们怎样才能只调用自定义钩子的这一小部分，以便可以重用它呢？嗯，因为 reducer 是一个函数*，我们可以通过让函数`dataReducer`返回另一个函数来调用 Javascript 中高阶函数的能力。有人称之为高阶*减速器*。*

 *我们想要的是，在 Contacts 页面上，传入一个无论是什么键的字符串，这样我们就可以过滤那个字符串，而不是当前在我们钩子中的硬编码的`id`。

```
// Contacts Page
const ContactsPage = () => {
  const [state, dispatch] = useFetchData({
    url: 'https://someContactsApi',
    recordKey: 'contactId'
  });

  return (
    <div>
      {state.data.map(contact => (
        <ContactComponent key={contact.contactId} />
      ))}
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们需要调整我们的定制钩子来接收这个新的`recordKey`变量，并在我们的`dataReducer`中使用它。

```
import React, { useEffect, useReducer } from 'react';

const initialState = {
  loading: false,
  error: false,
  data: []
};

function dataReducer(recordKey) {
  return function(state, action) {
    switch (action.type) {
      case 'LOADING':
        return {
          ...state,
          loading: action.loading
        };
      case 'ERROR':
        return {
          ...state,
          loading: false,
          error: action.error
        };
      case 'SET_DATA':
        return {
          loading: false,
          error: false,
          data: action.data
        };
      case 'DELETE_DATA':
        return {
          ...state,
          data: state.data.filter(datum => datum[recordKey] !== action.id)
        };
      default:
        return {
          ...state
        };
    }
  };
}

const useFetchData = ({ url, recordKey }) => {
  const [state, dispatch] = useReducer(dataReducer(recordKey), initialState);

  useEffect(() => {
    const getInitialData = async () => {
      try {
        const response = await fetch(url);
        const data = await response.json();
        dispatch({
          type: 'SET_DATA',
          data
        });
      } catch (error) {
        dispatch({ type: 'ERROR', error });
      }
    };
    getInitialData();
  }, [url]);

  return [state, dispatch];
};

export default useFetchData; 
```

Enter fullscreen mode Exit fullscreen mode

我们的自定义钩子现在可以处理我们扔给它的任何种类的钥匙！这是一个非常简单的例子，但是请记住，我们可以将*任何东西*传递给我们的高阶 reducer，并在返回的 reducer 函数中包含条件逻辑。使用 React 钩子，识别组件之间共享的公共逻辑要容易得多。在整个应用程序中重用和共享组件逻辑也更容易。*