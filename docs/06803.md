# 孩子的待办事项:Redux

> 原文：<https://dev.to/fosteman/kid-s-to-do-redux-1ma>

让我们在 Redux 中创建一个待办事项 app。

我想开始一个组件树，可以显示可切换的待办事项列表。

我将在 React 的默认`index.js`中引导 Redux 实例，文件管理可能会在这个应用程序的生命周期中出现。我将写另一篇关于正确存储 Redux 多模块实例的文章。

我将构建新的根`<TodoApp />`，它将被连接到冗余状态管理。

然后沿着树向下传递属性:`<TodoList />`和`<TodoItem />`。后者将显示 todo 的名称，并在其表面有一个切换。

将这些组件中的每一个都与`connect`联系起来是不合理的，因为它们并不遥远。然而，考虑在较大的应用中使用这个特设。

非常直接。确保你有网络连接，还有一把螺丝刀以防软盘被卡住。

## 第一步:从创建 React 应用开始

`npx create-react-app todo-app`

`cd todo-app`

安装 Redux

`npm i --save redux`

`npm start`

## 第二步:还原实例

如下修改`index.js`来导入 Redux 功能，*通过将*封装在`<Provider />`中来为组件树提供 Redux 状态。`TodoApp`组件是*连接*和`connect` HOC，向树公开状态和调度程序

```
import { combineReducers, createStore } from 'redux'
import { Provider } from 'react-redux'
import TodoApp from './todoApp'

/*
* action types 
* initial state
* reducers
* action creators
* reducer combination
* store declaration
*/

function mapStateToProps(state) {
  return {
    todos: state.todoState,
  };
}
function mapDispatchToProps(dispatch) {
  return {
     onToggleTodo: id => dispatch(doToggleTodo(id)),
  };
}

const ConnectedTodoApp = connect(mapStateToProps, mapDispatchToProps)(TodoApp);

ReactDOM.render(
  <Provider store={store}>
    <ConnectedTodoApp />
  </Provider>,
  document.getElementById('root')
); 
```

### 动作类型

```
// action types
const TODO_ADD = 'TODO_ADD';
const TODO_TOGGLE = 'TODO_TOGGLE'; 
const FILTER_SET = 'FILTER_SET'; 
```

### 初始状态

```
// initial state
const todos = [
    { id: '0', name: 'Outline blog post' }, 
    { id: '1', name: 'Build TodoApp' },
]; 
```

### 减速器

```
// reducers
function todoReducer(state = todos, action) {
    switch(action.type) {
        case TODO_ADD: {
            return applyAddTodo(state, action);
        }
        case TODO_TOGGLE: {
            return applyToggleTodo(state, action); 
        }
        default : return state; 
    }
} 

function applyAddTodo(state, action) {
    const todo = Object.assign({}, action.todo, {completed: false});
    return state.concat(todo);
}

function applyToggleTodo(state, action) {
    return state.map(todo => todo.id === action.todo.id ?
        Object.assign({}, todo, {completed: !todo.completed})
        : todo
    );
}

function filterReducer(state = 'SHOW_ALL', action) {
    switch(action.type) {
    case FILTER_SET: {
        return applySetFilter(state, action);
    }
    default: return state;
    }
}

function applySetFilter(state, action) {
    return action.filter;
} 
```

### 动作创建者

```
// action creators
function doAddTodo(id, name) {
    return {
        type: TODO_ADD,
        todo: {id, name}
    };
}

function doToggleTodo(id) {
    return {
        type: TODO_TOGGLE,
        todo: { id }
    };
}

function doSetFilter(filter) {
    return {
        type: FILTER_SET,
        filter
    };
} 
```

最后，让我们将`todoReducer`和`filterReducer`结合起来，创建商店

```
const rootReducer = combineReducers({
    todoState: todoReducer,
    filterState: filterReducer
});

const store = createStore(rootReducer); 
```

安装了 Redux 之后，让我们从一个新的根`TodoApp`开始构建待办应用组件树。

## 第三步:组件

### `TodoApp`

```
import React from 'react'
import TodoList from './todoList'

export default function TodoApp({ todos, onToggleTodo }) { 
    return (<TodoList
                    todos={store.getState().todoState}
                      onToggleTodo={id => store.dispatch(doToggleTodo(id))}
                  />);
} 
```

### `TodoList`

```
import React from 'react'
import TodoItem from './todoItem'

export default function TodoList({ todos, onToggleTodo }) { 
    return (
    <div>
      {todos.map(todo => 
                <TodoItem
            key={todo.id}
            todo={todo}
            onToggleTodo={onToggleTodo}
                />)} 
        </div>
    ); 
} 
```

### `TodoItem`

```
import React from 'react'

export default function TodoItem({ todo, onToggleTodo }) { 
    const { name, id, completed } = todo; 
        return (
            <div> {name}
            <button
            type="button"
            onClick={() => onToggleTodo(id)}
          >
        {completed ? "Incomplete" : "Complete"}
                </button>
            </div>
        );
} 
```

这些组件都不知道 Redux。它们只是显示待办事项，并使用回调来传播待办事项的*切换*。

存储做两件事:它使状态可访问，并公开改变状态的功能。todos 属性通过从`store`实例中检索来传递给`TodoApp`。此外，`onToggleTodo`函数作为一个属性沿着树向下传递，请注意，它是一个封装了由动作创建者创建的动作的调度的特设函数。