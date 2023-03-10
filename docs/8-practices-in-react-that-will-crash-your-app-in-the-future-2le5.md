# React 中的 8 种做法会让你的应用程序在未来崩溃

> 原文：<https://dev.to/jsmanifest/8-practices-in-react-that-will-crash-your-app-in-the-future-2le5>

在[媒体](https://medium.com/@jsmanifest)上找我！

**编辑**:这篇文章的第 **#2** 点已经被修改，从读者的角度来看更容易理解(也更恐怖)。感谢 dev.to 上的用户发邮件给我，告诉我之前的困惑！

我们很多人都爱上了 react 库，原因有几个。创建复杂的交互式用户界面是非常容易的。最重要的是能够在另一个组件之上组合组件，而不会破坏其他组合的组件。

令人惊讶的是，即使是社交媒体巨头，如脸书、T2、Instagram、T4、Pinterest、T7，也大量使用了它们，同时通过巨大的 API，如谷歌地图、T9，创造了无缝的用户体验。

如果您目前正在使用 react 构建一个应用程序，或者考虑在即将到来的项目中使用 react，那么本教程就是为您准备的。我希望这篇教程也能通过展示一些你应该三思而行的代码实现来帮助你开发出优秀的 react 应用程序。

事不宜迟，React 中有 8 种做法会让你的应用程序在未来崩溃:

## 1。在 Null 上声明默认参数

我在之前的一篇文章中提到过这个话题，但是这是那些令人毛骨悚然的“陷阱”之一，可以在一个阴沉的星期五愚弄一个粗心的开发者！毕竟，应用程序崩溃是*而不是笑话* -如果处理不当，任何类型的崩溃都可能在任何时间点导致金钱损失。

我曾经因为花了大量时间调试类似的东西而感到内疚:

```
const SomeComponent = ({ items = [], todaysDate, tomorrowsDate }) => {
  const [someState, setSomeState] = useState(null)

  return (
    <div>
      <h2>Today is {todaysDate}</h2>
      <small>And tomorrow is {tomorrowsDate}</small>
      <hr />
      {items.map((item, index) => (
        <span key={`item_${index}`}>{item.email}</span>
      ))}
    </div>
  )
}

const App = ({ dates, ...otherProps }) => {
  let items
  if (dates) {
    items = dates ? dates.map((d) => new Date(d).toLocaleDateString()) : null
  }

  return (
    <div>
      <SomeComponent {...otherProps} items={items} />
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的应用程序组件中，如果日期最终为 falsey，它将被初始化为 null。

如果你和我一样，我们的直觉告诉我们，如果它是一个 falsey 值，那么默认情况下，条目应该被初始化为一个空数组。但是当日期为 falsey 时，我们的应用程序会崩溃，因为项目为空。*什么*？

如果没有传递值或未定义，默认函数参数允许命名参数用默认值初始化！

在我们的例子中，即使 null 是 falsey，它仍然是一个值！

所以下一次你设置默认值为空值时，一定要三思而后行。你可以简单地将一个值初始化为一个空数组，如果这是该值的预期类型。

## 2。用方括号抓取属性

有时获取属性的方式可能会影响应用程序的行为。如果你想知道这是什么行为，这是应用程序崩溃。下面是一个用方括号执行对象查找的例子:

```
const someFunction = function() {
  const store = {
    people: {
      joe: {
        age: 16,
        gender: 'boy',
      },
      bob: {
        age: 14,
        gender: 'transgender',
      }
    }
  }
  return {
    getPersonsProfile(name) {
      return store.people[name]
    },
    foods: ['apple', 'pineapple'],
  }
}

const obj = someFunction()
const joesProfile = obj.getPersonsProfile('joe')

console.log(joesProfile)
/*
  result: 
      {
        age: 16,
        gender: boy,
      }
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这些实际上是 100%有效的用例，除了[比对象键查找](https://stackoverflow.com/questions/26353417/javascript-object-vs-array-lookup-performance/26353637)慢之外，它们没有什么真正的错误。

无论如何，当一个非故意的问题出现时，真正的问题开始在你的应用程序中蔓延，比如一个小小的打字错误:

```
const someFunction = function () {
  const store = {
    people: {
      joe: {
        age: 16,
        gender: 'boy',
      },
      bob: {
        age: 14, 
      gender: 'transgender',
      }
    }
  }
  return {
    getPersonsProfile(name) {
      return store.people[name]
    },
    foods: ['apple', 'pineapple'],
  }
}

const obj = someFunction()
const joesProfile = obj.getPersonsProfile('Joe')
const joesAge = joesProfile.age

console.log(joesAge) 
```

Enter fullscreen mode Exit fullscreen mode

如果你或你的一个队友正在对这个代码片段进行一些改进，并且犯了一个小错误(比如将*乔*中的 *J* 大写)，结果将立即返回*未定义*，并且会发生崩溃:

```
"TypeError: Cannot read property 'age' of undefined
    at tibeweragi.js:24:29
    at https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:13924
    at https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:10866" 
```

Enter fullscreen mode Exit fullscreen mode

令人毛骨悚然的是，应用程序*不会崩溃*，直到你的一部分代码试图用那个未定义的值进行属性查找！

所以与此同时，joes profile ( *未定义*伪装)将在你的应用中传递，没有人能够知道这个隐藏的 bug 正在四处蔓延，直到一段代码执行一些属性查找，就像*Joe profile . age*，因为*Joe profile*是`undefined`！

一些开发人员为避免崩溃所做的是，如果查找失败，初始化一些默认的有效返回值:

```
const someFunction = function () {
  const store = {
    people: {
      joe: {
        age: 16,
        gender: 'boy',
      },
      bob: {
        age: 14, 
      gender: 'transgender',
      }
    }
  }
  return {
    getPersonsProfile(name) {
      return store.people[name] || {}
    },
    foods: ['apple', 'pineapple'],
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

至少现在 app 不会崩溃了。这个故事的寓意是，当你用方括号符号应用查找时，总是*处理无效的查找情况！*

对于一些人来说，如果没有真实世界的例子，可能很难解释这种做法的严重性。所以我要举一个真实的例子。我将要向您展示的代码示例取自 8 个月前的一个存储库。为了保护这段代码来源的隐私，我重命名了几乎所有的变量，但是*代码的设计、语法和架构保持不变* :

```
import { createSelector } from 'reselect'

// supports passing in the whole obj or just the string to correct the video type
const fixVideoTypeNaming = (videoType) => {
  let video = videoType

  // If video is a video object
  if (video && typeof video === 'object') {
    const media = { ...video }
    video = media.videoType
  }

  // If video is the actual videoType string
  if (typeof video === 'string') {
    // fix the typo because brian is an idiot
    if (video === 'mp3') {
      video = 'mp4'
    }
  }

  return video
}

/* -------------------------------------------------------
  ---- Pre-selectors
-------------------------------------------------------- */

export const getOverallSelector = (state) =>
  state.app[fixVideoTypeNaming(state.app.media.video.videoType)].options.total
    .overall

export const getSpecificWeekSelector = (state, props) =>
  state.app[fixVideoTypeNaming(state.app.media.video.videoType)].options.weekly[
    props.date
  ]

/* -------------------------------------------------------
  ---- Selectors
-------------------------------------------------------- */

export const getWeeklyCycleSelector = createSelector(
  getSpecificWeekSelector,
  (weekCycle) => weekCycle || null,
)

export const getFetchingTotalStatusSelector = createSelector(
  (state) =>
    state.app[fixVideoTypeNaming(state.app.media.video.videoType)].options.total
      .fetching,
  (fetching) => fetching,
)

export const getFetchErrorSelector = createSelector(
  (state) =>
    state.app[fixVideoTypeNaming(state.app.media.video.videoType)].options.total
      .fetchError,
  (fetchError) => fetchError,
) 
```

Enter fullscreen mode Exit fullscreen mode

*fixVideoTypeNaming* 是一个基于作为参数传入的值提取视频类型的函数。如果参数是一个视频*对象*，它将从*中提取视频类型。视频类型*属性。如果它是一个字符串，那么调用者会传入 videoType，这样我们就可以跳过第一步。有人发现视频类型 *.mp4* 属性在应用程序的几个地方被弄错了。为了快速临时解决这个问题，使用了 *fixVideoTypeNaming* 来修补这个错别字。

现在，正如你们中的一些人可能已经猜到的，这个应用是用 [redux](https://redux.js.org/) 构建的(因此有了语法)。

为了使用这些选择器，您可以将它们导入到一个 [connect](https://react-redux.js.org/api/connect) 高阶组件中，以连接一个组件来监听该状态片段。

```
const withTotalCount = (WrappedComponent) => {
  class WithTotalCountContainer extends React.Component {
    componentDidMount = () => {
      const { total, dispatch } = this.props
      if (total == null) {
        dispatch(fetchTotalVideoTypeCount())
      }
    }

    render() {
      return <WrappedComponent {...this.props} />
    }
  }

  WithTotalCountContainer.propTypes = {
    fetching: PropTypes.bool.isRequired,
    total: PropTypes.number,
    fetchError: PropTypes.object,
    dispatch: PropTypes.func.isRequired,
  }

  WithTotalCountContainer.displayName = `withTotalCount(${getDisplayName(
    WrappedComponent,
  )})`

  return connect((state) => {
    const videoType = fixVideoTypeNaming(state.app.media.video.videoType)
    const { fetching, total, fetchError } = state.app.media.video[
      videoType
    ].options.total

    return { fetching, total, fetchError }
  })(WithTotalCountContainer)
} 
```

Enter fullscreen mode Exit fullscreen mode

UI 组件:

```
const TotalVideoCount = ({ classes, total, fetching, fetchError }) => {
  if (fetching) return <LoadingSpinner />
  const hasResults = !!total
  const noResults = fetched && !total
  const errorOccurred = !!fetchError

  return (
    <Typography
      variant="h3"
      className={classes.root}
      error={!!fetched && !!fetchError}
      primary={hasResults}
      soft={noResults || errorOccurred}
      center
    >
      {noResults && 'No Results'}
      {hasResults && `$${formatTotal(total)}`}
      {errorOccurred && 'An error occurred.'}
    </Typography>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

该组件接收特设委员会传递给它的所有道具，并根据道具提供的数据显示符合条件的信息。在一个完美的世界里，这很好。在一个不完美的世界里，这可能暂时没问题。

如果我们回到容器，看看选择器选择它们的值的方式，我们实际上可能已经埋下了一颗定时炸弹，等待一个公开的攻击机会:

```
export const getOverallSelector = (state) =>
  state.app[fixVideoTypeNaming(state.app.media.video.videoType)].options.total
    .overall

export const getSpecificWeekSelector = (state, props) =>
  state.app[fixVideoTypeNaming(state.app.media.video.videoType)].options.weekly[
    props.date
  ] 
```

Enter fullscreen mode Exit fullscreen mode

当开发任何种类的应用程序时，在开发流程中确保更高的可信度和减少错误的常见做法是在中间实施 T2 测试，以确保应用程序按预期运行。

然而，在这些代码片段的情况下，如果不进行测试，如果不及早处理，应用程序*将来会*崩溃。

举例来说，*state . app . media . video . video type*是*在链条深处的四个*层级。如果另一个开发人员在被要求修复应用程序的另一部分时意外出错，导致 *state.app.media.video* 变成 *undefined* 怎么办？应用程序将崩溃，因为它无法读取未定义的的属性 *videoType。*

此外，如果有另一个关于*视频类型*和*固定视频类型命名*的打字错误问题没有更新，以适应与 *mp3* 问题一起，应用程序有另一个意外崩溃的风险，除非*真实用户*遇到这个问题，否则没有人能够发现。而到那个时候，就*晚了*。

假设应用程序永远不会遇到这样的错误也不是一个好的做法。请小心！

## 3。渲染时不小心检查空对象

很久以前，在有条件呈现组件的黄金时代，我经常做的事情是使用`Object.keys`检查数据是否已经被填充到对象中。如果有数据，那么如果条件通过，组件将继续呈现:

```
const SomeComponent = ({ children, items = {}, isVisible }) => (
  <div>
    {Object.keys(items).length ? (
      <DataTable items={items} />
    ) : (
      <h2>Data has not been received</h2>
    )}
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

假设我们调用了一些 API，并在响应中的某个地方接收到了作为对象的*项*。也就是说，这一开始看起来很好。*项*的预期类型是一个对象，所以对它使用 *Object.keys* 会非常好。毕竟，我们*确实*将项目初始化为空对象，作为一种防御机制，以防万一出现错误，将它变成假值。

但是我们不应该相信服务器*总是返回相同的结构*。如果项目在未来变成了一个数组呢？`Object.keys(items)`会不会*不会崩溃*但会返回一个奇怪的输出像`["0", "1", "2"]`。您认为使用该数据呈现的组件会有什么反应？

但这还不是最糟糕的。片段中最糟糕的部分是，如果道具中的*项*被作为*空值*接收，那么`items` [甚至不会被初始化为您提供的默认值](https://medium.com/better-programming/10-things-not-to-do-when-building-react-applications-bc26d4f38644)！

然后*你的应用会在开始做其他事情之前崩溃* :

```
"TypeError: Cannot convert undefined or null to object
    at Function.keys (<anonymous>)
    at yazeyafabu.js:4:45
    at https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:13924
    at https://static.jsbin.com/js/prod/runner-4.1.7.min.js:1:10866" 
```

Enter fullscreen mode Exit fullscreen mode

还是那句话，请小心！

## 4。渲染前不小心检查数组是否存在

这与第三种情况非常相似，但是数组和对象经常互换使用，它们应该有自己的部分。

如果你有这样做的习惯:

```
render() {
  const { arr } = this.props
  return (
    <div>
      {arr && arr.map()...}
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，确保你至少有单元测试来一直关注代码，或者在将它传递给 render 方法之前尽早正确处理`arr`，否则如果`arr`变成了*对象文字*，应用程序将会崩溃。当然，`&&`操作员会认为这是*真值*并尝试*。映射*最终会导致整个应用崩溃的对象文字。

所以请记住这一点。把你的精力和挫折留给更大的问题吧，这些问题更值得你特别关注！；)

## 5。不使用棉绒

如果您在开发应用程序时没有使用任何类型的 linter，或者您根本不知道它们是什么，请允许我详细说明一下为什么它们在开发中有用。

我在开发流程中用来帮助我的 linter 是 [ESLint](https://eslint.org/) ，这是一个非常著名的 JavaScript 林挺工具，它允许开发人员发现他们代码中的问题，甚至不需要执行它们。

这个工具非常有用，它可以充当你的半导师，因为它可以帮助你实时纠正错误——就像有人在指导你一样。它甚至*描述了为什么你的代码可能是糟糕的*，并建议你应该做些什么来替换它们！

这里有一个例子:

[![eslint](img/c62498456337eba506a6abc316c79696.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hum0-oqc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/8-practices-in-react-that-will-crash-your-app-in-the-future/eslint.jpg)

关于 eslint 最酷的事情是，如果你不喜欢某些规则或者不同意其中的一些，你可以简单地禁用某些规则，这样它们就不会在你开发的时候显示为林挺警告/错误。*让自己开心就好*对吧？

## 6。呈现列表时析构

我在过去看到过一些人发生这种情况，这并不总是一个容易发现的错误。基本上，当你有一个项目列表，你要为列表中的每一个项目呈现一堆组件时，你的应用程序可能会出现的错误是，如果将来列表中的一个项目不是你期望的值，如果它不知道如何处理值类型，你的应用程序可能会崩溃。

这里有一个例子:

```
const api = {
  async getTotalFrogs() {
    return {
      data: {
        result: [
          { name: 'bob the frog', tongueWidth: 50, weight: 8 },
          { name: 'joe the other frog', tongueWidth: 40, weight: 5 },
          { name: 'kelly the last frog', tongueWidth: 20, weight: 2 },
        ],
      },
    }
  },
}

const getData = async ({ withTongues = false }) => {
  try {
    const response = await api.getTotalFrogs({ withTongues })
    return response.data.result
  } catch (err) {
    throw err
  }
}

const DataList = (props) => {
  const [items, setItems] = useState([])
  const [error, setError] = useState(null)

  React.useEffect(() => {
    getData({ withTongues: true })
      .then(setItems)
      .catch(setError)
  }, [])

  return (
    <div>
      {Array.isArray(items) && (
        <Header size="tiny" inverted>
          {items.map(({ name, tongueWidth, weight }) => (
            <div style={{ margin: '25px 0' }}>
              <div>Name: {name}</div>
              <div>Width of their tongue: {tongueWidth}cm</div>
              <div>Weight: {weight}lbs</div>
            </div>
          ))}
        </Header>
      )}
      {error && <Header>You received an error. Do you need a linter?</Header>}
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

[![frogs1](img/9af8b28797fb082bbdd7671a6d3d9283.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eGnt2nak--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/8-practices-in-react-that-will-crash-your-app-in-the-future/frogs1.JPG)

代码会非常好地工作。现在，如果我们看看 api 调用，而不是返回这个:

```
const api = {
  async getTotalFrogs() {
    return {
      data: {
        result: [
          { name: 'bob the frog', tongueWidth: 50, weight: 8 },
          { name: 'joe the other frog', tongueWidth: 40, weight: 5 },
          { name: 'kelly the last frog', tongueWidth: 20, weight: 2 },
        ],
      },
    }
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

如果在 api 客户机中出现意外情况时，数据流的处理方式出现了问题，并返回了这个数组，该怎么办？

```
const api = {
  async getTotalFrogs() {
    return {
      data: {
        result: [
          { name: 'bob the frog', tongueWidth: 50, weight: 8 },
          undefined,
          { name: 'kelly the last frog', tongueWidth: 20, weight: 2 },
        ],
      },
    }
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

你的应用会崩溃，因为它不知道如何处理:

```
Uncaught TypeError: Cannot read property 'name' of undefined
    at eval (DataList.js? [sm]:65)
    at Array.map (<anonymous>)
    at DataList (DataList.js? [sm]:64)
    at renderWithHooks (react-dom.development.js:12938)
    at updateFunctionComponent (react-dom.development.js:14627) 
```

Enter fullscreen mode Exit fullscreen mode

所以为了防止你的程序崩溃，你可以在每次迭代中设置一个默认对象:

```
{
  items.map(({ name, tongueWidth, weight } = {}) => (
    <div style={{ margin: '25px 0' }}>
      <div>Name: {name}</div>
      <div>Width of their tongue: {tongueWidth}cm</div>
      <div>Weight: {weight}lbs</div>
    </div>
  ))
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当您的用户没有看到他们面前的页面崩溃时，他们不必对您的技术和专业知识做出判断:

[![frogs2](img/584ed5fe4dda82bea1c7a4ac01672cf3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UfwRofMp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/8-practices-in-react-that-will-crash-your-app-in-the-future/frogs2.jpg)

然而，即使应用程序不再崩溃，我还是建议更进一步，处理丢失的值，比如对有类似问题的整个项目返回 null，因为它们没有任何数据。

## 7。对你将要实现的东西没有做足够的研究

我过去犯的一个严重错误是对我实现的搜索输入过于自信，在游戏中过早地相信我的意见。

我这么说是什么意思？嗯，这不是我对搜索输入组件过于自信的原因。组件*应该是一个简单的*任务...的确如此。

整个搜索功能出现问题的真正原因是查询中包含的*字符。*

当我们将关键字作为查询发送到搜索 API 时，认为用户键入的每个键都是有效的并不总是足够的，即使他们因此而在键盘上。

只要 100%确定像这样的正则表达式工作正常，并且避免遗漏任何可能导致应用程序崩溃的无效字符:

```
const hasInvalidChars = /^.*?(?=[\+\^#%&$\*:<>\?/\{\|\}\[\]\\\)\(]).*$/g.test(
  inputValue,
) 
```

Enter fullscreen mode Exit fullscreen mode

这个例子是最新的、为搜索 API 建立的正则表达式。

这里是以前的样子:

```
const hasInvalidChars = /^.*?(?=[\+\^#%&$\*:<>\?/\{\|\}\[\]\)\(]).*$/g.test(
  inputValue,
)

const callApi = async (keywords) => {
  try {
    const url = `https://someapi.com/v1/search/?keywords=${keywords}/`
    return api.searchStuff(url)
  } catch (error) {
    throw error
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，斜线`/`不见了，这导致了应用程序崩溃！如果这个字符最终通过网络被发送给一个 API，猜猜 API 认为这个 URL 会是什么？

另外，我不会 100%相信你在网上找到的例子。它们中的许多都不是经过全面测试的解决方案，而且对于正则表达式来说，大多数用例都没有真正的标准。

## 7。不限制文件输入的大小

限制用户选择的文件大小是一个很好的做法，因为大多数情况下，当文件可以以某种方式压缩而不会失去任何明显的质量下降迹象时，您并不真正需要一个非常大的文件。

但是有一个更重要的原因可以解释为什么将尺寸限制在一定范围内是一种好的做法。在我的公司，我们注意到用户过去在上传图片时偶尔会“冻结”。并不是每个人都有外星人 17 R5，所以你必须考虑你的用户的某些情况。

下面是一个将文件限制在 5 MB (5，000，000 字节)的示例:

```
import React, { useState, useEffect } from 'react'

const useUploadStuff = () => {
  const [files, setFiles] = useState([])

  // Limit the file sizes here
  const onChange = (e) => {
    const arrFiles = Array.from(e.target.files)
    const filesUnder5mb = arrFiles.filter((file) => {
      const bytesLimit = 5000000
      if (file.size > bytesLimit) {
        // optionally process some UX about this file size
      }
      return file.size < bytesLimit
    })
    setFiles(filesUnder5mb)
  }

  useEffect(() => {
    if (files.length) {
      // do something with files
    }
  }, [files])

  return {
    files,
    onChange,
  }
}

const UploadStuff = () => {
  const { onChange } = useUploadStuff()

  return (
    <div>
      <h2 style={{ color: '#fff' }}>Hi</h2>
      <div>
        <input
          style={{ color: '#fff' }}
          onChange={onChange}
          type="file"
          placeholder="Upload Stuff"
          multiple
        />
      </div>
    </div>
  )
}

export default UploadStuff 
```

Enter fullscreen mode Exit fullscreen mode

你不会希望用户在应该上传文档的时候上传视频游戏！

## 结论

本帖到此结束！

将会有第二部分，因为我只完成了一半的清单(哎呀！)

无论如何，感谢您的阅读，并确保关注我的未来更新！7 月 4 日快乐！

在[媒体](https://medium.com/@jsmanifest)上找我！