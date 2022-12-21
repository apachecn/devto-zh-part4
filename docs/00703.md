# React Hooks | Todo 应用简介

> 原文：<https://dev.to/yomeshgupta/intro-to-react-hooks-todo-app-4pkj>

React 是当今最受欢迎和发展最快的 UI 库之一。每一个新版本都有新的特性和反对意见。让我们来谈谈 React 的最新功能之一，即钩子。

## 为什么反应钩子？

**钩子** 是 React 16.8 新增的，提供功能组件中的状态管理和副作用。

1)早期，只有类组件用于本地状态管理和生命周期方法。这些生命周期方法对于引入副作用是必不可少的，比如数据获取、监听器等等。这导致了从 **功能性无状态组件** 到 **有状态类组件** 的大量重构，只要功能性组件需要使用状态或生命周期方法。使用钩子，我们可以使用类似于*状态*和*效果*的特性，而实际上不需要任何组件转换。我们将在后面的帖子中构建应用程序时更多地讨论 **useState** 和 **useEffect** 。

```
import React, { useState, useEffect } from 'react';

function Counter() {
    // Using state in a functional component
    const [count, setCount] = useState(0);

    // Using effects in a functional component
    useEffect(() => {
        document.title = `Counter: ${count}`;
    }, [count]);

    render(
        <div>
            <p>You have clicked {count} times</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
        </div>
    );
} 
```

React 组件之间可重用的有状态行为有点棘手。不过，这可以使用像[渲染道具](https://reactjs.org/docs/render-props.html)和[高阶组件](https://reactjs.org/docs/higher-order-components.html)这样的模式来完成。使用这种模式，组件必须重新构造，这使得代码更难理解和维护。通过钩子，有状态逻辑可以从组件中提取出来，放入它们自己的定制钩子中，这使得它们可以被独立测试，并且可以被重用。

## 开始造吧！

在这篇博文中，我们将构建一个简单的 Todo 应用程序。同样的演示可以在[这里](https://codesandbox.io/s/71j9q45y11)和[这里](https://github.com/yomeshgupta/react-hooks-todo-app)看到。

```
import React, { useState } from 'react';

function Todos() {
    // Creating a todos state variable
    const [todos, setTodos] = useState({
        1552406885681: {
            todo: 'Complete this blog post',
            isComplete: false
        }
    });

    // Rendering the todos
    return <div className="wrapper">
        <ul id="todos">
            {Object.entries(todos).map(([key, value]) => <li key={key}>{value.todo}</li>);}
        </ul>
    </div>;
} 
```

### 用 useState()定义状态

如前所述，现在我们可以在功能组件中进行状态管理，为此 React 提供了一个名为 **useState** 的钩子。

1)它处于初始状态。与类组件的状态不同，useState 的初始状态不必是对象。它可以是字符串、布尔值、对象或 JavaScript 中任何其他可能的值。

```
const [count, setCount] = useState(0); // number
const [name, setName] = useState('Yomesh'); // string
const [fetched, setFetched] = useState(false); // boolean
const [todos, setTodos] = useState({}); // object 
```

2)它声明了一个“状态变量”,其值存在于函数调用之间。它提供了与`this.state`完全相同的功能。

3)它返回一对值:当前状态和更新它的函数。我们通过数组析构得到这些返回值`const [todos, setTodos] = useState({});`

在上面的代码示例中，我们创建了一个名为`todos`的状态变量，默认值为当前的 todos。

### 抓取待办事项

前面，我们已经为我们的 todo 提供了一个默认值，但是如果我们必须从远程 API 获取这些 todo 呢？在这种情况下，我们需要类似于`componentDidMount`的东西来获取数据，就像我们过去在类组件中做的那样。

React 为我们提供了一个名为`useEffect`的钩子，它可以直接用于组件中，并提供了一种模仿这些生命周期方法并超越它们的方法。当我们谈论效果时，我们基本上指的是数据获取、DOM 更新、事件侦听器等。让我们一步一步地看看这一点。

```
import React, { useState, useEffect } from 'react';

const TODOS = {
    1552406885681: {
        todo: 'Complete this blog post',
        isComplete: false
    }
};

function Todos() {
    // Creating a todos state variable
    const [todos, setTodos] = useState({});

    // Setting up an effect
    useEffect(function() {
        // fetch(REMOTE_URL).then(response => setTodos(response));

        // mocking API call
        return new Promise((resolve, reject) => {
            setTimeout(() => resolve(TODOS), 2000);
        }).then(response => {
            // Updating state variable
            setTodos(response);
        });
    });

    // Rendering the todos
    return (
        <div className="wrapper">
            <ul id="todos">
                {Object.keys(todos).map(key => {
                    const value = todos[key];
                    return <li key={key}>{value.todo}</li>;
                })}
            </ul>
        </div>
    );
} 
```

`useEffect`总是在渲染后运行。所以，它们本质上是非阻塞的。可以把它们看作是给 React 一个指令，并在 DOM 渲染之后执行它。它有两个参数:一个将在渲染后执行的函数和一个依赖数组(下面会详细介绍)。

在上面的代码片段中:

1)我们正在设置一个用于从 API 获取数据的效果(模仿)。因此，最初，我们的待办事项将是空的，我们不会看到任何东西。在初始渲染之后，`useEffect`将被执行，数据被获取。

2)效果挂钩应该不返回任何内容或者返回一个清理函数。这就是为什么您可能会在开发人员控制台日志中看到以下错误- `Warning: An Effect function must not return anything besides a function, which is used for clean-up. It looks like you wrote useEffect(async () => ...) or returned a Promise. Instead, you may write an async function separately and then call it from inside the effect`。我们可以通过
解决这个问题

```
 ...
    // Setting up an effect
    useEffect(function() {
        function fetchData() {
            // fetch(REMOTE_URL).then(response => setTodos(response));

            // mocking API call
            new Promise((resolve, reject) => {
                setTimeout(() => resolve(TODOS), 2000);
            }).then(response => {
                // Updating state variable
                setTodos(response);
            });
        }

        fetchData();
    });
    ...
} 
```

3)这一切都很好，但是如果你执行到目前为止的代码，你会看到`fetchData`会在每次渲染后被调用，因为`useEffect`也会在每次渲染后执行！看看这个[沙盒](https://codesandbox.io/s/xpjrkj2wxo)就明白我的意思了。

为了避免这种无限循环和优化，`useEffect`采用第二个参数，称为`dependency array`。在这个数组中，我们可以提到所有的变量，这些变量的值的改变依赖于`useEffect`的执行。假设，我们有一个用例，我们需要根据输入查询显示从远程 API 获取的项目列表。在这种情况下，输入查询将是依赖数组的一部分。看看这个[沙盒](https://codesandbox.io/s/l0n6qn3x7)的真实例子。

但是在我们的应用程序中，我们只需要在初始加载后读取一次数据。我们可以通过提供一个空数组作为`useEffect`的第二个参数来实现。通过这样做，我们的效果将只在初始渲染后运行一次，就像这里的`componentDidMount`一样。

```
...
useEffect(function() {
    {
        /* some processing */
    }
}, []); // acts like componentDidMount
... 
```

4)现在，数据提取部分完成了。重点看下面的代码

```
.then(response => setTodos(response)); 
```

正如我们前面所讨论的，`const [todos, setTodos] = useState({})`为我们提供了两个返回值。第二个值是一个允许我们更新状态变量的函数。在这里，它与在一个类组件中做`this.setState({ todos: response })`
是一样的。

### 添加加载器和清空状态

我们将添加一个`Loader`和`Empty`组件。最初，没有 todos，因此将显示`Loader`，如果获取没有返回结果，则将显示`Empty`状态。

```
...
function Loader() {
    return <div id="loader">Loading...</div>;
}

function Empty() {
    return <div id="empty">No todos found...</div>;
}

function Todos() {
    const [fetched, setFetched] = useState(false);
    const [todos, setTodos] = useState({});
    const keys = Object.keys(todos);

    useEffect(function() {
        function fetchData() {
            new Promise((resolve, reject) => {
                setTimeout(() => resolve(TODOS), 2000);
            }).then(response => {
                setFetched(true);
                setTodos(response);
            });
        }

        fetchData();
    }, []);

    function renderContent() {
        if (!fetched) {
            return <Loader />;
        } else if (!keys.length) {
            return <Empty />;
        }
        return (
            <ul id="todos">
                {keys.map(key => {
                    const value = todos[key];
                    return <li key={key}>{value.todo}</li>;
                })}
            </ul>
        );
    }

    return <div className="wrapper">{renderContent()}</div>;
} 
```

### 重构...

到目前为止还不错，但我们可以更上一层楼。从`Redux`王国来的人会喜欢的。

#### 欢迎 useReducer！

它是`useState`的替代物。它接受三个参数- `reducer`、`initialState`、`init function`，并返回当前的`state`和`dispatch`函数来更新状态。

根据 React 文档，

> 当您有涉及多个子值的复杂状态逻辑或者下一个状态依赖于前一个状态时，useReducer 通常比 useState 更可取。

```
const [state, dispatch] = useReducer(reducer, initialState, init); 
```

现在让我们重构一些代码。

```
import React, { useEffect, useReducer } from 'react';

const TODOS = {
    1552406885681: {
        todo: 'Complete this blog post',
        isComplete: false
    }
};

const initialState = {
    fetched: false,
    todos: {}
};

function reducer(state, action) {
    switch (action.type) {
        case 'REPLACE_TODOS':
            return { ...state, fetched: true, todos: action.payload };
        default:
            return state;
    }
}
...
function Todos() {
    const [state, dispatch] = useReducer(reducer, initialState);
    const { fetched, todos } = state;
    const keys = Object.keys(todos);

    useEffect(function() {
        function fetchData() {
            new Promise((resolve, reject) => {
                setTimeout(() => resolve(TODOS), 2000);
            }).then(response => {
                dispatch({
                    type: 'REPLACE_TODOS',
                    payload: response
                });
            });
        }

        fetchData();
    }, []);
    ...
} 
```

我们可以使用组件层次结构内部的`dispatch`来更新我们的状态，就像传统的`Redux Actions`一样！

### 保存，完成，删除

在这一部分，我们将添加以下功能

#### **保存新待办事宜**

在这里，我们声明一个新的状态变量`task`，并将添加一个表单。我们将在状态变量中捕获新的 todo，并在表单提交时通过调度新的动作将它添加到列表中。

```
...
const [task, setTask] = useState('');

function reducer(state, action) {
    switch (action.type) {
        ...
        case 'ADD_TODO':
            return {
                ...state,
                todos: {
                    ...state.todos,
                    ...action.payload
                }
            }
        ...
    }
}
...
function saveHandler(e) {
    e.preventDefault();
    dispatch({
        type: 'ADD_TODO',
        payload: {
            [+new Date()]: {
                todo: task,
                isComplete: false
            }
        }
    });
    setTask('');
}

return (
    <div className="wrapper">
        <form method="POST" onSubmit={saveHandler}>
            <input type="text" onChange={e => setTask(e.target.value)} value={task} placeholder="What needs to be done?" />
            <input type="submit" value="Add" />
        </form>
... 
```

#### **将待办事项标记为完成**

现在，我们将向待办事项列表添加一些控件。我突出显示了添加的代码。如你所见，我们增加了一个`Check FontAwesomeIcon`。单击 check 控件时，会分派一个动作来更新我们的当前状态，并将该特定待办事项的`isComplete`标志设置为`true`。

你可以随时参考这个[沙盒](https://codesandbox.io/s/71j9q45y11)中的实例来更好地形象化它。

```
...
function reducer(state, action) {
    switch (action.type) {
        ...
        case "COMPLETE_TODO":
            return {
                ...state,
                todos: {
                    ...state.todos,
                    [action.payload.id]: {
                        ...state.todos[action.payload.id],
                        isComplete: true
                    }
                }
            };
        ...
    }
}
function controlHandler(id, operation) {
    switch (operation) {
        case "complete":
            dispatch({
                type: "COMPLETE_TODO",
                payload: {
                    id
                }
            });
            break;
        default:
            console.log("This is odd.");
    }
}
...
    function renderContent() {
        ...
        return (
            <ul id="todos">
                {keys.map(key => {
                    ...
                    return (
                        <li key={key}>
                            <p className={isComplete ? "complete" : ""}>
                                {todo}
                            </p>
                            <div class="controls">
                                {!isComplete ? (
                                    <FontAwesomeIcon
                                        icon="check"
                                        title="Mark as Complete"
                                        className="control-icon"
                                        onClick={() =>
                                            controlHandler(key, "complete")
                                        }
                                    />
                                ) : null}
                            </div>
                        </li>
                    );
                })}
            </ul>
        );
    }
... 
```

 **就像完整的功能一样，我们将添加一个带有点击处理程序的删除图标，该处理程序过滤待办事项并更新我们的状态。下面是我们的应用程序的完整工作代码。我突出显示了为删除 todo 项而添加的部分。

你可以随时参考这个[沙盒](https://codesandbox.io/s/71j9q45y11)中的实例来更好地形象化它。

```
/*
    Author: Yomesh Gupta (https://www.twitter.com/yomeshgupta)
*/

import React, { useEffect, useState, useReducer } from 'react';
import ReactDOM from 'react-dom';
import { library } from '@fortawesome/fontawesome-svg-core';
import { faCheck, faTrash } from '@fortawesome/free-solid-svg-icons';
import { FontAwesomeIcon } from '@fortawesome/react-fontawesome';

import './styles.css';

library.add(faCheck, faTrash);

const initialState = {
    fetched: false,
    todos: {}
};

const TODOS = {
    1552406885681: {
        todo: 'Complete this blog post',
        isComplete: false
    },
    1552406885682: {
        todo: 'Add everything to this blog post',
        isComplete: false
    }
};

function reducer(state, action) {
    switch (action.type) {
        case 'REPLACE_TODOS':
            return { ...state, fetched: true, todos: action.payload };
        case 'UPDATE_TODOS': {
            return { ...state, todos: action.payload };
        }
        case 'ADD_TODO':
            return {
                ...state,
                todos: {
                    ...state.todos,
                    ...action.payload
                }
            };
        case 'COMPLETE_TODO':
            return {
                ...state,
                todos: {
                    ...state.todos,
                    [action.payload.id]: {
                        ...state.todos[action.payload.id],
                        isComplete: true
                    }
                }
            };
        default:
            return state;
    }
}

function Loader() {
    return <div id="loader">Loading...</div>;
}

function Empty() {
    return <div id="empty">Seems kind of empty here...</div>;
}

function Todos() {
    const [task, setTask] = useState('');
    const [state, dispatch] = useReducer(reducer, initialState);
    const { fetched, todos } = state;
    const keys = Object.keys(todos);

    // Setting up an effect
    useEffect(function() {
        function fetchData() {
            new Promise((resolve, reject) => {
                // mocking API call
                setTimeout(() => resolve(TODOS), 2000);
            }).then(response => {
                // Updating state variable
                dispatch({
                    type: 'REPLACE_TODOS',
                    payload: response
                });
            });
        }
        fetchData();
    }, []);

    function saveHandler(e) {
        e.preventDefault();
        dispatch({
            type: 'ADD_TODO',
            payload: {
                [+new Date()]: {
                    todo: task,
                    isComplete: false
                }
            }
        });
        setTask('');
    }

    function controlHandler(id, operation) {
        switch (operation) {
            case 'complete':
                dispatch({
                    type: 'COMPLETE_TODO',
                    payload: {
                        id
                    }
                });
                break;
            case 'delete': {
                const clonedTodos = { ...todos };
                delete clonedTodos[id];
                dispatch({
                    type: 'UPDATE_TODOS',
                    payload: clonedTodos
                });
                break;
            }
            default:
                console.log('This is odd.');
        }
    }

    function renderContent() {
        if (!fetched) {
            return <Loader />;
        } else if (!keys.length) {
            return <Empty />;
        }
        return (
            <ul id="todos">
                {keys.map(key => {
                    const value = todos[key];
                    const { isComplete, todo } = value;
                    return (
                        <li key={key}>
                            <p className={isComplete ? 'complete' : ''}>{todo}</p>
                            <div class="controls">
                                {!isComplete ? (
                                    <FontAwesomeIcon
                                        icon="check"
                                        title="Mark as Complete"
                                        className="control-icon"
                                        onClick={() => controlHandler(key, 'complete')}
                                    />
                                ) : null}
                                <FontAwesomeIcon
                                    icon="trash"
                                    title="Delete Todo"
                                    className="control-icon"
                                    onClick={() => controlHandler(key, 'delete')}
                                />
                            </div>
                        </li>
                    );
                })}
            </ul>
        );
    }

    return (
        <div className="wrapper">
            <form method="#" onSubmit={saveHandler}>
                <input
                    type="text"
                    onChange={e => setTask(e.target.value)}
                    value={task}
                    placeholder="What needs to be done?"
                />
                <input type="submit" value="Add" title="Add Todo" />
            </form>
            {renderContent()}
        </div>
    );
}
const rootElement = document.getElementById('root');
ReactDOM.render(<Todos />, rootElement); 
```

终于，我们的 app 完成了！唷！然而，我们可以继续实施更多的功能，如错误处理，更多的控制，到期日等！React 本身提供了更多的钩子，我们甚至可以编写自己的定制钩子！让我们在这篇博文的第二部分继续讨论这个问题。

快速链接:[工作沙盒](https://codesandbox.io/s/71j9q45y11) | [Github Repo](https://github.com/yomeshgupta/react-hooks-todo-app)

这个 Todo 应用程序的一个强大和更高级的版本，以及更多的功能，都附带了一个超级棒的 chrome 扩展，名为[backgroups](https://chrome.google.com/webstore/detail/backdrops/beanogjmmfajlfkfmlchaoamcoelddjf)。你应该去看看。

> Backdrops 是一个令人惊奇的 chrome 扩展，它可以让你无聊的 chrome 新标签页变得更加有趣和有用。点击查看[。](https://chrome.google.com/webstore/detail/backdrops/beanogjmmfajlfkfmlchaoamcoelddjf)

希望这篇文章对你有所帮助。原来贴[这里](https://devtools.tech/intro-to-react-hooks/)。请在这里随意分享您的反馈[。](https://www.twitter.com/yomeshgupta)**