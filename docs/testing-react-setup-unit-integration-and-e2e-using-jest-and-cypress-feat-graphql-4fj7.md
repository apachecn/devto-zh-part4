# 使用 Jest 和 Cypress 测试 React -设置、单元、集成和 E2E(专长。GraphQL)

> 原文：<https://dev.to/rxassim/testing-react-setup-unit-integration-and-e2e-using-jest-and-cypress-feat-graphql-4fj7>

*注:这是来自[我的时事通讯](https://www.getdrip.com/forms/14582623/submissions/new)和[博客](https://www.assim.me/testing-react)的交叉帖子。我会在每封邮件发出一周后公布。订阅以在您的收件箱中更早地获得更多类似的内容！💌*

嗨！这是一个指南/备忘单，当我想为一个项目编写测试时，我会回来阅读。

我想这可能会帮助其他开发人员，所以给你😁

# 设置

## 安装 jest、cypress 和 helper 库

```
yarn add jest @testing-library/react @testing-library/jest-dom -D 
```

Enter fullscreen mode Exit fullscreen mode

## 配置

在本节中，我们将配置 Jest 和 Cypress

### 是

让我们在根目录下为 Jest 创建一个配置文件:

```
module.exports = {
  // location.href will have this value
  testURL: 'https://example.com',
  // Add here folders to ignore
  testPathIgnorePatterns: ['/node_modules/'],
  setupTestFrameworkScriptFile: require.resolve(
    './test/setup.js'
  ),
  // path to components/modules to test
  modulePaths: ['<rootDir>/src'],
  moduleNameMapper: {
    // mock files that jest doesn't support like CSS and SVG files
    '\\.css$': '<rootDir>/test/module-mock.js',
    '\\.svg$': '<rootDir>/test/module-mock.js',
  },
  // collect coverage report from only the js files inside src
  collectCoverageFrom: ['**/src/**/*.js'],
  coverageThreshold: {
    global: {
      // 20 is just an example
      // you can change it to any value you want (below 100)
      statements: 20,
      branches: 20,
      functions: 20,
      lines: 20,
    },
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

现在在根目录下创建一个`test`文件夹，并在其中创建`setup.js`文件:

```
// cleanup helper
import '@testing-library/react/cleanup-after-each'
// custom matchers for jest
// learn more: https://github.com/testing-library/jest-dom
import '@testing-library/jest-dom/extend-expect' 
```

Enter fullscreen mode Exit fullscreen mode

同样在同一个`test`文件夹中创建一个`module-mock.js`:

```
module.exports = {} 
```

Enter fullscreen mode Exit fullscreen mode

#### 代码覆盖率

在*的 package.json* 中，在你的`test`脚本的末尾添加`--coverage`:

```
{  ...  "scripts":  {  ...  "test":  "jest --coverage"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 观看模式

在编码时，使用 Jest in watch 模式来获得与您正在更改的文件相关的测试的即时反馈。
要使用这个特性，添加一个脚本到 *package.json* 并使用它:

```
{  ...  "scripts":  {  ...  "test:watch":  "jest --watch"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 柏树

安装`cypress`和助手:

```
yarn add cypress @testing-library/cypress -D 
```

Enter fullscreen mode Exit fullscreen mode

然后给 *package.json* 添加一个脚本来运行 cypress:

```
{  ...  "scripts":  {  ...  "cy:open":  "cypress open",  "cy:run":  "cypress run",  //  run  all  cypress  tests  }  } 
```

Enter fullscreen mode Exit fullscreen mode

```
yarn cy:open 
```

Enter fullscreen mode Exit fullscreen mode

Cypress 在运行测试时会录制视频并截图。
让我们将赛普拉斯为此使用的文件夹添加到`.gitignore`

```
 ...
  cypress/videos
  cypress/screenshots 
```

Enter fullscreen mode Exit fullscreen mode

#### 柏树. json

当第一次运行`cypress open`时，它会在根目录下名为`cypress`的文件夹中创建一堆文件和文件夹。它还在根目录下创建一个名为`cypress.json`的文件。这是 cypress 使用的配置文件。

让我们添加一个在 E2E 测试中使用的 base URL:

```
//cypress.json  {  "baseUrl":  "http://localhost:3000"  } 
```

Enter fullscreen mode Exit fullscreen mode

#### @测试-图书馆/柏树

向 cypress 添加了一些非常方便的命令，让我们来配置它:

转到`<rootDir>/cypress/support`，打开`index.js`，添加这一行:

```
import '@testing-library/cypress/add-commands'
... 
```

Enter fullscreen mode Exit fullscreen mode

### 测试工具(助手):

拥有一个 test-utils 文件，该文件导出一组专门用于您正在测试的项目的工具。

*   示例:

导出一个`render`方法，它负责添加样式化组件 ThemeProvider HOC:

```
import React from 'react'
import {
  render as originalRender,
  wait,
} from '@testing-library/react'

const theme = {
  colors: {
    red: 'red',
  },
}

function render(component, renderOptions) {
  const utils = originalRender(
    <ThemeProvider theme={theme}>
      {component}
    </ThemeProvider>,
    renderOptions
  )
  return {
    ...utils,
  }
}
export { render } 
```

Enter fullscreen mode Exit fullscreen mode

现在在你的测试中，从这个`test-utils`文件中导入`render`而不是`@testing-library/react`

# 单元测试

当你想测试*一个功能/组件* :
的功能时，编写一个单元测试

```
import React from 'react'
import { render } from '@testing-library/react'
import Paragraph from '../paragraph'

test('renders the text given', () => {
  const { getByText } = render(<Paragraph>Hello</Paragraph>)

  expect(getByText(/Hello/i)).toBeInTheDocument()
}) 
```

Enter fullscreen mode Exit fullscreen mode

# 集成测试

当你想测试*几个组件一起工作* :
的功能时，写一个集成测试

```
import React from 'react'
import { MockedProvider } from '@apollo/react-testing'
import wait from 'waait'
import { fireEvent } from '@testing-library/react'
import { render } from '../test-utils'
import App, { LOGIN_MUTATION } from '../app'

beforeEach(() => {
  window.localStorage.removeItem('token')
})

test('login as a user', async () => {
  const fakeUser = { id: 123, username: 'fakeuser' }
  const fakeUserCredentials = {
    ...fakeUser,
    password: 'stupidpassword123',
  }
  const token =
    'thisisjustanexampleofatoken-youcanuseafakedatageneratorinstead'
  const loginMutationMock = jest.fn()
  const loginMutationErrorMock = jest.fn()
  const mocks = [
    {
      request: {
        query: LOGIN_MUTATION,
        variables: {
          username: fakeUserCredentials.username,
          password: fakeUserCredentials.password,
        },
      },
      result: () => {
        loginMutationMock()
        return { data: { user: fakeUser, token: token } }
      },
      error: () => {
        loginMutationErrorMock()
      },
    },
  ]
  const { getByTestId, getByText, getByLabelText } = render(
    <MockedProvider mocks={mocks} addTypename={false}>
      <App />
    </MockedProvider>
  )
  // open login form dialog/modal
  fireEvent.click(getByText(/login/i))
  // fill out login form
  const usernameNode = getByLabelText(/username/i)
  const passwordNode = getByLabelText(/password/i)
  usernameNode.value = fakeUserCredentials.username
  passwordNode.value = fakeUserCredentials.password
  // submit login form
  fireEvent.click(getByText(/sign in/i))
  // wait for the mocked requests to finish
  await wait(0)
  // assert calls
  expect(loginMutationMock).toHaveBeenCalledTimes(1)
  expect(loginMutationErrorMock).not.toHaveBeenCalled()
  // assert login side-effect
  expect(window.localStorage.getItem('token')).toBe(token)
  expect(getByTestId('username').textContent).toEqual(
    fakeUser.username
  )
}) 
```

Enter fullscreen mode Exit fullscreen mode

# 端到端测试:

最简单的定义:假设你有一个服从你命令的机器人，现在让它以普通用户的身份测试你的应用🤷‍♂️.

```
describe('authentication and registration', () => {
  let user

  beforeEach(() => {
    return cy
      .logout()
      .createNewUser()
      .then(u => (user = u))
      .visit('/')
  })

  it('register as a guest user', () => {
    const user = {
      username: 'user',
      email: 'hello@example.com',
      password: 'password123',
    }
    cy.getByText(/register/i)
      .click()
      .getByLabelText(/username/i)
      .type(user.username)
      .getByLabelText(/email/i)
      .type(user.email)
      .getByLabelText(/password/i)
      .type(user.password)
      .getByText(/register/i)
      .click()
      .assertRoute('/')
    cy.getByTestId('username').should(
      'contain',
      user.username
    )
  })

  it('login as a user', () => {
    cy.getByText(/login/i)
      .click()
      .getByLabelText(/username/i)
      .type(user.username)
      .getByLabelText(/password/i)
      .type(user.password)
      .getByText(/sign in/i)
      .click()
      .assertRoute('/')
    cy.getByTestId('username').should(
      'contain',
      user.username
    )
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

我会努力改进这篇文章并添加内容，但如果你想纠正/添加/编辑❤️的某些东西，请随时发送一份 PR