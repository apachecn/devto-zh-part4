# 新的 Redux 挂钩:前后对比。他们更好吗？

> 原文：<https://dev.to/gsto/new-redux-hooks-a-before-and-after-comparison-are-they-better-loj>

今天 react-redux 团队发布了 7.1.0 版本，其中[为 react-redux](https://react-redux.js.org/api/hooks) 增加了钩子！这里有一个快速的比较，它如何改变你写组件的方式。

## 首先简单介绍一下新玩具

*   `useSelector`:传入一个以状态为自变量的函数，返回值。用于从 state 获取单个值。可以替代 mapStateToProps。
*   `useDispatch`:返回对[调度](https://react-redux.js.org/using-react-redux/connect-mapdispatch)对象的引用。它可以替代 mapDispatchToProps。
*   `useStore`:返回商店的一个实例。一般不推荐。

## 用连接举例

一个示例组件，用于存储查询以及何时提交表单进行搜索。我想让这个例子简单一些，所以在获取结果的部分发挥你的想象力。

```
import React from 'react'
import { connect } from 'react-redux'
const defaultSearchBar = ({ query, updateQuery, handleSearch }) => {
  const handleSubmit = (e) => {
    e.preventDefault()
    handleSearch(query)
  }
  const handleChange = e => updateQuery(e.target.value)
  return (
    <form onSubmit={handleSubmit}>
      <input
        name="search"
        value={query}
        onChange={handleChange}
      />
    </form>
  )
}
const mapStateToProps = state => ({
  query: state.query,
})
const mapDispatchToProps = dispatch => ({
  updateQuery: newQuery => dispatch({ type: 'UPDATE_QUERY', payload: newQuery }),
  handleSearch: newSearch => dispatch({ type: 'NEW_SEARCH', payload: newSearch }),
})
const connectedSearchBar = connect(
  mapStateToProps,
  mapDispatchToProps,
)(defaultSearchBar) 
```

Enter fullscreen mode Exit fullscreen mode

## 带挂钩的新例子

在我们的新示例中，我们有一些不同:我们完全消除了 connect 函数、mapStateToProps 和 mapDispatchToProps。这意味着我们的组件不再直接接受道具。现在，我们的表单看起来像这样:

```
import React from 'react'
import { useSelector, useDispatch } from 'react-redux'

const hookedSearchBar = () => {
  const dispatch = useDispatch()
  const query = useSelector(state => state.query)
  const handleSubmit = (e) => {
    e.preventDefault()
    dispatch({ type: 'NEW_SEARCH', payload: query })
  }
  const handleChange = e => dispatch({ type: 'UPDATE_QUERY', payload: e.target.value })

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="search"
        value={query}
        onChange={handleChange}
      />
    </form>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建自己的定制挂钩

如果您有经常在组件之间共享的代码，您可以创建一个自定义挂钩来将所有的功能放在一起。这里有一个例子，我们将表单中特定于 redux 的部分隔离到它自己的钩子中:

```
useSearchQuery = () => {
  const dispatch = useDispatch()
  const query = useSelector(state => state.query)
  const updateQuery = query => dispatch({ type: 'UPDATE_QUERY', payload: query })
  const updateSearch = search => dispatch({ type: 'NEW_SEARCH', payload: search })
  return { query, updateQuery, updateSearch }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 是否应该进行切换？

上面创建 redux 挂钩的能力很有趣，但我也担心这会使代码更难测试，因为测试这些组件已经非常简单了。我不赞成这两种方式，但是我希望这种比较能让你更容易对你的代码库做出明智的决定。

你对新的 react-redux 挂钩有什么看法？我很想在下面的评论中听到你的意见！