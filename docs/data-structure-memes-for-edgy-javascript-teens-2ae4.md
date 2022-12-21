# 前卫 JavaScript 青少年的数据结构迷因

> 原文：<https://dev.to/jamestthompson3/data-structure-memes-for-edgy-javascript-teens-2ae4>

前端开发人员越来越多地面临复杂状态和数据管理的挑战。我们会遇到因数据管理变得过于单一或过于分散、组件更新过于频繁，或者我们花费大量开发时间试图发现数据是如何通过整个应用程序传递的而导致的问题。我们的组件应该只消耗呈现其子组件所需的最少量的信息。在我们的应用程序中，如何以一种既易于使用又易于理解的方式实现这一点呢？我认为答案是在 react 领域之外进行数据管理，并使用 react 挂钩来挂钩相关的数据更新。

React 从 16.8.0 开始就附带了钩子，这导致开发人员放弃类组件，转而使用带有钩子的功能组件。您可能也考虑过通过使用 hooks 和 react context 来抛弃像 redux 这样的库。虽然最初看起来是一个很好的想法(我用这种方式重新编写了我们应用程序的很大一部分)，但您会发现钩子和上下文会导致不必要的重新呈现，并增加代码库的逻辑复杂性。

如果你想直接跳到代码，[这里是沙箱](https://codesandbox.io/s/obsv-hooks-382xk?fontsize=14)

### 把我藏在外面:怎么样？

大多数 react 应用程序需要从服务器获取数据，并显示给应用程序的用户。随着应用程序规模和范围的增长，在应用程序中快速存储数据成为一个挑战。如果您检查几乎任何规模的 react 应用程序，您可能会发现不同解决方案的组合。使用像 redux 或 mobx 这样的第三方库很流行，但有时这些数据存储在本地组件状态中。

在每种方法中都需要考虑权衡:使用外部库意味着编写更多的代码来更新和使用应用程序中的数据，导致难以遵循逻辑；将应用程序数据保持在组件状态意味着当组件被卸载时它会消失，迫使我们重新获取数据或将组件放在渲染树中更高的位置(通常这是“容器”组件的模式)。

在许多情况下，状态和数据管理可以而且应该分开。应用程序中可供使用的数据不一定反映在使用该数据的组件的当前状态中。这方面的一个例子是在 redux 中存储数据。当我们将数据从服务器放入 reducer 时，我们现在可以在连接到 redux 存储时访问这些数据。使用该数据的组件可能有几种状态和状态转换，但这不会改变数据的可用性。

我认为，我们可以将数据管理移出 react，从而为我们带来以下好处:

0:编写和推理都有简单的 redux、sagas、mobx 等的问题。是锅炉板代码并且难以遵循逻辑)。

1:允许我们在必要时将 UI 绑定到数据，但不允许我们的数据依赖于我们的渲染库(使用 react 上下文意味着我们的数据必须遵循 react 的约束)

2:仅允许对与当前组件相关的*数据进行底层更改，以自动触发对 react 调度器的更新请求。*

### 大 OOF:为什么语境复杂

将数据存储在 react 上下文中会导致复杂性和重新呈现，这会损害应用程序的性能并降低代码库的可维护性。关于这一点，尼克·格拉芙有一个精彩的演讲，所以如果你想听他的话，可以去看看。然而，深入研究上下文，在寻找管理应用程序所需数据的解决方案时，问题很快就出现了。让我们来挖掘一些例子。

使用以下收集的数据:

```
[  {  "name":  "sam",  "id":  "1987ea87gde302",  "likes":  [  {  "id":  0,  "item":  "cars"  },  {  "id":  1,  "item":  "dogs"  },  {  "id":  2,  "item":  "Bruce Springsteen"  },  {  "id":  3,  "item":  "mowing the lawn"  }  ],  "dislikes":  [  {  "id":  0,  "item":  "vegetables"  },  {  "id":  1,  "item":  "income tax"  },  {  "id":  2,  "item":  "existential crises"  }  ]  },  ...  ] 
```

如果我们想把它存储在 react 上下文中并用钩子把它取出来，我们应该这样做:

```
// Assuming the data structure above, a list of user objects is named userList
const UserContext = React.createContext(userList)

function UserListView() {
  const listOfUsers = React.useContext(UserContext)

  return listOfUsers.map(user => <p>{user.name}</p>) } 
```

这太棒了！直到您需要更新用户列表，在这种情况下，您可能需要创建一个自定义组件，该组件公开在上下文中更新和检索值的方法:

```
const UserContext = React.createContext([])
function UserContextHolder({children}) {
  const [users, setUsers] = React.useState([])
  return (
    <UserContext.Provider value={{users, setUsers}}>
      {children}
    </UserContext.Provider>
  )
} 
```

似乎很简单！但是，这个组件必须在 react DOM 树中占据足够高的位置，这样所有使用它的组件都可以成为它的子组件。这意味着每当*该上下文的任何*值被更新时，该组件的任何其他子组件将被强制重新呈现！此外，如果我们尝试并重用这个上下文来存储与我们的用户列表相关的其他内容，例如一个选定的用户或一组选定的用户，我们将再次强制所有使用该信息的组件成为该上下文的子组件，并在任何数据更改时强制它们重新呈现。

为了说明这一点，假设我们有一个 UI，它显示我们的用户列表，然后显示所选用户的好恶列表。如果我们将所有这些信息存储在上下文中，当使用这个 UI 时，我们会看到许多呈现事件:

===主列表===
0:上下文装载，我们的用户列表通过 XHR 请求更新。

1:默认选定用户从用户列表中选择，并在上下文中设置

2:每次选择新的用户时，上下文都会更新，组件也会重新呈现

3:来自喜欢列表的更新
4:来自不喜欢列表的更新

===喜欢列表===
0:从主列表中选择的用户导致初始渲染
1:选择的用户更新
2:更新自身
3:更新不喜欢列表

===不喜欢列表===
0:从主列表中选择的用户导致初始渲染
1:选择的用户更新
2:更新自身
3:更新喜欢列表

注意在上下文中，即使是对不相关数据的更新也会导致重新渲染。我们的主列表只显示用户的名字，当特定用户的好恶信息更新时，不应该强制重新显示。该模型还假设这三个列表是上下文组件的唯一子组件，但是在现实世界中，我们的应用程序往往要复杂一些。例如，如果我们添加用于添加、删除和编辑喜欢和不喜欢的按钮组件，所有这些组件也将被重新呈现。

想象一下，如果我们向用户对象添加属性——例如，如果我们想显示用户是否在线——或者我们有一个递归数据结构，每个用户都有一个朋友列表，这些朋友又是用户对象。由于用户列表可能会发生许多变化，所以每次添加、删除或修改列表的一部分时，我们都会增加每个组件的重新呈现次数。在 react 上下文中存储数据会在组件之间创建不必要的链接，并迫使我们将每个组件包装在`useMemo`中以优化呈现。

### 别忘了点赞订阅:使用 RXJS 构建自定义数据结构

使用 react context 的一个方便之处是您可以免费获得更新！每当一个上下文值被更新时，所有使用它的组件及其子组件都需要重新呈现。当您考虑拥有一个真正的数据驱动的 UI 时，这种行为是很棒的，但是当您考虑到上面介绍的复杂性时，就不那么棒了。那么，我们如何保持这种自动更新行为，同时将组件呈现减少到只依赖于组件本身直接使用的数据呢？输入 rxjs。

如果你不熟悉 rxjs 或反应式编程，我建议你去看看 Andre Staltz 的要点，它涵盖了反应式编程的一些原则  。Rxjs 主题是组件订阅数据更改的一种方式。它们提供了一个干净的 API 来接收和交互对数据存储的更新。然而，将数据从可观察对象直接传输到组件将与未来的 react 更新不兼容，因为 react 更新是基于拉的系统，而可观察对象是基于推的 T4。使用 react 提供的与更新状态相关的钩子，我们订阅数据中的变化，而不直接将更新推送到我们的组件，而是从 react 调度器请求更新。

使用上面定义的用户列表，我们可以构建一个自定义存储来包含我们的数据，并公开更新和订阅更新的方法。通过在 react 之外创建这个数据结构，我们允许从我们的 UI 独立地访问它的方法，为我们创建自己有用的数据管理实用程序提供了一个强大的起点。

让我们首先创建一个存储数据和订阅的基本结构:

```
import { Subject } from 'rxjs'

class DataStore {
  subjects = new Map()
  store = new Map()

  getSubscription = key => this.subjects.get(key)
  getValue = key => this.store.get(key)

  createSubscription = key => {
    const subject = this.subjects.get(key)
    const storeValue = this.store.get(key)
    if (subject && storeValue) return subject
    this.subjects.set(key, new Subject())
    this.store.set(key, undefined)
    return this.subjects.get(key)
  }

  setValue = (key, value) => {
    this.store.set(key, value)
    this.subjects.get(key).next(value)
  }

  removeSubscription = key => {
    const selectedSubscription = this.subjects.get(key)
    const selectedValue = this.store.get(key)
    if (selectedSubscription) {
      selectedSubscription.complete()
      this.subjects.delete(key)
    } else {
      throw new Error('Cannot find subscription %s', key)
    }
    if (selectedValue) {
      this.store.delete(key)
    } else {
      throw new Error('Cannot find store key %s', key)
    }
  }
} 
```

与我们使用上下文的例子不同，这种结构完全不知道我们的数据的形状，这赋予了它在整个应用程序中重用的灵活性。像这样的数据结构允许我们存储几乎任何东西(除了重复的键)，这意味着我们可以有许多`DataStore`对象的实例，或者我们可以有一个包含所有应用程序数据的整体存储(尽管我不确定这是否是最好的主意)。

从`DataStore`对象公开的方法中，我们可以创建一整套用于消费、更新、自省和订阅数据的工具。让我们看看如何在 react 组件中直接使用它们。

首先，在组件外部实例化一个`DataStore`对象的实例，并为我们的用户列表创建一个订阅。

```
// in UserRender.jsx

const USER_STORE = new DataStore()
USER_STORE.createSubscription('userList') 
```

在我们的组件逻辑中，我们可以创建从存储中消费数据的方法。当数据改变时，我们希望在这里重新渲染。

```
// in UserRender.jsx

const USER_STORE = new DataStore()
USER_STORE.createSubscription('userList')

const fetchAndStoreUserList = () => {
  fetchUsers().then(users => USER_STORE.setValue('userList', users))
}

export function UserRender() {
  const [userList, setUserList] = React.useState([])
  USER_STORE.getSubscription('userList').subscribe(setUserList)
  React.useEffect(fetchAndStoreUserList, [])

  return userList.map(user => <p>{user.name}</p>) } 
```

这就是我们如何将用户列表从`DataStore`中取出并放入我们的组件中。这利用了 react 的`useState`功能，允许我们从 react 请求更新，而不是立即从我们的订阅推送组件更新。将我们订阅的输出通过管道传输到`useState`还允许 react 进行批量渲染，如果用户列表是从 web 套接字连接或任何其他快速触发状态更新的方法更新的，这就很方便了。

此时，您可能会想，“这看起来不错，但是当我调用`USER_STORE.setValue`时，我不还是必须重新呈现主列表吗？”。答案是肯定的。尽管我们已经将应用程序数据的管理转移到 react 之外，但我们仍然被绑定到由`useState`调用的更新周期，因为它是作为回调传递给`USER_STORE.setValue`的。这是钩子真正开始发光的地方！

### 按 F 键进行选择？

如果您使用过 redux，您很可能会遇到选择器。对于那些不熟悉这个主题的人来说，选择器允许我们隔离(或选择)应用程序数据的一部分，并且只在那部分数据发生变化时才启动渲染。通过使用钩子，我们将`USER_STORE`的功能包装成使用一个选择器，该选择器只在用户列表改变时更新`UserRender`组件。这意味着我们可以更新部分用户数据(比如喜欢或不喜欢的列表)，而不必重新呈现不直接使用这些数据的组件。创建以选择器作为参数的钩子也有助于我们从冗余的代码库过渡，并允许重用现有的代码。

```
export function useSelector(store, subscriptionKey, selector) {
  store.getSubscription(subscriptionKey).subscribe(selector)
} 
```

由于我们创建了`DataStore`，所以`useSelector`的代码很简单。我们只是想传递给它我们想从中读取的存储库(在本例中是`USER_STORE`)、我们感兴趣的订阅的键(`userList`)，以及每当一个新值被推送到流中时将被调用的选择器函数。我们现在可以在新的数据结构中重用 redux 选择器了！

我们希望序列化用户列表的键，并且只在这些键改变时更新`UserRender`组件。为此，我们需要首先创建我们的用户列表选择器:

```
function memoUsers() {
  const cache = {}
  return function(updateUser) {
    return function(userList: User[]) {
      const key = JSON.stringify(userList.map(user => user.user))
      if (cache[key]) {
        // don't call to re-render
      } else {
        cache[key] = key
        updateUser(userList)
      }
    }
  }
} 
```

现在，`memoUsers`可以传递给我们的`useSelector`钩子，用来代替我们的`userSubscription`。

```
// in UserRender.jsx

import { useSelector } from './hooks'

const USER_STORE = new DataStore()
USER_STORE.createSubscription('userList')

const fetchAndStoreUserList = () => {
  fetchUsers().then(users => USER_STORE.setValue('userList', users))
}

function memoUsers() {
  const cache = {}
  return function(updateUser) {
    return function(userList: User[]) {
      const key = JSON.stringify(userList.map(user => user.user))
      if (cache[key]) {
        // don't call to re-render
      } else {
        cache[key] = key
        updateUser(userList)
      }
    }
  }
}

const cache = memoUsers()

export function UserRender() {
  const [userList, setUserList] = React.useState([])
  const setCachedUserList = cache(setUserList)
  useSelector(USER_STORE, 'userList', setCachedUserList)
  React.useEffect(fetchAndStoreUserList, [])

  return userList.map(user => <p>{user.name}</p>) } 
```

现在，只有当我们在列表中添加或删除了一个用户，或者更改了所选的用户时,`UserRender`组件才会更新，而不是当我们更改了一个特定用户的属性时。组件本身很简单，应用程序数据的繁重工作由我们的`DataStore`处理。我们不需要创建动作和减速器，也不需要使用高阶组件。

通过扩展`DataStore`对象，您可以做更多的工作来改进对数据存储的写入。扩展`DataStore`应该基于每个用例，因为为每个遇到的用例向`DataStore`添加一个新方法是一种反模式。更好的方法是创建一个新的对象，扩展`DataStore`并添加特定情况下所需的方法。这里的关键是我们保持这些数据结构的灵活性，因为我们的数据结构与反应无关，定制的数据结构应该易于读取和编写。

### 状态管理！==数据管理:不要@我(或者做，我希望听到您的反馈)

在 react 之外进行数据管理为我们控制外部触发的组件呈现提供了基础。它还允许我们开发在应用程序中容易重用的模式。它减轻了 react 的缓存和操作负担。通过钩子，我们可以很容易地钩住我们的定制数据结构，这允许我们的组件只消费它们需要的东西，并且只对与它们相关的更新做出反应。

关注我们的组件如何在我们的应用程序中使用数据，通过允许每个组件独立地选择如何以及何时读取和写入共享的数据结构来防止脆弱的架构。与使用上下文不同，我们不必考虑我们的组件在 react DOM 树中的位置，或者用`useMemo`包装组件来优化渲染周期。

高效的数据管理归结为简单性。您能可靠地跟踪通过您的应用程序的数据流吗？您能自省您的数据吗？当您的组件没有使用的数据发生变化时，您的组件会被强制更新吗？这些是在构建应用程序时应该问的问题。对于数据管理来说，不存在放之四海而皆准的解决方案，但是我希望您能考虑简化并尝试这里谈到的一些概念。