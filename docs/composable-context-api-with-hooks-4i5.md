# 带挂钩的可组合上下文 API

> 原文：<https://dev.to/gabethere/composable-context-api-with-hooks-4i5>

React 的 ContextAPI 是使用 Redux 进行全局状态管理的一个很好的轻量级替代方案。

理解这一点很重要，不是每个组件都需要使用 React 的 ContextAPI，或者任何全局状态管理工具。理想情况下，组件应该尽可能长时间地以“功能性”无状态方式存在。例如，不携带任何状态，而是利用通过 props 传入的实时值。

例如:

```
 const UserNameDisplay = (props) => (<span>props.userName<span/>); 
```

这种无状态的设计使得测试更加容易，并强制将逻辑和状态保存在组件的父组件中。本质上，保持状态集中，以防止应用程序内的不同步状态。

然而，在我们的例子中，我们确实希望有一个包容的组件，我们可以利用它来为我们提供一种状态，以及一种跨任何需要它的组件改变该状态的方法。

如果我们以一个 TODO 应用程序为例，我们很可能知道我们可能需要在任何给定的时间保持对应用程序上 TODO 项目的引用。这使得主要顶级组件的子组件，比如说 Todo 组件，不必向下钻取`todos`的状态到每个子组件，然后每个需要`todos`的子组件将部分地需要向下钻取`todos`到链的更深处。

比如(不正确的做事方式):

```
 const SomeOtherChildComponent = ({todos}) => {
            return (
                <AnotherChildComponent todos={todos}/> // you get the idea by now ...
            )
    }

    const TodosMainComponent = () => {
        const todos = [];
        return (
            <SomeOtherChildComponent todos={todos}/>
        )

    } 
```

这是相当麻烦的。如果我们处理一个级别的组件深度，Prop drilling 是非常好的，但是，当我们需要多个级别的深度时，ContextAPI 将提供一种更好的方式将状态“传递”给`TodosMainComponent`的子组件。

这个想法是，我们有一个提供者组件来设置我们的状态，以及同样多的消费者组件来消费那个状态。

要点如下:

```
 <SomeContext.Provider value={someState}>

        <SomeComponent/>

    </SomeContext.Provider> 
```

理想情况下，我们希望有一种更“可组合”的方式来定义我们的上下文。

我们可以利用 React 钩子的概念来创建一个引入特定上下文状态的定制组件。在这种情况下，处于`todo`状态。 *[Stackblitz Todo 上下文示例](https://stackblitz.com/edit/react-todo-context-api)*

## 设置减速器和初始状态:

让我们首先定义我们的减速器结构:

```
 import { HYDRATE_TODOS } from "./actionTypes";

    export const initialState = {
      todos: []
    };

    const reducer = (state = initalState, { type, payload }) => {
      switch (type) {
        case HYDRATE_TODOS:
          return { ...state, todos: payload };
        default:
          return state;
      }
    };

    export default reducer; 
```

## 连接我们的组合提供者组件:

我们可以使用`useState`钩子定义`todos`，因为我们只是处理一个对象数组(单个值)，但是，为了扩展这个对象，以便向状态添加额外的属性/动作(添加、删除、更新等)，我们将从一个 reducer 开始。

```
 import React, { createContext, useReducer } from "react";
    import reducer, { initialState } from "./reducer"; // our reducer from above 
```

我们必须做的第一件事是确保我们正在创建一个反应上下文

```
 import React, { createContext, useReducer } from "react";
    import reducer, { initialState } from "./reducer"; // our reducer from above

    export const TodosContext = createContext(); // our context for todos 
```

现在，我们可以创建一个接受其他组件作为“道具”传入的组件。我们可以把这个组件看作是“父”组件，它将初始化我们的上下文并将上下文传递给子组件(传入的组件)。

```
 import React, { createContext, useReducer } from "react";
    import reducer, { initialState } from "./reducer"; // our reducer from above

    export const TodosContext = createContext(); // our context for todos

    export const TodosProvider = ({ children }) => {
      const [state, dispatch] = useReducer(reducer, initialState); // intialize our reducer
      const value = [state, dispatch]; // what we'll expose to all children components

      return (
        <TodosContext.Provider value={value}>{children}</TodosContext.Provider>
      );
    }; 
```

哦，嘿，看那个，我们基本上已经创建了一个可重用的组件，我们可以引入它来初始化我们的 todo 的上下文，并传递尽可能多的子元素。这与 React 路由器的工作方式类似。主路由器组件和嵌套在下面的子路由:

```
 <Router>
        <Route/>
        <Route/>
    </Router> 
```

理解这一点很重要，我们本质上是向所有子组件公开`state`和`dispatch`属性。这基本上允许我们的子组件通过向我们的`todos`减速器分派动作来改变`todo`状态，并且通过使用`state`道具来读取我们的`todos`。

这就是我们在上下文搭建方面所需要的。让我们使用它！

## 使用 TODO 提供程序组件:

在上面的例子中，我们将使用新的`TodoContext` :
重构`TodosMainComponent`及其`ChildComponent`来显示待办事项列表

```
 import React, { useContext, useEffect, Fragment } from 'react';
    import { TodoProvider, TodoContext } from './todos/contexts/TodoContext' // import our context provider
    import { HYDRATE_TODOS } from "./actionTypes";

    const TodoApp = () => {
        return(
            <Fragment>
                <TodoProvider> //remember, we've already setup this provider with the value and initial state
                    <TodosMainComponent/>
                </TodoProvider>
            </Fragment>
        )
    }

    const SomeOtherChildComponent = () => {
            const [{todos}, todoDispatch] = useContext(TodoContext); // we can dispatch events or leverage the todo state here

            const displayItems = (todos) => todos.map(todo =>
          <li key={todo.id.toString()}>{todo.body}</li>
      );

          return (
            <ul>{displayItems(todos)}</ul>
          )
    }

    const TodosMainComponent = () => {
        const someTodoList = [{id: 1, body: 'Some todo'}];
        const [{ todos }, todosDispatch] = useContext(TodoContext);

        useEffect(()=> {
            todoDispatch({type: HYDRATE_TODOS, payload: someTodoList});
        }, []);

        return (
            <SomeOtherChildComponent/>
        )
    } 
```

## 结论

显然，这是一个非常简单的概念示例，但是，在实际应用中，在特定的上下文中包装一组路由可能更合适。例如，你可以这样做:

```
 <TodoProvider>
            <Route path="/" exact component={TodoMainComponent} />
            <Route path="/todos/add" exact component={Add} />
    </TodoProvider> 
```

这将允许您从 Add 组件将 todo 插入到您的状态中，并避免必须返回到后端来刷新本地状态数据。

我们还需要记住，如果有任何状态变化，React 将急切地重新呈现您的组件。因此，如果您有一个嵌套在一个上下文中的子组件的非常大的子树，那么可能值得考虑拆分您的状态，从而使多个上下文具有一个较小的子组件集。

[Kent C Dodds 还提出了一种替代解决方案来解决复杂、快速变化的上下文值所带来的性能问题。](https://kentcdodds.com/blog/how-to-optimize-your-context-value)这里的想法是，我们将我们的实际状态拆分到它自己的提供者中，并将我们的 reducer 调度功能拆分到另一个提供者中。仅允许正在读取`todo`状态的组件呈现，而不允许任何仅改变状态的组件呈现。如果你有功能组件，如按钮、菜单显示、导航页脚等，这可能是一个很好的解决方案。

如果您对提高 React 的上下文 API 在大型子树上的性能的其他解决方案更感兴趣，请查看 Dan Abramov 在这方面提出的解决方案。

## 资源

*这篇文章的灵感来自 Eduardo Robelos 关于 [React Hooks:如何创建和更新上下文的文章。供应商](https://dev.to/oieduardorabelo/react-hooks-how-to-create-and-update-contextprovider-1f68)*