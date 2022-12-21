# 测试 API 调用

> 原文：<https://dev.to/ryands17/testing-api-calls-88i>

在第二部分中，我们将学习如何测试从 API 获取数据并在 UI 中呈现数据的组件。

这是一个简单的`Users`组件。

```
import React, { useEffect, useState } from 'react'
import { User } from 'types/users'
import { getUsers } from 'services/users'

const Users: React.FC = () => {
  let [users, setUsers] = useState<User[]>([])
  let [loading, setLoading] = useState(false)

  useEffect(() => {
    setLoading(true)
    getUsers()
      .then(users => setUsers(users))
      .catch(console.error)
      .then(() => setLoading(false))
  }, [])

  return loading ? (
    <p aria-label="loading">Loading ...</p>
  ) : (
    <ul style={{ listStyle: 'none' }}>
      {users.map(user => (
        <li key={user.id}>
          {user.name} ({user.email})
        </li>
      ))}
    </ul>
  )
}

export default Users 
```

Enter fullscreen mode Exit fullscreen mode

在这里的`useEffect`钩子中，我调用了`getUsers`方法，并根据从 API 接收数据的时间设置了`loading`和`users`状态。根据这一点，我们设置了一个*加载*指示器，在获取用户之后，我们在一个列表中呈现一些用户的详细信息。

***注意:*** 如果你不熟悉钩子，那么你可以用你在类组件中正常定义的初始状态替换`useState`调用，用`componentDidMount`替换`useEffect`方法。

这就是`getUsers`法。

```
export const getUsers = () => {
  return fetch('https://jsonplaceholder.typicode.com/users').then(res =>
    res.json()
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我只是使用 [JSONPlaceholder](https://jsonplaceholder.typicode.com/) 来获取一些假用户。在这个测试中，我们将检查加载文本是否出现，以及在进行 API 调用后，用户是否可见。

现在，您的测试应该是隔离的，因此无论何时运行，从服务器或任何第三方服务调用实际的 API 都是依赖和低效的，不符合隔离原则。因此，我们应该模拟我们的 API 请求并返回我们自己的样本响应。

为此，我使用了 [react-mock](https://github.com/skidding/react-mock) 包，它提供了一个方便的 API 来模拟 fetch 请求。

首先，我们添加所需的导入，并创建一个要返回的示例用户数组。

```
import React from 'react'
import { render } from '@testing-library/react'
import { FetchMock } from '@react-mock/fetch'
import Users from './Users'
import { User } from 'types/users'

const users: Partial<User>[] = [
  {
    id: 1,
    name: 'Leanne Graham',
    email: 'Sincere@april.biz',
  },
  {
    id: 2,
    name: 'Ervin Howell',
    email: 'Shanna@melissa.tv',
  },
] 
```

Enter fullscreen mode Exit fullscreen mode

***注:*** 注意渲染之外导入的东西，即`waitForElement`。这正是我们在任何异步操作之后断言元素是否在 dom 中所需的方法。

其次，我们创建一个助手方法，它使用`FetchMock`组件来模拟我们的 API。

```
const renderUsers = () => {
  return render(
    <FetchMock
      matcher="https://jsonplaceholder.typicode.com/users"
      response={users}
    >
      <Users />
    </FetchMock>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们在`matcher`属性中提供了 api 的 url，而`response`属性包含了我们正在模仿的用户数据。

***注意:*** 我没有包括 API 返回的所有字段，只是包括了字段的一个子集，特别是那些在组件中呈现的字段。

最后，我们如下编写我们的`test`块。

```
test(`should render the users list`, async () => {
  const { getByLabelText, findByRole } = renderUsers()
  expect(getByLabelText('loading')).toBeInTheDocument()

  let userList = await findByRole('list')
  expect(userList.children.length).toEqual(users.length)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这就是有趣的地方。

第一行很简单，用`FetchMock`包装器呈现用户组件，以获得查询组件元素的`getByLabelText`方法。

第二行断言加载文本是否显示在 UI 中。这是使用`toBeInTheDocument`匹配器完成的，并使用我们在`p`标签上添加的`aria-label`进行匹配。

***注:*** `toBeInTheDocument`不是原生的 Jest matcher，而是来自库 [jest-dom](https://github.com/testing-library/jest-dom) 。我们通过在`src`文件夹中创建一个`setupTests.ts`文件并添加这一行`import '@testing-library/jest-dom/extend-expect'`来使用它。这将自动添加我们可以和`expect`一起使用的 DOM 匹配器。

第三行是我们使用`findByRole`方法获取列表的地方。

```
let userList = await findByRole('list') 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里使用了`await`,因为这个方法返回一个承诺，并接受一个返回 HTML 元素的匹配器(以角色的形式)。在我们的模拟 API 返回我们提供的响应之前，它将等待指定的 DOM 元素，即我们在其中呈现用户列表的`ul`标签。

在我们的组件中，在 API 返回一个成功的响应后，加载内容被替换为用户列表。所以`findByRole`将检查 DOM 中的元素，直到它可用，如果不可用，它将抛出一个错误。

由于我们的模拟 API 是成功的，`findByRole`将获得所需的元素，即`ul`标签。

在测试的第四行也是最后一行，我们断言呈现的列表的长度是否等于我们传递给模拟 API 的样本数据的长度。

```
expect(userList.children.length).toEqual(users.length) 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行`yarn test`或者`npm test`，你会看到你的测试通过了！继续使用`yarn start`或`npm start`在浏览器中运行您的应用程序，并在短时间内看到加载指示器，然后看到用户被渲染。

上例中的仓库是这里的[和](https://github.com/ryands17/testing-react)。它包括了本系列前一篇文章中的例子，还将包括更多用例的例子。

***注意* :** 正如 Kent 在评论中提到的，我们可以在我们的测试中添加另一行来确保`ul`已经正确地呈现了用户的电子邮件，并向我们保证无论我们传递了什么，我们的用户列表都会得到呈现。

为此，有一种类型的快照在开玩笑，[内联快照](https://jestjs.io/docs/en/snapshot-testing#inline-snapshots)！与外部快照不同，内联快照直接将正在呈现的内容写入您的测试，而不是创建外部`.snap`文件，为此，您只需将这行代码添加到您的测试中。

```
expect(userList.textContent).toMatchInlineSnapshot() 
```

Enter fullscreen mode Exit fullscreen mode

Jest 会自动填充`toMatchInlineSnapshot`方法中`ul`标签的内容。所以在您保存测试之后，它应该会用您已经通过的列表进行更新。甜蜜的权利！

继续修改我们传递的示例用户列表，保存文件并注意这个方法中反映的变化。

如果您得到一个失败的测试，按`u`来更新快照，以便它得到您对用户列表所做的最新更改。

感谢您的阅读！