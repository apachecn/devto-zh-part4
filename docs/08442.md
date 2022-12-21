# 测试简单的 React 组件

> 原文：<https://dev.to/ryands17/testing-a-simple-react-component-1d14>

在本系列的第一部分中，我们将学习如何通过测试 UI 中反映的变化来测试具有本地状态的组件。

#### 写测试很重要。还有什么更好的方式来编写它们，就像用户与你的应用程序交互一样。

[@testing-library/react](https://github.com/testing-library/react-testing-library/) 由[创建 **Kent C. Dodds**](https://kentcdodds.com/) 是 testing library 项目的一部分，包括 react 和其他 UI 库的测试实用程序。

正如 Kent 所说，不要测试你的组件状态。相反，测试用户界面反映的状态变化，因为这将是任何用户与你的应用程序交互的方式。

这就是为什么我正在创建一个系列，在这里我将添加带有特定用例的例子。

**下面例子中的资料员**就是这里的。

对于第一个例子，我们将采用一个简单的清单组件，它接受要显示的任务列表。我们可以切换以检查任务是否完成，还可以查看剩余任务的数量。

***注意:*** 我在这个项目中使用 TypeScript，因为它提供了很好的开发体验，但是你也可以通过剥离类型在 JavaScript 中创建这些例子。

这是用 [React 钩子](https://reactjs.org/docs/hooks-intro.html)做的任务列表组件。如果你不熟悉钩子，你可以用类组件实现同样的功能。

```
import React, { useState, useMemo } from 'react'

interface Checklist {
  id: number
  text: string
  checked: boolean
}

interface ChecklistProps {
  checklistItems: Checklist[]
}

const Checklist: React.FC<ChecklistProps> = ({ checklistItems = [] }) => {
  let [checklist, updatelist] = useState<Checklist[]>(checklistItems)

  const toggleComplete = (index: number) => {
    let newChecklist = [...checklist]
    newChecklist[index].checked = !newChecklist[index].checked
    updatelist(newChecklist)
  }

  let checkedItems = useMemo(() => checklist.filter(c => c.checked).length, [
    checklist,
  ])

  return (
    <div>
      <ul className="checklist">
        {checklist.map((checkitem, index) => (
          <li key={checkitem.id} className="list-item">
            <input
              type="checkbox"
              id={checkitem.id.toString()}
              checked={checkitem.checked}
              onChange={() => toggleComplete(index)}
            />
            <label htmlFor={checkitem.id.toString()}>{checkitem.text}</label>
          </li>
        ))}
      </ul>
      <p data-testid="checked-items">
        Checked {checkedItems} of {checklist.length} items
      </p>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们在一个无序列表中显示我们的任务，在它下面，已完成的任务不在总数之内。现在,`ul`标签有了一个 list 的角色，所以我们将用这个库给我们的特定方法来查询这个列表。

所以我们要写的第一个测试将是测试我们的列表是否被正确呈现。为此，我们将获取列表元素，并断言它是否包含与我们传递的任务数量相同的任务。

```
import React from 'react'
import { render } from '@testing-library/react'
import Checklist from './Checklist'

const checklistItems = [
  {
    id: 1,
    text: 'Learn React Testing Library',
    checked: false,
  },
  {
    id: 2,
    text: 'Learn Advanced JS concepts',
    checked: false,
  },
]

test(`has rendered a the items passed correctly`, () => {
  const { getByRole } = render(<Checklist checklistItems={checklistItems} />)
  let ul = getByRole('list')
  expect(ul.children.length).toEqual(2)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个简单的`test`块，它包含所需的导入、要通过 props 传递的样本任务列表，以及测试库中的一个**渲染**方法。这个方法将渲染我们的整个组件和它的子组件，不像[酵素](https://airbnb.io/enzyme/)的`shallow`方法，顾名思义，它对组件进行浅层渲染，即跳过子组件的渲染。

现在`render`返回给我们一些方法，帮助我们获取组件中存在的所需元素。所以我们用`getByRole`方法获取`ul`元素，默认情况下`ul`的角色是`list`。

最后，我们使用 Jest 提供的[匹配器](https://jestjs.io/docs/en/using-matchers)，我们可以检查列表项的长度是否等于我们提供的任务列表。现在，如果您通过`npm test`或`yarn test`运行这个，您的测试将通过！

***注意:*** `yarn test`或`npm test`默认以观察模式运行，这样当你保存你的测试或组件时，它会自动运行这些，你可以在你的终端查看输出。

继续，我们的第二个测试是断言复选框是否起作用，为此我们需要与任务项交互，所以我们需要模拟一个**点击**事件。这个库正好有这样的方法:`fireEvent`。

```
test(`updates UI of checked item on toggling`, () => {
  const { getByLabelText } = render(
    <Checklist checklistItems={checklistItems} />
  )

  let firstItem = getByLabelText(checklistItems[0].text) as HTMLInputElement
  fireEvent.click(firstItem)
  expect(firstItem.checked).toBeTruthy()

  fireEvent.click(firstItem)
  expect(firstItem.checked).toBeFalsy()
}) 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们在测试中再次呈现了清单组件。这很重要，因为它隔离了我们的测试，这样前一个测试不会影响下一个。

我们使用另一个实用方法`getByLabelText`获取任务列表中的第一项，因为我们使用了一个标签来切换任务完成，这个方法将找到与标签相关联的输入。

在获取任务之后，我们在该项上模拟一个点击事件。然后我们使用 Jest 提供的匹配器断言`checked`属性是否为 truthy。然后，我们再次模拟 click 事件，检查该事件是否正常工作，并成功切换复选框。在保存时，如果你检查你的终端，第二个测试也通过了！

***注意*:**`@testing-library/react`的最新版本在每个测试块之后自动处理清理，因此您不需要在您的测试中添加任何清理逻辑！。

对于我们的最后一个测试，我们将验证我们在任务列表下面呈现的选中项目数。

```
test(`correctly shows the checked item length`, () => {
  const { getByTestId, getByLabelText } = render(
    <Checklist checklistItems={checklistItems} />
  )
  let p = getByTestId('checked-items')

  let firstItem = getByLabelText(checklistItems[0].text) as HTMLInputElement
  fireEvent.click(firstItem)
  expect(p.textContent).toContain('1 of 2')

  let secondItem = getByLabelText(checklistItems[1].text) as HTMLInputElement
  fireEvent.click(secondItem)
  expect(p.textContent).toContain('2 of 2')
}) 
```

Enter fullscreen mode Exit fullscreen mode

***注意:*** `getByTestId`的工作方式就像 JavaScript 中的`getElementById`方法，但是它匹配的是`data-testid`属性而不是 id。

我们再次在测试块中呈现我们的组件，通过`getByLabelText`方法获取列表项，我们使用`toContain`匹配器匹配`p`标签的文本以包含我们传递的文本。

现在，在保存文件时，我们可以在终端中看到我们所有的测试都通过了！你会看到在运行`yarn start`或`npm start`时，我们的应用程序运行得非常完美。我们测试了我们的组件，就像我们将如何与它交互一样。这都是关于用本地状态测试组件的。

感谢您的阅读。