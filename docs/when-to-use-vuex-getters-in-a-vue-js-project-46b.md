# 何时在 Vue.js 项目中使用 Vuex Getters

> 原文：<https://dev.to/drewtownchi/when-to-use-vuex-getters-in-a-vue-js-project-46b>

如果你喜欢这篇文章，请考虑在 [Twitter](https://www.twitter.com/drewtown_chi) 上关注我或者访问我的[网站](https://www.drewtown.dev)。

[Vuex](https://vuex.vuejs.org) 的特性之一是能够解析和缓存来自存储状态的值，以便通过 getters 快速检索。 [Vuex getters](https://vuex.vuejs.org/guide/getters.html) 等同于 Vue 的计算属性，因为它们都对依赖关系的变化做出反应，并被缓存以提高性能。我在初学者学习 Vue 和 Vuex 时经常看到的一个问题是，什么时候在他们的项目中使用 getters。经常有一种误解，认为 Vuex 必须用于从存储中检索所有数据，并且有过度使用 getters 的倾向。通常，从存储中检索状态并在组件中对该状态执行操作是一个更好的解决方案，需要的样板文件更少。

何时使用直接访问状态和何时使用 getters 之间的界限是一条灰色细线。

## 当接入状态足够好时

我的一般经验是，每当我需要来自 Vuex 状态的变量的整个值时，我将直接从状态中检索它。一些例子中，你可能需要一个布尔值，一个字符串，一个完整的对象或者一个完整的数组。当您从状态中检索整个变量时，通过直接从状态中访问它来检索整个值是有意义的。

```
export default {
  computed: { 
    isOpen() {
      return this.$store.state.isOpen; 
    }
  }
} 
```

将这些值的检索包装在一个 getter 中没有什么价值。组件中的 computed 属性将缓存结果，还将监视依赖项(在本例中是存储的状态)是否有任何变化。您获得了 getter 的好处，而没有 Vuex 所需的样板文件。在这些情况下，通过创建一个 getter，您实际上创建了两个不同的存储访问点，它们返回完全相同的值。

新手开始使用 Vuex 的倾向是什么都用，用它所有的特性。乍一看，这似乎很聪明。你所有的状态、突变、动作和获取器都在一个集中的地方，这应该很容易推理。你会很快意识到添加不必要的样板文件是 Vuex 所基于的通量模式的一个大错误。所需的额外代码对于在某些情况下提取的值来说是令人厌烦的，例如上面概述的情况。

在 Vuex 是最强和最好的选择时使用它，但避免过度使用，因为它是可用的。

## 那么什么时候使用吸气剂呢？

当您需要访问状态**和**过滤或以某种方式操作数据时，大多数时候 getters 是最好的选择。当您需要访问状态并在多个组件中使用相同的过滤和解析时，getter 甚至更好。

> Getters 为在一个集中的地方包含和重用数据逻辑提供了一个很好的接口。

我们来看一个需要提供不同视图的待办应用。某些视图可能只需要显示所有待办事项的列表，而其他视图可能需要按照待办事项的截止日期对它们进行排序。

```
getters: {
  openTodos: state => {
    return state.todos.filter(todo => !todo.done);
  },
  sortedTodos: state => {
    //remember sort mutates the original array so, copy it first with a spread.
    return [...state.todos].sort((a, b) => a.dueDate.localeCompare(b.dueDate));
  }
} 
```

在 Vuex 中实现过滤和排序逻辑允许您在应用程序中集中和组织数据操作。这有助于让组件更少关心如何操作数据，并允许更容易的重构。当逻辑被改变或数据被更新时，需要更新的地方更少，从而减少了更新代码所需的工作。

## 在 getters 中使用变量

getters 的另一个好用途是通过 id 从数组中检索对象项。

```
getters: {
  getTodoById: state => id => {
    return state.todos.find(todo => todo.id === id);
  }
} 
```

因为 getter 可以通过[方法样式 access](https://vuex.vuejs.org/guide/getters.html#method-style-access) 接受一个变量，所以 getter 是一种集中和组织数据访问的便捷方式，即使您需要提供参数。可以用一个方法包含所有需要的逻辑，而不是让查找逻辑分布在许多组件和文件中。

## 结论

Getters 是一个很好的工具，但不是每个问题都是钉子。当需要提取存储中的部分状态或在检索数据之前操作数据时，经常使用它们。集中逻辑将减少代码重用和重复的数量，代价是增加一些样板文件。在这些情况下，收益远远超过成本。