# 如何使用 React-tracked:React hooks-oriented Todo 列表示例

> 原文：<https://dev.to/dai_shi/how-to-use-react-tracked-react-hooks-oriented-todo-list-example-40hl>

##### 同 immer

### 简介

React 钩子改变了组成组件的方式。这篇文章将展示一个非常面向钩子的例子。

我们使用两个库:react-tracked 和 immer。immer 使得以不可变的方式更新状态变得容易，而 react-tracked 使得通过跟踪读取状态以进行优化变得容易。更多详情请查看回购协议。

[https://github.com/dai-shi/react-tracked](https://github.com/dai-shi/react-tracked)

我们展示的例子来自 Redux: [Todo List](https://redux.js.org/basics/example)

### 文件夹结构

```
- src/
  - index.tsx
  - state.ts
  - hooks/
    - useAddTodo.ts
    - useToggleTodo.ts
    - useVisibilityFilter.ts
    - useVisibleTodos.ts
  - components/
    - AddTodo.tsx
    - App.tsx
    - FilterLink.tsx
    - Footer.tsx
    - Todo.tsx
    - VisibleTodoList.tsx 
```

Enter fullscreen mode Exit fullscreen mode

我们有两个文件夹`components`和`hooks`。组件基本上是视图。钩子包括逻辑。

### src/state.ts

在这个例子中，我们没有使用减速器。我们只定义一个状态和一些类型。

```
import { useState } from 'react';

export type VisibilityFilterType =
  | 'SHOW_ALL'
  | 'SHOW_COMPLETED'
  | 'SHOW_ACTIVE';

export type TodoType = {
  id: number;
  text: string;
  completed: boolean;
};

export type State = {
  todos: TodoType[];
  visibilityFilter: VisibilityFilterType;
};

const initialState: State = {
  todos: [],
  visibilityFilter: 'SHOW_ALL',
};

export const useValue = () => useState(initialState);

export type SetState = ReturnType<typeof useValue>[1]; 
```

Enter fullscreen mode Exit fullscreen mode

注意最后一行。这可能有点棘手。
`SetState`是`setState`的一种类型。

### src/hooks/useAddTodo.ts

```
import { useCallback } from 'react';
import { useDispatch } from 'react-tracked';
import produce from 'immer';

import { SetState } from '../state';

let nextTodoId = 0;

const useAddTodo = () => {
  const setState = useDispatch<SetState>();
  const addTodo = useCallback((text: string) => {
    setState(s => produce(s, (draft) => {
      draft.todos.push({
        id: nextTodoId++,
        text,
        completed: false,
      });
    }));
  }, [setState]);
  return addTodo;
};

export default useAddTodo; 
```

Enter fullscreen mode Exit fullscreen mode

这是负责添加项目的钩子。我们这里用 immer，但没必要。

### src/hooks/usetoggletodo . ts

```
import { useCallback } from 'react';
import { useDispatch } from 'react-tracked';
import produce from 'immer';

import { SetState } from '../state';

const useToggleTodo = () => {
  const setState = useDispatch<SetState>();
  const toggleTodo = useCallback((id: number) => {
    setState(s => produce(s, (draft) => {
      const found = draft.todos.find(todo => todo.id === id);
      if (found) {
        found.completed = !found.completed;
      }
    }));
  }, [setState]);
  return toggleTodo;
};

export default useToggleTodo; 
```

Enter fullscreen mode Exit fullscreen mode

同样的想法，这个挂钩切换一个项目。

### src/hooks/usevisibilityfilter . ts

```
import { useCallback } from 'react';
import { useTracked } from 'react-tracked';
import produce from 'immer';

import { VisibilityFilterType, State, SetState } from '../state';

const useVisibilityFilter = () => {
  const [state, setState] = useTracked<State, SetState>();
  const setVisibilityFilter = useCallback((filter: VisibilityFilterType) => {
    setState(s => produce(s, (draft) => {
      draft.visibilityFilter = filter;
    }));
  }, [setState]);
  return [state.visibilityFilter, setVisibilityFilter] as [
    VisibilityFilterType,
    typeof setVisibilityFilter,
  ];
};

export default useVisibilityFilter; 
```

Enter fullscreen mode Exit fullscreen mode

这个钩子用于返回当前的`visibilityFilter`和一个 setter 函数。为此我们使用了`useTracked`。把`useTrackedState`和`useDispatch`组合起来就是组合钩。

### src/hook/usevisible all . ts

```
import { useTrackedState } from 'react-tracked';

import { TodoType, VisibilityFilterType, State } from '../state';

const getVisibleTodos = (todos: TodoType[], filter: VisibilityFilterType) => {
  switch (filter) {
    case 'SHOW_ALL':
      return todos;
    case 'SHOW_COMPLETED':
      return todos.filter(t => t.completed);
    case 'SHOW_ACTIVE':
      return todos.filter(t => !t.completed);
    default:
      throw new Error(`Unknown filter: ${filter}`);
  }
};

const useVisibleTodos = () => {
  const state = useTrackedState<State>();
  return getVisibleTodos(state.todos, state.visibilityFilter);
};

export default useVisibleTodos; 
```

Enter fullscreen mode Exit fullscreen mode

这个钩子处理 Todo 项目的过滤。

### src/components/add todo . tsx

```
import * as React from 'react';
import { useState } from 'react';

import useAddTodo from '../hooks/useAddTodo';

const AddTodo: React.FC = () => {
  const [text, setText] = useState('');
  const addTodo = useAddTodo();
  return (
    <div>
      <form
        onSubmit={(e) => {
          e.preventDefault();
          if (!text.trim()) {
            return;
          }
          addTodo(text);
          setText('');
        }}
      >
        <input value={text} onChange={e => setText(e.target.value)} />
        <button type="submit">Add Todo</button>
      </form>
    </div>
  );
};

export default AddTodo; 
```

Enter fullscreen mode Exit fullscreen mode

除了`useAddTodo`是从`hooks`文件夹导入的以外，没有什么特别需要注意的。

### src/components/Todo.tsx

```
import * as React from 'react';

type Props = {
  onClick: (e: React.MouseEvent) => void;
  completed: boolean;
  text: string;
};

const Todo: React.FC<Props> = ({ onClick, completed, text }) => (
  <li
    onClick={onClick}
    role="presentation"
    style={{
      textDecoration: completed ? 'line-through' : 'none',
      cursor: 'pointer',
    }}
  >
    {text}
  </li>
);

export default Todo; 
```

Enter fullscreen mode Exit fullscreen mode

这是一个没有挂钩依赖的组件。

### src/components/visibletodolist . tsx

```
import * as React from 'react';

import useVisibleTodos from '../hooks/useVisibleTodos';
import useToggleTodo from '../hooks/useToggleTodo';
import Todo from './Todo';

const VisibleTodoList: React.FC = () => {
  const visibleTodos = useVisibleTodos();
  const toggleTodo = useToggleTodo();
  return (
    <ul>
      {visibleTodos.map(todo => (
        <Todo key={todo.id} {...todo} onClick={() => toggleTodo(todo.id)} />
      ))}
    </ul>
  );
};

export default VisibleTodoList; 
```

Enter fullscreen mode Exit fullscreen mode

这和原来的例子不一样。我们把过滤逻辑移到了钩子上。

### src/components/filter link . tsx

```
import * as React from 'react';

import useVisibilityFilter from '../hooks/useVisibilityFilter';
import { VisibilityFilterType } from '../state';

type Props = {
  filter: VisibilityFilterType;
};

const FilterLink: React.FC<Props> = ({ filter, children }) => {
  const [visibilityFilter, setVisibilityFilter] = useVisibilityFilter();
  const active = filter === visibilityFilter;
  return (
    <button
      type="button"
      onClick={() => setVisibilityFilter(filter)}
      disabled={active}
      style={{
        marginLeft: '4px',
      }}
    >
      {children}
    </button>
  );
};

export default FilterLink; 
```

Enter fullscreen mode Exit fullscreen mode

这使用了`useVisibilityFilter`挂钩。注意钩子返回一个元组、一个值和一个 setter 函数。

### src/components/footer . tsx

```
import * as React from 'react';

import FilterLink from './FilterLink';

const Footer: React.FC = () => (
  <div>
    <span>Show: </span>
    <FilterLink filter="SHOW_ALL">All</FilterLink>
    <FilterLink filter="SHOW_ACTIVE">Active</FilterLink>
    <FilterLink filter="SHOW_COMPLETED">Completed</FilterLink>
  </div>
);

export default Footer; 
```

Enter fullscreen mode Exit fullscreen mode

这个组件没有什么特别需要注意的。

### src/components/App.tsx

```
import * as React from 'react';

import Footer from './Footer';
import AddTodo from './AddTodo';
import VisibleTodoList from './VisibleTodoList';

const App: React.FC = () => (
  <div>
    <AddTodo />
    <VisibleTodoList />
    <Footer />
  </div>
);

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

这是将其他组件组合在一起的组件。

### src/index.tsx

最后，我们需要入口点。

```
import * as React from 'react';
import { render } from 'react-dom';
import { Provider } from 'react-tracked';

import { useValue } from './state';
import App from './components/App';

const Index = () => (
  <Provider useValue={useValue}>
    <App />
  </Provider>
);

render(React.createElement(App), document.getElementById('app')); 
```

Enter fullscreen mode Exit fullscreen mode

注意`<Provider>`从 state.ts 传递了`useValue`。

### 在线演示

[codesandbox](https://codesandbox.io/s/github/dai-shi/react-tracked/tree/master/examples/07_todolist)

[回购中的源代码](https://github.com/dai-shi/react-tracked/tree/master/examples/07_todolist)

### 期末备注

在我写这篇文章的时候，我注意到了一些事情。我最初的动机是展示如何使用反应跟踪。然而，这个例子也很好地展示了 setState 和定制钩子如何在没有 reducers 的情况下分离关注点。我的另一个小发现是，在这个例子中，immer 对定制钩子没有多大帮助。

我们没有过多讨论性能优化。还有改进的余地。最简单的一个就是用`React.memo`。优化可能是未来帖子的一个单独主题。

* * *

最初发表于 2019 年 7 月 8 日[https://blog.axlight.com](https://blog.axlight.com/posts/how-to-use-react-tracked-react-hooks-oriented-todo-list-example/)。