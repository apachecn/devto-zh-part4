# 构建 React 应用时不要做的 10 件事

> 原文：<https://dev.to/jsmanifest/10-things-not-to-do-when-building-react-applications-58a7>

如果您还没有，请关注我的 [medium 【T1:)](https://medium.com/@jsmanifest)

react 是一个非常受欢迎的网络开发工具，我敢肯定，React 的粉丝们会觉得很幸运，因为他们有这么好的库:)

不幸的是，生活中没有什么是完美的，react 也不例外。

React 有它自己的一套*陷阱*——如果你不小心处理它们*现在*，其中一些可能会成为你的应用程序的严重问题。

以下是构建 React 应用时不要做的 10 件事:

## 1。花太多时间在自己的私人世界里

[![react isolation](img/72985a47df25b23d0de5b289fef2c21b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dWy_ufq0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/10-things-not-to-do-when-building-react-applications/1.jpg)

如果你花了太多时间编写项目中的所有代码，而没有花时间阅读社区中发生的事情，你*可能*有风险*编写社区中报告的不良实践*。你可能会冒着继续编写那些糟糕实践的风险，直到你做了 20 次，才终于有机会在一个中等的帖子上发现它是*坏*。

当这种情况发生时，*现在*你必须回去重构那 20 个代码实现，因为你发现得太晚了，而其他人都走在你前面，带着更新的消息继续前进。

当 react 发布 [hooks](https://reactjs.org/docs/hooks-overview.html) 时，我非常兴奋，开始构建一堆迷你项目来玩这些每个人都为之兴奋的新玩具。在阅读了一些关于 hooks 将会稳定的资料后，我开始在我的项目中更加认真地实现这些。我使用 *useState* 和 *useEffect* 就像一个老板*无处不在*。

然后我发现有人链接到了这条推特消息，这让我对 useReducer 做了更多的研究。

那 *30 分钟的研究*足以让我回去重构大量代码。

## 2。使用*。绑定*(非类组件构造函数)

我认为我们大多数 react 开发者都意识到我们应该*。如果我们想引用*这个*来访问它们方法中自己的类实例，那么绑定*我们的类方法。(除非你使用[转换器](https://babeljs.io/docs/en/babel-plugin-proposal-class-properties)来转换你的类属性和方法。

这很好，我同意更喜欢用箭头函数来声明它们。

但是我要讲的这部分不是关于这个的。它是关于*内联函数*——或者是在 react 组件的*渲染方法*中定义的函数，并作为道具传递给子组件。

当在 render 方法中定义内联函数时，react 开始在每次组件重新呈现时指定一个新的函数实例。众所周知，由于[浪费的重新渲染](https://www.freecodecamp.org/news/how-to-identify-and-resolve-wasted-renders-in-react-cc4b1e910d10/)，这会导致性能问题。

我们来看看这个例子:

```
const ShowMeTheMoney = () => {
  const [money, setMoney] = useState(0)

  const showThemTheMoney = (money) => {
    setMoney(money)
  }

  const hideTheMoney = () => {
    setMoney(null)
  }

  const sayWhereTheMoneyIs = (msg) => {
    console.log(msg)
  }

  return (
    <div>
      <h4>Where is the money?</h4>
      <hr />
      <div style={{ display: 'flex', alignItems: 'center' }}>
        <SomeCustomButton
          type="button"
          onClick={() => sayWhereTheMoneyIs("I don't know")}
        >
          I'll tell you
        </SomeCustomButton>{'  '}
        <SomeCustomButton type="button" onClick={() => showThemTheMoney(0.05)}>
          I'll show you
        </SomeCustomButton>
      </div>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

我们*知道*的`onClick={() => sayWhereTheMoneyIs("I don't know")}`和`onClick={() => showThemTheMoney(0.05)}`是*的内联函数*。

我看过几个鼓励这样做的教程(包括一个来自 Udemy 的教程):

```
return (
  <div>
    <h4>Where is the money?</h4>
    <hr />
    <div style={{ display: 'flex', alignItems: 'center' }}>
      <SomeCustomButton
        type="button"
        onClick={sayWhereTheMoneyIs.bind(null, "I don't know")}
      >
        I'll tell you
      </SomeCustomButton>{'  '}
      <SomeCustomButton
        type="button"
        onClick={showThemTheMoney.bind(null, 0.05)}
      >
        I'll show you
      </SomeCustomButton>
    </div>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

这个*看起来*像是缓存了引用，从而避免了不必要的重新渲染，因为他们*在渲染方法*中没有使用箭头内嵌函数，但是他们实际上仍然[在每个渲染阶段创建新的函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind)！

我们中的一些人可能已经知道，如果我们一直在 react 生态系统中关注类组件趋势的话。

然而，自从 react hooks 发布以来。自从类组件变得不那么受欢迎以来，bind 就被抛弃了。绑定是谈论的话题，通常*是关于绑定类方法的*。此外，上面的这些例子甚至根本没有绑定到类方法，所以如果你不够小心，就更难注意到这里的后果。

*新人*应该特别注意这种反模式！

## 3。将动态值作为键传递给子级

你有没有遇到过这样的情况:你*感到*被迫向被映射的孩子提供唯一的密钥？

最好提供唯一的键:

```
const Cereal = ({ items, ...otherProps }) => {
  const indexHalf = Math.floor(items.length / 2)
  const items1 = items.slice(0, indexHalf)
  const items2 = items.slice(indexHalf)
  return (
    <>
      <ul>
        {items1.map(({ to, label }) => (
          <li key={to}>
            <Link to={to}>{label}</Link>
          </li>
        ))}
      </ul>
      <ul>
        {items2.map(({ to, label }) => (
          <li key={to}>
            <Link to={to}>{label}</Link>
          </li>
        ))}
      </ul>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

现在假设*项 1* 中的一些*到*值恰好与*项 2* 中的一些值相同。

我已经看到，当一些人想要重构一个类似的组件时，他们最终会这样做:

```
import { generateRandomUniqueKey } from 'utils/generating'

const Cereal = ({ items, ...otherProps }) => {
  const indexHalf = Math.floor(items.length / 2)
  const items1 = items.slice(0, indexHalf)
  const items2 = items.slice(indexHalf)
  return (
    <>
      <ul>
        {items1.map(({ to, label }) => (
          <li key={generateRandomUniqueKey()}>
            <Link to={to}>{label}</Link>
          </li>
        ))}
      </ul>
      <ul>
        {items2.map(({ to, label }) => (
          <li key={generateRandomUniqueKey()}>
            <Link to={to}>{label}</Link>
          </li>
        ))}
      </ul>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这个*完成了为每个孩子提供唯一密钥的工作。但是有两件事是错的:*

1.  我们不仅让 react 做了不必要的生成唯一值的工作，而且我们还在每次渲染时都要重新创建所有的节点，因为键每次都是不同的。

2.  react 中的*关键*概念是*关于身份的一切*。并且为了识别哪个组件是哪个，键 *do* 需要是唯一的，但是*不像那个*。

类似这样的事情会变得更好一点:

```
import { generateRandomUniqueKey } from 'utils/generating'

const Cereal = ({ items, ...otherProps }) => {
  const indexHalf = Math.floor(items.length / 2)
  const items1 = items.slice(0, indexHalf)
  const items2 = items.slice(indexHalf)
  return (
    <>
      <ul>
        {items1.map(({ to, label }) => (
          <li key={`items1_${to}`}>
            <Link to={to}>{label}</Link>
          </li>
        ))}
      </ul>
      <ul>
        {items2.map(({ to, label }) => (
          <li key={`items2_${to}`}>
            <Link to={to}>{label}</Link>
          </li>
        ))}
      </ul>
    </>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们应该有信心，每个项目都有自己唯一的键值，同时*保持*它们的身份。

## 4。在 Null 上声明默认参数

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

在我们的 *App* 组件中，如果*日期*最终为*假*，它将被初始化为*空*。

如果我们运行代码——如果你像我一样，我们的直觉告诉我们，默认情况下，*项*应该初始化为空数组，如果它是 falsey 值。但是当*日期*为假时，我们的应用程序会崩溃，因为*项目*为空。什么？

如果 [*没有值或未定义的*被传递](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)，默认函数参数允许命名参数用默认值初始化！

在我们的例子中，即使 *null* 为 falsey，它仍然是一个值！

这个错误让我花了很多时间去调试，尤其是当 *null* 值来自 redux reducers 的时候！呃。

## 5。保持重复代码不变

当你被催着推出一个补丁时，复制和粘贴代码是很有诱惑力的，因为有时这可能是最快的解决方案。

下面是一个重复代码的例子:

```
const SomeComponent = () => (
  <Body noBottom>
    <Header center>Title</Header>
    <Divider />
    <Background grey>
      <Section height={500}>
        <Grid spacing={16} container>
          <Grid xs={12} sm={6} item>
            <div className={classes.groupsHeader}>
              <Header center>Groups</Header>
            </div>
          </Grid>
          <Grid xs={12} sm={6} item>
            <div>
              <img src={photos.groups} alt="" className={classes.img} />
            </div>
          </Grid>
        </Grid>
      </Section>
    </Background>
    <Background grey>
      <Section height={500}>
        <Grid spacing={16} container>
          <Grid xs={12} sm={6} item>
            <div className={classes.labsHeader}>
              <Header center>Labs</Header>
            </div>
          </Grid>
          <Grid xs={12} sm={6} item>
            <div>
              <img src={photos.labs} alt="" className={classes.img} />
            </div>
          </Grid>
        </Grid>
      </Section>
    </Background>
    <Background grey>
      <Section height={300}>
        <Grid spacing={16} container>
          <Grid xs={12} sm={6} item>
            <div className={classes.partnersHeader}>
              <Header center>Partners</Header>
            </div>
          </Grid>
          <Grid xs={12} sm={6} item>
            <div>
              <img src={photos.partners} alt="" className={classes.img} />
            </div>
          </Grid>
        </Grid>
      </Section>
    </Background>
  </Body>
) 
```

Enter fullscreen mode Exit fullscreen mode

现在是开始考虑如何抽象这些组件的时候了，在不改变实现的情况下，这些组件可以多次重用*。如果某个*网格*组件相对于其周围的网格容器存在样式问题，那么您必须手动更改它们中的每一个。*

一个更好的编码方式可能是抽象出重复的部分，并传入稍微不同的道具:

```
const SectionContainer = ({
  bgProps,
  height = 500,
  header,
  headerProps,
  imgProps,
}) => (
  <Background {...bgProps}>
    <Section height={height}>
      <Grid spacing={16} container>
        <Grid xs={12} sm={6} item>
          <div {...headerProps}>
            <Header center>{header}</Header>
          </div>
        </Grid>
        <Grid xs={12} sm={6} item>
          <div>
            <img {...imgProps} />
          </div>
        </Grid>
      </Grid>
    </Section>
  </Background>
)

const SomeComponent = () => (
  <Body noBottom>
    <Header center>Title</Header>
    <Divider />
    <SectionContainer
      header="Groups"
      headerProps={{ className: classes.groupsHeader }}
      imgProps={{ src: photos.groups, className: classes.img }}
    />
    <SectionContainer
      bgProps={{ grey: true }}
      header="Labs"
      headerProps={{ className: classes.labsHeader }}
      imgProps={{ src: photos.labs, className: classes.img }}
    />
    <SectionContainer
      height={300}
      header="Partners"
      headerProps={{ className: classes.partnersHeader }}
      imgProps={{ src: photos.partners, className: classes.img }}
    />
  </Body>
) 
```

Enter fullscreen mode Exit fullscreen mode

所以现在如果你的老板最终改变主意，想把所有这些部分的高度都做成大约 300 像素，你只有在 T2 的一个地方可以修改。

现在，如果我们希望制作一个支持多种用例的组件，我并不是想推荐这样的解决方案，这是为了我们*知道*它将只在那个环境中被重用*的特定用途。支持多种用例的 *SectionContainer* 的一个更动态的可重用解决方案可能会被编码成更通用的这样，*仍然不需要*改变实现:* 

```
const SectionContainer = ({
  bgProps,
  sectionProps,
  children,
  gridContainerProps,
  gridColumnLeftProps,
  gridColumnRightProps,
  columnLeft,
  columnRight,
}) => (
  <Background {...bgProps}>
    <Section {...sectionProps}>
      {children || (
        <Grid spacing={16} container {...gridContainerProps}>
          <Grid xs={12} sm={6} item {...gridColumnLeftProps}>
            {columnLeft}
          </Grid>
          <Grid xs={12} sm={6} item {...gridColumnRightProps}>
            {columnRight}
          </Grid>
        </Grid>
      )}
    </Section>
  </Background>
) 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们现在允许开发人员在保留底层实现的同时，根据需要随意扩展组件的任何部分。

## 6。初始化构造函数中的属性

当你在构造函数中初始化状态:

```
import React from 'react'

class App extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      items: props.items,
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能会遇到错误。这是因为*构造函数*只被调用一次*，这是组件第一次被创建的时间。*

 *下一次你试图改变道具时，状态将会保留它之前的值，因为在重新渲染时不会调用构造函数。

如果你还没有遇到这个问题，希望这对你有所帮助！

如果你想知道如何让道具与状态同步，一个更好的方法应该是这样的:

```
import React from 'react'

class App extends React.Component {
  constructor(props) {
    super(props)
    // Initialize the state on mount
    this.state = {
      items: props.items,
    }
  }

  // Keep the state in sync with props in further updates
  componentDidUpdate = (prevProps) => {
    const items = []
    // after  calculations comparing prevProps with this.props
    if (...) {
      this.setState({ items })
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 7。有条件渲染用 *& &*

当有条件地呈现组件时，一个常见的*陷阱*是使用& &操作符。

如果一个条件不满足它的要求，React 将试图把你提供的任何东西作为替代输出呈现给你。因此，当我们看这个:

```
const App = ({ items = [] }) => (
  <div>
    <h2>Here are your items:</h2>
    <div>
      {items.length &&
        items.map((item) => <div key={item.label}>{item.label}</div>)}
    </div>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

当 *items.length* 为空时，这实际上会在屏幕上呈现一个数字 *0* 。JavaScript 认为数字 *0* 是一个[假值](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)，所以当*项*是一个空数组时， *& &* 操作符不会计算它右边的表达式，只会返回第一个值。

如果我想保留语法，我通常会使用双重否定:

```
const App = ({ items = [] }) => (
  <div>
    <h2>Here are your items:</h2>
    <div>
      {!!items.length &&
        items.map((item) => <div key={item.label}>{item.label}</div>)}
    </div>
  </div>
) 
```

Enter fullscreen mode Exit fullscreen mode

这样，如果 *items* 是一个空数组，react 将不会在屏幕上呈现任何内容，如果计算的输出是一个布尔值。

## 8。不传播以前的状态

一些偶尔会爬上我的错误列表的东西来自于不小心实现的状态更新逻辑。

最近的一个例子涉及 react 钩子，特别是一个 *useReducer* 实现。这是一个成为问题的基本例子:

```
const something = (state) => {
  let newState = { ...state }
  const indexPanda = newState.items.indexOf('panda')
  if (indexPanda !== -1) {
    newState.items.splice(indexPanda, 1)
  }
  return newState
}

const initialState = {
  items: [],
}

const reducer = (state, action) => {
  switch (action.type) {
    case 'add-item':
      return { ...state, items: [...something(state).items, action.item] }
    case 'clear':
      return { ...initialState }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当 *something* 函数调用并复制状态时，底层的*项*属性没有改变。当我们用*使它变异时。拼接*，这会使*状态项*发生变异，并会引入 bug。

在更大的代码中尤其会对此感到厌倦。我们可能都会遇到类似上面的小例子，但是当事情变得混乱时，这个*总是*必须时刻牢记在心，因为它很容易忘记，尤其是当你被迫将代码交付给产品时！

## 9。没有显式地将属性传递给子组件

通常推荐的做法是，在传递给子组件的属性中要明确。

这有几个很好的理由:

1.  更轻松的调试体验
    1.  作为一个开发者，你知道传递给每个孩子的是什么。
        1.  其他开发人员也会知道这一点，并且会更容易阅读代码
2.  更容易理解组件将做什么
    1.  传递 props explicity 的另一个好处是，当你这样做时，它也以一种每个人都理解的方式记录了你的代码，甚至不需要正式的文档。而且那个*节省时间*！
3.  为了确定组件是否应该重新渲染，需要的*道具会更少*。

虽然*可以*有一些很好的用例来传播*所有的*道具。

例如，如果在将道具传递给子组件之前，父组件很快需要一两件东西，那么他们(和您)很容易就能做到:

```
const Parent = (props) => {
  if (props.user && props.user.email) {
    // Fire some redux action to update something globally that another
    //    component might need to know about
  }

  // Continue on with the app
  return <Child {...props} />
} 
```

Enter fullscreen mode Exit fullscreen mode

只要确保你不会发现自己处于这样的情况:

```
<ModalComponent
  open={aFormIsOpened}
  onClose={() => closeModal(formName)}
  arial-labelledby={`${formName}-modal`}
  arial-describedby={`${formName}-modal`}
  classes={{
    root: cx(classes.modal, { [classes.dialog]: shouldUseDialog }),
    ...additionalDialogClasses,
  }}
  disableAutoFocus
>
  <div>
    {!dialog.opened && (
      <ModalFormRoot
        animieId={animieId}
        alreadySubmitted={alreadySubmitted}
        academy={academy}
        user={user}
        clearSignature={clearSignature}
        closeModal={closeModal}
        closeImageViewer={closeImageViewer}
        dialog={dialog}
        fetchAcademyMember={fetchAcademyMember}
        formName={formName}
        formId={formId}
        getCurrentValues={getCurrentValues}
        header={header}
        hideActions={formName === 'signup'}
        hideClear={formName === 'review'}
        movieId={movie}
        tvId={tvId}
        openPdfViewer={openPdfViewer}
        onSubmit={onSubmit}
        onTogglerClick={onToggle}
        seniorMember={seniorMember}
        seniorMemberId={seniorMemberId}
        pdfViewer={pdfViewer}
        screenViewRef={screenViewRef}
        screenRef={screenRef}
        screenInputRef={screenInputRef}
        updateSignupFormValues={updateSignupFormValues}
        updateSigninFormValues={updateSigninFormValues}
        updateCommentFormValues={updateCommentFormValues}
        updateReplyFormValues={updateReplyFormValues}
        validateFormId={validateFormId}
        waitingForPreviousForm={waitingForPreviousForm}
        initialValues={getCurrentValues(formName)}
        uploadStatus={uploadStatus}
        uploadError={uploadError}
        setUploadError={setUploadError}
        filterRolesFalseys={filterRolesFalseys}
      />
    )}
  </div>
</ModalComponent> 
```

Enter fullscreen mode Exit fullscreen mode

如果你这样做了，考虑将组件部分拆分成单独的组件，这样会更干净，更具可定制性。

## 10。支柱钻井

将道具传递给多个子组件就是他们所说的*“代码味道”*。

如果你不知道什么是道具钻取，这意味着当一个父母将道具传递给树深处的多级组件时。

现在问题不在于父母也不在于孩子。他们*应该保持他们的实现*不变。中间的*组件*可能会成为 react 应用中的一个问题。

这是因为现在中间的组件紧密耦合，暴露了太多他们根本不需要的信息。最糟糕的是，当父组件重新渲染时，中间的*组件也会重新渲染*，对链上的所有子组件产生多米诺骨牌效应。

一个好的解决方案是使用[上下文](https://reactjs.org/docs/context.html)来代替。或者，道具的 *redux* (然而这些道具将被连载)。

## 结论

这篇文章到此结束:)我希望这篇文章对你有所帮助，并确保关注我的后续文章！

如果您还没有，请关注我的 [medium 【T1:)](https://medium.com/@jsmanifest)*