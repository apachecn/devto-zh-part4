# 对你的反应使用状态管理很有趣

> 原文：<https://dev.to/denniscual/fun-to-use-state-management-for-your-react-46g8>

当管理你的反应状态时，你的状态管理器应该只给你简单的工作去做，即使你在维护巨大的状态。小是新的大。确实是的！它必须只给你小的工具来管理它，这就足够了。这意味着它必须有最少的 API。

给你这种功能，并以有趣和简单的方式使用它。这就像是更大版本的`React.useState`钩子，但是管理不同的状态。它也有较小的 API 面，所以你不需要学习很多东西，只是为了让它正确。

让我们来看看如何使用这个令人敬畏的库:

#### 1 -配置您的商店

注:`easy-react-state`用的是`immerjs`。因此，在更新状态时，可以使用任何语法。这非常方便，尤其是当您更改嵌套属性的值时。你可以像`state.user.name = 'Zion'`一样使用 mutator 语法。

```
const configAppStore = {
  todos: {
    initialState: [],
    setters: state => ({
      addTodo(todo) {
        state.push(todo)
        return state
      },
    }),
  },
} 
```

#### 2 -基于您的商店创建状态管理器

注意:返回的`useSelector`将根据用户需要的数据订阅存储，并且仅当订阅的数据发生变化(即使根状态被更新)时，才会重新呈现消费者组件。

```
const [useAppSelector, appSetters] = createStateManager(configAppStore) 
```

#### 3 -消耗到您的 React 组件

注意:我们不需要一个`Provider`来消费商店。只需创建一个管理器，然后就可以直接使用了。

```
const App = () => {
  const todos = useAppSelector(state => state.todos)
  console.log('todos', todos)
  return (
    <div>
      <h3>Todos Control</h3>
      <button
        onClick={() => {
          const todo = {
            id: `todo-${Date.now()}`,
            label: `Todo ${Date.now()}`,
          }
          appSetters.todos.addTodo(todo)
        }}
      >
        Add todo
      </button>
    </div>
  )
} 
```

很简单，对吧？异步更新怎么样？这很简单。

因为`setters`只是在`Component`之外创建的对象，所以你可以在任何地方调用它。例如，在`async`函数中调用它:

```
const [useAppSelector, appSetters] = createStateManager(configAppStore)

async function fetchUsers() {
  appSetters.users.loading()
  try {
    const res = await apiUsers()
    appSetters.users.setUsers(res)
  } catch (err) {
    appSetters.users.setError(err)
  }
} 
```

就是这样！如果你有兴趣并且想要尝试，[你可以查看它的 github repo 了解更多信息](https://github.com/ombori/easy-react-state)。当然，它是开源的，欢迎反馈和投稿！