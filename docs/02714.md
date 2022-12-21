# 弥合 React 的 useState、useReducer 和 Redux 之间的差距

> 原文：<https://dev.to/leewarrickjr/bridging-the-gap-between-react-s-usestate-usereducer-and-redux-3k94>

[![Photo of a man carelessly leaping over a very deep chasm by Alex Radelich](img/b0bb22ab9e67367f3bcf0c87378d609a.png "Photo of a man carelessly leaping over a very deep chasm by Alex Radelich")](https://leewarrick.com/blog/static/ba29f3c8bad89c521880863a2ddfe9a2/c35de/mindthegap.jpg)

*最初发表于[leewarrick.com/blog](https://leewarrick.com/blog/a-guide-to-usestate-and-usereducer/)T3】*

Redux 是我认为是“个人珠穆朗玛峰”的技术之一。每次我看到它，我都觉得要记住的样板和模式没有尽头。

在我的第一份工作和我工作的第一个代码库，我们必须使用 NGRX (Angular 的 Redux 版本)。这非常具有挑战性；我花了几个小时阅读文档和观看教程，试图理解 NGRX。我甚至绝望地尝试学习 Redux 来理解 NGRX。我经常向我的老板抱怨要记住的所有样板文件、文件和模式。

他告诉我，“如果你使用你自己的解决方案，你可能最终会重复同样的模式”。

我最终承认。在用除了 Redux 之外的所有东西*管理了 React state 之后，我发现自己开始欣赏为什么它会以这种方式工作并且需要这么多样板文件。在学习了 React 的上下文 API、`useReducer`以及很多关于管理状态的知识之后，我终于欣赏 Redux 了。*

不过，从 A 地到 B 地并不容易。在学习`useState`和`useReducer`之间有很多内容要讲，当你进入 Redux 和管理复杂的状态时甚至更多。

## “使用状态”挂钩

React 的`useState`使用起来很愉快。给它一个初始值，它给你一个对反应值的引用和一个 setter 函数来更新这个值。

下面是带有`useState`的经典反例:

(**注**:如果你想看这些例子的现场版本，请在这里查看[的原文。](https://leewarrick.com/blog/a-guide-to-usestate-and-usereducer/) )

```
function Counter() {
    const [count, setCount] = React.useState(0)
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>
                Increment
            </button>
        </div>
    )
} 
```

够简单！只有两个怪癖:

首先，您必须向`setCount`函数提供一个新的状态值(`setCount(count++)`和`count++`不起作用)。React 沉浸在不变性中，这意味着您应该总是返回一个新值，而不是更改当前值。

另一个奇怪的地方是返回的数组，但是几乎所有的钩子都遵循这个模式。考虑到与类组件相比，编写函数式钩子组件是多么容易，这是一个很小的代价。

因此，虽然`useState`看起来很简单，但是当您需要不止一个状态值时会发生什么呢？如果您有一个包含多个字段的表单，该怎么办？

幸运的是，有了钩子，我们可以多次使用`useState`:

```
function FormExample() {
  const [email, setEmail] = React.useState('')
  const [name, setName] = React.useState('')

  const columnStyle = {
    display: 'flex',
    flexDirection: 'column',
  }
  return (
    <form style={{ ...columnStyle, width: '300px' }}>
      <label style={columnStyle}>
        <span>Name:</span>
        <input
          onChange={e => setName(e.target.value)}
          value={name}
          type="text"
        />
      </label>
      <label style={columnStyle}>
        <span>Email:</span>
        <input
          onChange={e => setEmail(e.target.value)}
          value={email}
          type="text"
        />
      </label>
      <pre>{JSON.stringify({name, email}, null, 2)}</pre>
    </form>
  )
}
render(FormExample) 
```

太好了！但是用`useState`多少才算多呢？有合理的限制吗？我们应该把它保持在 5 或更少吗？

如果您需要管理更复杂的数据结构或执行副作用呢？

## 【useReducer】挂钩

[![Photo of a man stirring a campfire cooking pot by Gary Sandoz](img/ee424a58f1b3be0c800007e0012d6cf0.png "Photo of a man stirring a campfire cooking pot by Gary Sandoz")](https://leewarrick.com/blog/static/1a1acb148e3f2c1505bb60e360fc0d10/c35de/cooking.jpg)

现在我们已经进入了`useReducer`领域。‘useReducer’中的 Redux 来自 Redux，Redux 又从 JavaScript 的`Array.reduce()`中借用了它。

那么“减少”什么意思呢？想想煨香醋，这样醋就蒸发了，剩下的是香甜可口的釉料。这就是所谓的“香脂还原”。把 reducers 想象成接受论点并把它们消化掉，直到它们变得更容易处理。

在 React 的上下文中，这里是与`useReducer` :
一起使用的典型模式

```
const reducer = function (currentState, action) {
  // Make a new state based on the current state and action
  // Note: There's usually a big switch statement here
  return newState
}
const [state, dispatch] = useReducer(reducer, initialValue)

// example usage:
dispatch({type: "THING_HAPPENED"})
// Or with an optional "payload":
dispatch({type: "THING_HAPPENED", payload: newData}) 
```

在我们深入研究经典的 reducer 模式之前，我想把`useReducer`归结为它的基本功能。简而言之:`useReducer`与`useState`几乎相同，除了`useReducer`让您通过传递函数来定义如何更新它的状态值。

让我们看看之前的反例。这里我们将使用`useReducer` :
实现我们自己的`useState`

```
function Counter() {
    const [count, setCount] = React.useReducer((currentCount, newCount) => newCount, 0)
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={() => setCount(count + 1)}>
                Increment
            </button>
        </div>
    )
} 
```

不过，这是没有任何好处的额外工作。为什么将一个函数传递给`useReducer`，而将另一个函数传递给`onClick`？此外，我们的计数器逻辑存在于我们的 JSX 按钮元素中，这并不好。

让我们去掉额外的函数，把我们的逻辑从 JSX 中移出来:

```
function Counter() {
    const [count, increment] = React.useReducer(currentCount => currentCount + 1, 0)
    return (
        <div>
            <p>Count: {count}</p>
            <button onClick={increment}>
                Increment
            </button>
        </div>
    )
} 
```

如果您没有从这篇文章中得到任何其他东西，请记住:

**`useReducer`的强大之处在于它允许我们定义如何更新状态值。**

也就是说，在我们深入研究 reducers 和模式之前，我想花一点时间来定义“状态”。

## 对“状态”中的问题做出反应

React 中有一些关于“状态”的主要误解。我认为当 Vue 将它的状态版本命名为“数据”时，他们使得考虑 Vue 代码变得更容易。

React 定义的状态实际上只是我们通常存储在变量中的数据。然而，React 需要对数据的变化做出反应。所以在底层，当你在状态中存储东西的时候，React 把方法和属性附加到那个对象上，这样它就知道什么时候触发重新渲染。

React“状态”只是一个 React 观察更新的对象。

那么，如果 React 的“状态”不是真正的状态，那么什么是状态呢？“国家”的概念实际上早于几十年前的反应。在计算机科学术语中，**应用程序的状态描述了它的当前状态以及导致该状态的先前事件和用户交互。**

众所周知，这种状态在编程中很难处理。这就是为什么当你寻求帮助时，每个技术支持代理默认为“关机再开机”。您的设备不知何故进入了不良状态，有时脱离不良状态的最简单方法是重启系统进入新状态。

当我们编写 React 代码时，问题是我们倾向于将程序的状态与 React 正在观察的数据混为一谈。例如，组件中可能有描述用户在输入字段中键入内容的数据，也有告诉您表单是否有效的数据。当前数据和它如何响应用户而变化的组合就是组件的实际状态。

我们通常只担心存储和更新组件中的数据，并避免考虑它的实际状态，直到我们开始发现错误。

## reducer 和 Redux

reducer 模式试图驯服更新复杂状态的流程。虽然不简单，但它可以帮助我们定义和管理应用程序和组件中的状态变化。

让我们在一个表单的上下文中来看一个简单版本的 reducer 模式:

```
const reducer = function (currentState, action) {
    switch(action.type) {
        case 'NAME_CHANGED':
            return {...currentState, name: action.payload}
        case 'EMAIL_CHANGED':
            return {...currentState, email: action.payload}
        default:
            return state
    }
}
const [state, dispatch] = useReducer(reducer, {name: '', email:''})

// example usage:
dispatch({type: 'NAME_CHANGED'})
// or with a payload:
dispatch({type: 'NAME_CHANGED', payload: 'Suzy'}) 
```

把这想象成一个事件信号系统。当我们调用`dispatch`时，我们传入一个告诉我们发生了什么的对象，然后我们的 reducer 获取该信息并处理它以创建一个新的状态。

那么为什么称之为派遣和行动呢？为什么是 switch 语句？

### 调度员

我喜欢把`dispatch`想象成一个老式总机电话系统的调度员。调度程序将信息与主消息(类型)和任何附加信息(有效载荷)打包在一起，并将其插入到交换机，我们的 reducer(碰巧包含一个`switch`)中。

### 动作

[他们真的应该称之为“事件”而不是“行动”。](https://medium.com/magnetis-backstage/why-action-is-a-bad-name-for-a-redux-action-68bec375539e)动作描述了应用程序中发生的事件。所以在命名动作类型时，最好用过去式即`"NAME_CHANGED"`，而不是现在式即`"CHANGE_NAME"`。

虽然这看起来像是不相关的语义，但它对于理解 redux 模式有很大的不同。重要的是要记住，你的减压器是响应事件来决定一个新的状态。当你说`"CHANGE_NAME"`时，你在暗示你的减速器*将*改变名称，而不是让它决定*是否*会改变它。

注意:虽然我更愿意称这些为事件，但出于惯例，我们将坚持使用“动作”。记住在你的动作类型中使用过去式。

**另一个注意:**我们也使用[尖叫 _ 蛇 _ 案件](https://en.wikipedia.org/wiki/Snake_case)作为我们的动作类型。这表示字符串是一个常量值，也恳求您不要改变它们。(“尖叫蛇案”是一个金属乐队的绝佳名字，btw。)

### 切换语句

在长的`if` / `else if`链中选择 switch 语句主要是为了可读性。

您可能还注意到，在我们的 switch 语句中没有`break`语句，而是有许多扩展操作符。我们使用`return`来代替 break，这样可以防止开关瀑布效应(后面会详细介绍)。至于 spread 操作符，请记住 React 是建立在不变性的基础上的，因此创建新对象是必要的。通过首先传播，然后传入我们的更改，我们可以只覆盖我们需要的状态中的属性，而不影响其余的:

```
const state = {
  name: "Robert",
  email: "SuperBobby74@aol.com"
}
const newState = {...state, name: "Bobby"}
console.log(newState) 
```

让我们将 reducer 模式和`useReducer`应用到前面的表单:

```
function FormExample() {
  function formReducer(state, action) {
    switch (action.type) {
      case 'NAME_CHANGED':
        return { ...state, name: action.payload }
      case 'EMAIL_CHANGED':
        return { ...state, email: action.payload }
      default:
        return state
    }
  }

  const [state, dispatch] = React.useReducer(formReducer, {
    name: '',
    email: '',
  })

  const columnStyle = {
    display: 'flex',
    flexDirection: 'column',
  }

  return (
    <form style={{ ...columnStyle, width: '300px' }}>
      <label style={columnStyle}>
        <span>Name:</span>
        <input
          onChange={e =>
            dispatch({ type: 'NAME_CHANGED', payload: e.target.value })
          }
          value={state.name}
          type="text"
        />
      </label>
      <label style={columnStyle}>
        <span>Email:</span>
        <input
          onChange={e =>
            dispatch({ type: 'EMAIL_CHANGED', payload: e.target.value })
          }
          value={state.email}
          type="text"
        />
      </label>
      <pre>{JSON.stringify(state, null, 2)}</pre>
    </form>
  )
}

render(FormExample) 
```

这很好，但我们可以做一些改进。

首先，让我们把动作类型拿出来，并把它们做成这样一个对象:

```
const actions = {
  nameChanged: 'NAME_CHANGED',
  emailChanged: 'EMAIL_CHANGED',
} 
```

这将使你免于以后的错误。如果您在切换和调度中使用`actions.nameChanged`,您的 IDE 可能有助于防止操作类型中的打字错误。(如果代码库使用的是 TypeScript，您可能会在枚举中看到相同的模式。)

我们还可以将初始状态提取到它自己的对象中，并将它与我们的 reducer 和我们的操作一起移出组件。

```
const actions = {
  nameChanged: 'NAME_CHANGED',
  emailChanged: 'EMAIL_CHANGED',
}

const initialState = {
  name: '',
  email: '',
}

function formReducer(state, action) {
  switch (action.type) {
    case actions.nameChanged:
      return { ...state, name: action.payload }
    case actions.emailChanged:
      return { ...state, email: action.payload }
    default:
      return state
  }
}

function FormExample() {
  const [state, dispatch] = React.useReducer(formReducer, initialState)

  const columnStyle = {
    display: 'flex',
    flexDirection: 'column',
  }
  return (
    <form style={{ ...columnStyle, width: '300px' }}>
      <label style={columnStyle}>
        <span>Name:</span>
        <input
          onChange={e =>
            dispatch({ type: actions.nameChanged, payload: e.target.value })
          }
          value={state.name}
          type="text"
        />
      </label>
      <label style={columnStyle}>
        <span>Email:</span>
        <input
          onChange={e =>
            dispatch({ type: actions.emailChanged, payload: e.target.value })
          }
          value={state.email}
          type="text"
        />
      </label>
      <pre>{JSON.stringify(state, null, 2)}</pre>
    </form>
  )
}
render(FormExample) 
```

### 使用归约器处理业务逻辑

你可能想知道为什么我们把我们的`useState`例子弄得如此复杂。似乎我们所做的只是添加代码来复制我们以前拥有的相同功能。

当我们在表单中添加一个提交按钮时，Reducers 开始大放异彩。表单是非常复杂的东西(需要管理大量的状态)，这就是为什么有这么多表单库的原因。您需要考虑验证，并跟踪填写了哪些字段，提交表单时发生了什么，等等。

如果你打算用`useState`来管理这个逻辑，你会发现自己用很多代码来包装你的提交，添加更多的`useState`钩子，并且可能用验证函数来包装你的 setter 函数，这些函数可能会更新*和其他*状态值。这会很快变得一团糟。

与`useState`不同，`useReducer`提供了一个很好的基础设施来处理所有围绕验证和提交的逻辑:

```
const actions = {
  nameChanged: 'NAME_CHANGED',
  emailChanged: 'EMAIL_CHANGED',
  formSubmitted: 'FORM_SUBMITTED',
}

const initialState = {
  name: '',
  email: '',
  nameError: null,
  emailError: null,
  formCompleted: false,
  formSubmitted: false,
}

function formReducer(state, action) {
  let error
  switch (action.type) {
    case actions.nameChanged:
      error = validate('name', action.payload)
      return { ...state, name: action.payload, nameError: error }
    case actions.emailChanged:
      error = validate('email', action.payload)
      return { ...state, email: action.payload, emailError: error }
    case actions.formSubmitted:
      // if the form has been successfully submitted,
      // stop here to prevent rage clicks and re-submissions
      if (state.formCompleted) return state
      let formValid = true
      // invalidate the form if values are missing or in error
      if (state.nameError || !state.name || state.emailError || !state.email) {
        formValid = false
      }
      // if the user has attempted to submit before, stop here
      if (state.formSubmitted) return { ...state, formCompleted: formValid }
      // if this is the first submit, we need to validate in case the user
      // clicked submit without typing anything
      let nameError = validate('name', state.name)
      let emailError = validate('email', state.email)
      return {
        ...state,
        nameError,
        emailError,
        formSubmitted: true,
        formCompleted: formValid,
      }
    default:
      return state
  }
}

// this helper function validates the name and email inputs
// if there's an error, it returns an error message describing the problem
// if there are no errors, it returns null
// it's outside our reducer to make things more readable and DRY
function validate(name, value) {
  if (typeof value === 'string') value = value.trim()
  switch (name) {
    case 'name':
      if (value.length === 0) {
        return 'Must enter name'
      } else if (value.split('  ').length < 2) {
        return 'Must enter first and last name'
      } else {
        return null
      }
      break
    case 'email':
      if (value.length === 0) {
        return 'Must enter email'
      } else if (
        !value.includes('@') ||
        !value.includes('.') ||
        value.split('.')[1].length < 2
      ) {
        return 'Must enter valid email'
      } else {
        return null
      }
      break
  }
}

function FormExample() {
  const [state, dispatch] = React.useReducer(formReducer, initialState)

  // extract our dispatch to a change handler to DRY the code up
  function handleChange(e) {
    dispatch({ type: actions[e.target.name + 'Changed'], payload: e.target.value })
  }

  // this is attached to the form, not the submit button so that
  // the user can click OR press 'enter' to submit
  // we don't need a payload, the input values are already in state
  function handleSubmit(e) {
    e.preventDefault()
    dispatch({ type: actions.formSubmitted })
  }

  const columnStyle = {
    display: 'flex',
    flexDirection: 'column',
  }
  // this adds a red outline to the input if the field isn't filled out correctly,
  // but only if the user has attempted to submit
  const inputStyle = hasError => {
    return {
      outline: hasError && state.formSubmitted ? '2px solid red' : 'none',
    }
  }
  return (
    <form style={{ ...columnStyle, width: '300px' }} onSubmit={handleSubmit}>
      <label style={columnStyle}>
        <span>Name:</span>
        <input
          style={inputStyle(state.nameError)}
          onChange={handleChange}
          name="name"
          value={state.name}
          type="text"
        />
        <span>{state.formSubmitted && state.nameError}</span>
      </label>
      <label style={columnStyle}>
        <span>email:</span>
        <input
          style={inputStyle(state.emailError)}
          onChange={handleChange}
          name="email"
          value={state.email}
          type="text"
        />
        <span>{state.formSubmitted && state.emailError}</span>
      </label>
      <p>{state.formCompleted && 'Form Submitted Successfully!'}</p>
      <button type="submit">Submit</button>
      <pre>{JSON.stringify(state, null, 2)}</pre>
    </form>
  )
}

render(FormExample) 
```

注意我们的 reducer 函数是如何随着业务逻辑而膨胀的。没关系！事实上，让你的减肥者保持肥胖，让事件处理者保持苗条是一个很好的经验法则。

功能也发生了变化。当我们的值改变时，reducer 处理验证，并在必要时向我们的状态添加错误消息。如果表单还没有提交，我们可以等到用户提交后再用红框和错误消息来烦他们。如果提交时出现错误，我们可以在他们键入时更改消息，以指导他们输入正确的信息。最后，我们可以在提交案例中用一个`formCompleted`标志来防止愤怒点击和重复提交。

这为用户提供了很好的体验，并为所有这些复杂的 UI 交互提供了一个很好的组织模型。

## 欢迎来到 Redux

信不信由你，**我们现在已经实现了 Redux** 的所有主要组件。Redux 本身实际上只是一个助手库，帮助我们完成本文中所做的事情。

在一个典型的 Redux 应用程序中，我们将**动作**、**减速器**和**状态**放入项目中它们自己的文件中。为了管理多个状态对象，我们可以将动作/缩减器/状态集合分组到不同的**存储**，然后这些存储成为具有**根缩减器**的**全局存储**的一部分。根缩减器的工作是将每个存储的状态组合成一个单一的全局状态对象。

然后，我们将所需的任何存储、调度程序和操作导入到组件中，以访问状态并将事件发送到全局存储。Redux 提供了一些实用程序来帮助构建这个全局状态管理系统，但是在大多数情况下，您将自己编写所有的动作、reducers 和状态，就像我们在本文中所做的一样。

因此，如果您已经做到了这一步，您就可以使用 Redux 了！真正的问题是，你应该吗？

### redux 死了是☠吗？

借助上下文 API 和这里学到的信息，现在不用 Redux 也可以做很多事情。把一个上下文想象成一个 Redux Store，你可以把它放在应用程序的任何地方。包装在上下文提供程序中的任何组件都可以访问您从中共享的值。上下文可以在应用程序的顶层，为所有东西提供状态，或者在更底层，只与少数组件共享它的状态。

Kent C Dodds 有一篇关于在 React 中使用上下文进行状态管理的**优秀的** [文章](https://kentcdodds.com/blog/application-state-management-with-react)。

也就是说， **Redux 没有死**。有大量的代码库在使用它，如果你打算专业地编写 React，学习它并不是一个坏主意。

## 超越 Redux😵

我们现在要进入一些稍微高级的话题，所以系好安全带。

最精明的测试人员可能已经注意到了上一个例子中的**错误。**向上滚动，看看能不能找到我们漏掉的边缘案例。

放弃？

提交成功后，您可以编辑表单！

我们如何解决这个问题？您的第一反应可能是开始在我们的 reducer 中散布`formSubmitted`标志，以防止对表单的进一步修改，就像我们在提交案例开始时所做的那样。

这是可行的，但是很难阅读和推理。我认为提交案例已经有点乱了，给其他案例添加更多的逻辑只会让事情变得更糟。

更重要的是，我们最初是如何忽略这一点的？我们学习了所有这些复杂的 JavaScript 来防止错误，但我们还是找到了一些！

## 隐性 vs 显性状态

在我关于状态的话题中，我提到过我们有时会在代码中用布尔值或标志来描述状态。我们已经在表单中用`formCompleted`和`formSubmitted`做到了这一点。问题是我们隐式地描述了表单的状态，而不是显式地描述。

这意味着我们依赖这些布尔的某种组合来描述表单的状态。例如，如果用户没有输入任何东西，也没有按提交，我们可以写:

```
if (!formSubmitted && !name && !email && !emailError && !nameError) {
  // behave as if user hasn't done anything yet
} 
```

这很乱，很难理解。当你回头再看这段代码时，你可能会忘记它是如何工作的，并且犹豫是否要修改它。更好的方法是显式地描述表单的状态，然后确保表单在任何时间点只能存在于其中一种状态。

我们可以将我们以前状态描述为:

*   **清除** -用户没有输入任何内容或按提交
*   **脏** -用户已开始输入信息，但尚未成功完成并提交
*   **已完成** -表格已正确填写并提交

我们还希望处理这些状态之间的转换，以及在每个状态下可能发生的操作:

**清除** -用户没有输入任何内容或按提交

*   可能的转换:脏

## -允许的操作:编辑和提交，但提交不会触发错误，只是一条消息

**脏** -用户已开始输入信息，但尚未成功完成并提交

*   可能的转换:已完成

## -允许操作:编辑和提交，但提交会触发错误信息

**已完成** -表格已正确填写并提交

*   可能的转换:无！
*   允许的操作:无！

## 有限状态机

我们刚刚创建的心智模型是一个状态机或有限状态机(FSM)。**有限的**意味着表单可以存在的状态数量有限，**状态**描述表单的状态，**机器**表示我们如何在不同状态之间转换的机制。

我不是状态机专家，所以我强烈推荐阅读 David Khourshid 的[这些](https://medium.com/@DavidKPiano/the-facetime-bug-and-the-dangers-of-implicit-state-machines-a5f0f61bdaa2) [文章](https://24ways.org/2018/state-machines-in-user-interfaces/)，以便更深入地了解状态机。

将该模型应用于我们的代码有两种选择。

首先，有一个为 FSM 量身定制的库叫做 [XState](https://xstate.js.org/docs/) ，由上面提到的同一个 David 编写。如果你感兴趣，戴夫·格迪斯有一个很棒的关于在 React 中使用 xstate 的[教程](https://gedd.ski/post/state-machines-in-react/)。

另一种选择是在我们的 reducer 中自己实现逻辑。这有点难，但是如果您阅读了我链接的 FSM 文章，您可能已经看到了一个使用**嵌套 switch** 语句实现 FSM 的例子。让我们将它应用到我们的表单中。

### 高级开关报表

在我们开始最后一个例子之前，让我们简单回顾一下 JavaScript 的`switch`。

我们将要使用的是“落差”或“瀑布”开关用法。这意味着我们将故意在每个案例中使用*而不是*,这样我们就可以匹配多个案例。

让我们看一个例子，我们无视妈妈的建议，不吃早餐，但仍然吃午餐和晚餐:

```
const actionType = "LUNCH_ORDERED"

switch(actionType) {
  case "BREAKFAST_ORDERED":
    console.log("breakfast")
    // no break!
  case "LUNCH_ORDERED":
    console.log("lunch")
    // no break!
  case "DINNER_ORDERED":
    console.log("dinner")
    break
  default:
    console.log("fasting 😵")
} 
```

一旦您匹配了一个案例，您将匹配所有案例，直到您中断或返回。

嵌套开关呢😵？

```
function dailyLife(status, actionType) {
  switch(status) {
    case "work":
      switch(actionType) {
        case "WORK_REQUESTED":
          console.log("DOING WORK")
          break
      }
    //no break after "work"
    case "holiday":
      switch(actionType) {
        case "CAKE_EATEN":
          console.log("FEELING FAT")
          break
        case "NAP_REQUESTED":
          console.log("NAPPING")
          break
      }
  }
}
console.log("ooooh, who's birthday is it?")
dailyLife("work", "CAKE_EATEN") // feeling fat

console.log("Taking a break, afk")
dailyLife("work", "NAP_REQUESTED") // napping

console.log("Hey, I know it's Saturday, but can you get us that TPS report?")
dailyLife("holiday", "WORK_REQUESTED") // not happening, sorry boss 
```

这里我们可以看到，你可以在上班和放假的时候睡午觉，但是在放假的时候不能上班。(至少你不应该)。

这个想法是，如果你必须在状态之间共享动作，**将带有非共享动作的状态放在顶部**。如果只能在工作中工作，那么工作状态应该是在最上面的。如果工作和假期都能吃蛋糕，那么假期/吃蛋糕应该在下面。

这绝对是一种先进的技术，所以当你写一个嵌套和瀑布式的复杂开关时，要小心并经常测试。

在我们的表单中，我们希望用户能够编辑表单，不管它是“干净的”还是“脏的”。为了共享输入更改操作，我们不在干净和脏的情况之间`break`，以便这些操作对两者都可用。此外，您可以在两种状态下提交，但是提交在每种状态下的行为不同。

好了，我们走吧！让我们来看看我们最终的表单示例，它包含 FSM 和`useReducer` :

```
const actions = {
  nameChanged: 'NAME_CHANGED',
  emailChanged: 'EMAIL_CHANGED',
  formSubmitted: 'FORM_SUBMITTED',
}

const initialState = {
  name: '',
  email: '',
  nameError: null,
  emailError: null,
  submitAttempted: false,
  submitMessage: '',
  status: 'clean',
}

function formReducer(state, action) {
  let error
  switch (state.status) {
    case 'dirty':
      switch (action.type) {
        case actions.formSubmitted:
          let formValid = true
          let nameError = validate('name', state.name)
          let emailError = validate('email', state.email)
          if (nameError || !state.name || emailError || !state.email) {
            formValid = false
          }
          return {
            ...state,
            nameError,
            emailError,
            submitAttempted: true,
            status: formValid ? 'completed' : 'dirty',
            submitMessage: formValid
              ? 'Form Submitted Successfully'
              : 'Form Has Errors',
          }
      }
    // no 'break' or 'return', case 'dirty' continues!
    case 'clean':
      switch (action.type) {
        case actions.nameChanged:
          error = validate('name', action.payload)
          return {
            ...state,
            name: action.payload,
            nameError: error,
            submitMessage: '',
            status: 'dirty',
          }
        case actions.emailChanged:
          error = validate('email', action.payload)
          return {
            ...state,
            email: action.payload,
            emailError: error,
            submitMessage: '',
            status: 'dirty',
          }
        case actions.formSubmitted:
          return {
            ...state,
            submitMessage: 'Please fill out the form',
          }
        default:
          return state
      }
    case 'completed':
    // no 'break' or 'return', case 'completed' continues!
    default:
      return state
  }
}

function validate(name, value) {
  if (typeof value === 'string') value = value.trim()
  switch (name) {
    case 'name':
      if (value.length === 0) {
        return 'Must enter name'
      } else if (value.split('  ').length < 2) {
        return 'Must enter first and last name'
      } else {
        return null
      }
      break
    case 'email':
      if (value.length === 0) {
        return 'Must enter email'
      } else if (
        !value.includes('@') ||
        !value.includes('.') ||
        value.split('.')[1].length < 2
      ) {
        return 'Must enter valid email'
      } else {
        return null
      }
      break
  }
}

function FormExample() {
  const [state, dispatch] = React.useReducer(formReducer, initialState)

  function handleChange({ target: { name, value } }) {
    dispatch({ type: actions[name + 'Changed'], payload: value })
  }

  function handleSubmit(e) {
    e.preventDefault()
    dispatch({ type: actions.formSubmitted })
  }

  const columnStyle = {
    display: 'flex',
    flexDirection: 'column',
  }
  const inputStyle = hasError => {
    return {
      outline: hasError && state.submitAttempted ? '2px solid red' : 'none',
    }
  }
  return (
    <form style={{ ...columnStyle, width: '300px' }} onSubmit={handleSubmit}>
      <label style={columnStyle}>
        <span>Name:</span>
        <input
          style={inputStyle(state.nameError)}
          onChange={handleChange}
          name="name"
          value={state.name}
          type="text"
        />
        <span>{state.submitAttempted && state.nameError}</span>
      </label>
      <label style={columnStyle}>
        <span>email:</span>
        <input
          style={inputStyle(state.emailError)}
          onChange={handleChange}
          name="email"
          value={state.email}
          type="text"
        />
        <span>{state.submitAttempted && state.emailError}</span>
      </label>
      <p>{state.submitMessage}</p>
      <button type="submit">Submit</button>
      <pre>{JSON.stringify(state, null, 2)}</pre>
    </form>
  )
}

render(FormExample) 
```

现在我们的表单没有 bug 了！

我们已经明确地建模并考虑了它可能存在的所有状态，并定义了这些状态下可能的动作。

**注意:**你可能注意到我们在代码中还有一个`submitAttempted`布尔。这是可以的，因为它仅用于显示或隐藏表单中的错误消息。最重要的是，**我们不检查`submitAttempted`来确定我们处于什么状态。**

## 离别的思念

这篇文章充满了先进的概念，我希望你能够学到其中的一些，即使你没有一直读到最后。如果你没有理解每一个概念和例子，不要烦恼。从简单的东西开始，在进入更难的概念之前，首先在你自己的代码中应用和实践这些概念。我就是这样学会的。

感谢阅读这篇大文章，干杯！

*喜欢这个帖子？请[订阅我的时事通讯](https://tinyletter.com/leewarrick)和[收听我的播客！](https://techjr.dev)*