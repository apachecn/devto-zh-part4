# 在 React 应用中编写更简洁代码的 14 个有益技巧

> 原文：<https://dev.to/jsmanifest/14-beneficial-tips-to-write-cleaner-code-in-react-apps-1gcf>

在[媒体](https://medium.com/@jsmanifest)上找到我

编写干净的代码在你职业生涯的某个时刻变得必不可少，尤其是在你试图获得第一份开发工作的时候。这是让你成为一名团队成员的基本要素，你可以在面试中打破僵局，也可以获得成功。你如何写代码是他们在做出雇佣决定之前想要考虑的事情之一。你的代码应该是人类可以理解的，而不仅仅是机器。

本文中列出的东西在你的项目变得越大时应该越适用，而对于小项目来说*可能*就没必要了。用你最好的判断力:)

以下是在 React 应用中编写更简洁代码的 14 个有益技巧:

## 1。摧毁你的道具

析构你的道具是一个很好的方法，有助于使你的编码器更干净，更易维护。这是因为您清楚地定义或声明了某个东西(比如一个组件)正在使用什么，并且它不会强迫开发人员通读组件的实现来找出与组件相关联的所有道具。

它还让您能够为它们声明默认值，您可能已经见过很多次了:

```
import React from 'react'
import Button from 'components/Button'

const MyComponent = ({ placeholder = '', style, ...otherProps }) => {
  return (
    <Button
      type="button"
      style={{
        border: `1px solid ${placeholder ? 'salmon' : '#333'}`,
        ...style,
      }}
      {...otherProps}
    >
      Click Me
    </Button>
  )
}

export default MyComponent 
```

Enter fullscreen mode Exit fullscreen mode

关于 JavaScript 中的析构，我发现最酷的事情之一是它允许你支持不同的参数变化。

例如，如果您有一个 *authenticate* 函数，该函数过去接受一个`token`作为参数来认证用户，现在由于一个新的服务器响应结构而希望接受一个`jwt_token`，那么您可以很容易地支持这两个参数，而不需要修改太多代码:

```
// before refactoring
async function authenticate({ user_id, token }) {
  try {
    const response = await axios.post('https://someapi.com/v1/auth/', {
      user_id,
      token,
    })
    console.log(response)
    return response.data
  } catch (error) {
    console.error(error)
    throw error
  }
}

// after refactoring
async function authenticate({ user_id, jwt_token, token = jwt_token }) {
  try {
    const response = await axios.post('https://someapi.com/v1/auth/', {
      user_id,
      token,
    })
    console.log(response)
    return response.data
  } catch (error) {
    console.error(error)
    throw error
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在代码到达`token`时`jwt_token`将被求值，所以如果`jwt_token`是有效令牌而`token`是`undefined`，那么`token`的值将成为`jwt_token`的值。如果`token`已经是某个真值(一个真实的令牌)，它将保持自己。

## 2。文件夹化您的组件

让我们看看下面的目录结构:

*   科学研究委员会
    *   成分
    *   Breadcrumb.js
    *   CollapsedSeparator.js
    *   投入
        *   索引. js
        *   输入. js
        *   utils.js
        *   focusManager.js
    *   卡片
        *   索引. js
        *   Card.js
        *   carddivider . js-共用函数
    *   Button.js
    *   印刷术. js

众所周知，面包屑作为其核心功能之一，与某种分隔符相关联。`CollapsedSeparator`组件是在`Breadcrumb.js`中导入的，所以我们知道它们在实现上都是相关的。然而，不知道这些信息的人可能会认为`Breadcrumb`和`CollapsedSeparator`是两个完全独立的组件，彼此之间没有任何关系——特别是如果`CollapsedSeparator`没有任何明确的迹象表明它与面包屑相关，例如前缀为*bread crumb*(bread crumb collapsed separator . js)。

既然我们知道它们是相关的，我们可能会问为什么它们不像 Input 和 Card 一样在一个文件夹中，并开始做出奇怪的假设，如“我想知道是否有人把它放在那里，看我是否会像一个好撒玛利亚人一样拿出来……”。干净代码实践的效果应该是相反的——开发人员应该能够阅读您的代码，并很快理解这种情况！

面包屑看起来像这样:

*   科学研究委员会
    *   成分
    *   面包屑
        *   索引. js
        *   Breadcrumb.js
        *   CollapsedSeparator.js
    *   投入
        *   索引. js
        *   输入. js
        *   utils.js
        *   focusManager.js
    *   卡片
        *   索引. js
        *   Card.js
        *   carddivider . js-共用函数
    *   Button.js
    *   印刷术. js

现在，无论此后创建了多少个与*面包屑*相关的组件，只要它们驻留在同一个目录中，我们就会*总是*知道它们与`Breadcrumb`相关:

*   科学研究委员会
    *   成分
    *   面包屑
        *   索引. js
        *   Breadcrumb.js
        *   CollapsedSeparator.js
        *   Expander.js
        *   BreadcrumbText.js
        *   BreadcrumbHotdog.js
        *   bread bread fishs . js
        *   面包屑剩菜. js
        *   BreadcrumbHead.js
        *   BreadcrumbAddict.js
        *   bread bread dragon 0814 . js
        *   BreadcrumbContext.js
    *   投入
        *   索引. js
        *   输入. js
        *   utils.js
        *   focusManager.js
    *   卡片
        *   索引. js
        *   Card.js
        *   carddivider . js-共用函数
    *   Button.js
    *   印刷术. js

```
import React from 'react'
import Breadcrumb, {
  CollapsedSeparator,
  Expander,
  BreadcrumbText,
  BreadcrumbHotdog,
  BreadcrumbFishes,
  BreadcrumbLeftOvers,
  BreadcrumbHead,
  BreadcrumbAddict,
  BreadcrumbDragon0814,
} from '../../../../../../../../../../components/Breadcrumb'

const withBreadcrumbHotdog = (WrappedComponent) => (props) => (
  <WrappedComponent BreadcrumbHotdog={BreadcrumbHotdog} {...props} />
)

const WorldOfBreadcrumbs = ({
  BreadcrumbHotdog: BreadcrumbHotdogComponent,
}) => {
  const [hasFishes, setHasFishes] = React.useState(false)

  return (
    <BreadcrumbDragon0814
      hasFishes={hasFishes}
      render={(results) => (
        <BreadcrumbFishes>
          {({ breadcrumbFishes }) => (
            <BreadcrumbLeftOvers.Provider>
              <BreadcrumbHotdogComponent>
                <Expander>
                  <BreadcrumbText>
                    <BreadcrumbAddict>
                      <pre>
                        <code>{JSON.stringify(results, null, 2)}</code>
                      </pre>
                    </BreadcrumbAddict>
                  </BreadcrumbText>
                </Expander>
                {hasFishes
                  ? breadcrumbFishes.map((fish) => (
                      <>
                        {fish}
                        <CollapsedSeparator />
                      </>
                    ))
                  : null}
              </BreadcrumbHotdogComponent>
            </BreadcrumbLeftOvers.Provider>
          )}
        </BreadcrumbFishes>
      )}
    />
  )
}

export default withBreadcrumbHotdog(WorldOfBreadcrumbs) 
```

Enter fullscreen mode Exit fullscreen mode

## 3。使用标准命名约定命名组件

使用标准约定命名组件使得其他开发人员更容易阅读您的代码。

例如，[高阶成分](https://reactjs.org/docs/higher-order-components.html)通常会加上前缀`with`，这是大多数人习惯的:

```
import React from 'react'
import hoistNonReactStatics from 'hoist-non-react-statics'
import getDisplayName from 'utils/getDisplayName'

const withFreeMoney = (WrappedComponent) => {
  class WithFreeMoney extends React.Component {
    giveFreeMoney() {
      return 50000
    }

    render() {
      return (
        <WrappedComponent
          additionalMoney={[
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
          ]}
          {...this.props}
        />
      )
    }
  }

  WithFreeMoney.displayName = `withFreeMoney(${getDisplayName(
    WrappedComponent,
  )}$)`
  hoistNonReactStatics(WithFreeMoney, WrappedComponent)

  return WithFreeMoney
}

export default withFreeMoney 
```

Enter fullscreen mode Exit fullscreen mode

如果你决定像这样做一些不同的事情:

```
import React from 'react'
import hoistNonReactStatics from 'hoist-non-react-statics'
import getDisplayName from 'utils/getDisplayName'

const useFreeMoney = (WrappedComponent) => {
  class WithFreeMoney extends React.Component {
    giveFreeMoney() {
      return 50000
    }

    render() {
      return (
        <WrappedComponent
          additionalMoney={[
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
            this.giveFreeMoney(),
          ]}
          {...this.props}
        />
      )
    }
  }

  WithFreeMoney.displayName = `useFreeMoney(${getDisplayName(
    WrappedComponent,
  )}$)`
  hoistNonReactStatics(WithFreeMoney, WrappedComponent)

  return WithFreeMoney
}

export default useFreeMoney 
```

Enter fullscreen mode Exit fullscreen mode

这是完全有效的 JavaScript，以这种方式命名没有什么不对。但是已经有了一个标准的命名惯例，用[反应钩子](https://reactjs.org/docs/hooks-reference.html)已经到达现场的`use`。只是当你分享你的代码时要小心，尤其是当你寻求帮助时，因为人们可能已经习惯了每天看到共同的约定。

## 4。避免布尔陷阱

当涉及到原始布尔值来确定某个东西的输出值时，在决定输出时必须格外小心。众所周知，这是一种代码味道，它迫使开发人员查看组件的源代码/实现，以便能够对最终结果做出准确的假设。

例如，如果我们声明了一个采用这些可用选项的排版组件:`'h1'`、`'h2'`、`'h3'`、`'h4'`、`'h5'`、`'h6'`、`'title'`、`'subheading'`

当它们像这样传入时，你怎么知道它们将如何被应用？

```
const App = () => (
  <Typography color="primary" align="center" subheading title>
    Welcome to my bio
  </Typography>
) 
```

Enter fullscreen mode Exit fullscreen mode

那些对 React(或者更恰当地说， *JavaScript* )更有经验的人可能已经猜到`title`会超过`subheading`，因为按照排序的方式，最后一个会覆盖前一个。

但问题是，如果不看源代码，我们将无法真正说出`title`或`subheading`将被应用到什么程度。

例如:

```
.title {
  font-size: 1.2rem;
  font-weight: 500;
  text-transform: uppercase;
}

.subheading {
  font-size: 1.1rem;
  font-weight: 400;
  text-transform: none !important;
} 
```

Enter fullscreen mode Exit fullscreen mode

即使`title`赢了，`text-transform: uppercase` CSS 行*仍然不会被应用*，因为`subheading`在它的实现中用`text-transform: none !important;`声明了更高的特异性。如果我们不够小心，调试样式问题*可能会变得非常困难，尤其是当它不会向控制台*显示任何警告/错误时。这可能会使组件的签名复杂化。

这里只是一个更干净的替代方案的例子，它重新实现了解决这个问题的`Typography`组件:

```
const App = () => <Typography variant="title">Welcome to my bio</Typography> 
```

Enter fullscreen mode Exit fullscreen mode

**排版**

```
import React from 'react'
import cx from 'classnames'
import styles from './styles.css'

const Typography = ({
  children,
  color = '#333',
  align = 'left',
  variant,
  ...otherProps
}) => {
  return (
    <div
      className={cx({
        [styles.h1]: variant === 'h1',
        [styles.h2]: variant === 'h2',
        [styles.h3]: variant === 'h3',
        [styles.h4]: variant === 'h4',
        [styles.h5]: variant === 'h5',
        [styles.h6]: variant === 'h6',
        [styles.title]: variant === 'title',
        [styles.subheading]: variant === 'subheading',
      })}
    >
      {children}
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们在`App`组件中传递`variant="title"`时，我们将确信只有`title`会被应用，这让我们省去了查看源代码来确定结果的麻烦。

你也可以做一个简单的 if/else 来计算道具:

```
let result
if (variant === 'h1') result = styles.h1
else if (variant === 'h2') result = styles.h2
else if (variant === 'h3') result = styles.h3
else if (variant === 'h4') result = styles.h4
else if (variant === 'h5') result = styles.h5
else if (variant === 'h6') result = styles.h6
else if (variant === 'title') result = styles.title
else if (variant === 'subheading') result = styles.subheading 
```

Enter fullscreen mode Exit fullscreen mode

但是这样做的最大好处是，你可以做这个简单、干净的一行程序，然后就可以收工了:

```
const result = styles[variant] 
```

Enter fullscreen mode Exit fullscreen mode

## 5。使用粗箭头功能

使用粗箭头函数是在 JavaScript 中声明函数的一种更短、更简洁的方式(在本例中更恰当地命名为*函数表达式*)。

然而，有些时候你不希望在函数表达式中使用粗箭头函数，比如当你需要提升的时候。

在 React 中，同样的概念同样适用。然而，如果你不需要提升，使用箭头语法是一个更好的选择(在我看来):

```
// Function declaration version
function Gallery({ title, images = [], ...otherProps }) {
  return (
    <CarouselContext.Provider>
      <Carousel>
        {images.map((src, index) => (
          <img src={src} key={`img_${index}`} />
        ))}
      </Carousel>
    </CarouselContext.Provider>
  )
}

// Arrow / Function expression version
const Gallery = ({ title, images = [], ...otherProps }) => (
  <CarouselContext.Provider>
    <Carousel>
      {images.map((src, index) => (
        <img src={src} key={`img_${index}`} />
      ))}
    </Carousel>
  </CarouselContext.Provider>
) 
```

Enter fullscreen mode Exit fullscreen mode

但是在这个例子中你很难看出它的好处...当你做简单的一行程序时，箭头函数的魅力大放异彩:

```
// Function declaration version
function GalleryPage(props) {
  return <Gallery {...props} />
}

// Arrow / Function expression version
const GalleryPage = (props) => <Gallery {...props} /> 
```

Enter fullscreen mode Exit fullscreen mode

俏皮话让每个人都开心！:)

## 6。把独立的函数放在你的定制钩子之外

我看到一些人在他们的定制钩子中声明他们并不真正需要的函数。这使得定制钩子变得有点臃肿，随着它变得越来越长，越来越难阅读，因为一些开发人员可能会开始质疑钩子是否真的*依赖于钩子内部的函数。如果不是，最好把它移到外面，这样就能清楚地了解钩子的依赖项是什么，哪些不是。*

这里有一个例子:

```
import React from 'react'

const initialState = {
  initiated: false,
  images: [],
}

const reducer = (state, action) => {
  switch (action.type) {
    case 'initiated':
      return { ...state, initiated: true }
    case 'set-images':
      return { ...state, images: action.images }
    default:
      return state
  }
}

const usePhotosList = ({ imagesList = [] }) => {
  const [state, dispatch] = React.useReducer(reducer, initialState)

  const removeFalseyImages = (images = []) =>
    images.reduce((acc, img) => (img ? [...acc, img] : acc), [])

  React.useEffect(() => {
    const images = removeFalseyImages(imagesList)
    dispatch({ type: 'initiated' })
    dispatch({ type: 'set-images', images })
  }, [])

  return {
    ...state,
  }
}

export default usePhotosList 
```

Enter fullscreen mode Exit fullscreen mode

看一下这个例子，`removeFalseyImages`实际上不需要在定制钩子中，而是可以在之外提取*,并且仍然可以在钩子内部使用，没有任何问题，因为它不与它的任何状态交互。*

## 7。保持一致

保持一致也是 JavaScript 中普遍推荐的方法。

至于 React，保持一致:

1.  进口和出口
2.  命名组件、挂钩、HOC、类名

当导入和导出组件时，当我想在两者之间放置导出时，我有时喜欢使用这个语法:

```
import App from './App'

export { default as Breadcrumb } from './Breadcrumb'

export default App 
```

Enter fullscreen mode Exit fullscreen mode

但是我同样喜欢这个语法:

```
export { default } from './App'
export { default as Breadcrumb } from './Breadcrumb' 
```

Enter fullscreen mode Exit fullscreen mode

无论你喜欢做哪一个，只要确保你坚持为每个项目选择一个，这样就简单了。

与命名约定保持一致也是一条非常重要的规则。

当你定义一个像`useApp`这样的钩子时，重要的是用前缀`use`命名你的下一个钩子，像`useController`。

如果你不这样做，你最终会这样做:

```
// custom hook #1
const useApp = ({ data: dataProp = null }) => {
  const [data, setData] = React.useState(dataProp)

  React.useEffect(() => {
    setData(data)
  }, [])

  return {
    data,
  }
}

// custom hook #2
const basicController = ({ device: deviceProp }) => {
  const [device, setDevice] = React.useState(deviceProp)

  React.useEffect(() => {
    if (!device && deviceProp) {
      setDevice(deviceProp === 'mobile' ? 'mobile' : 'desktop')
    }
  }, [deviceProp])

  return {
    device,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

导入两个钩子:

```
import React from 'react'
import useApp from './useApp'
import basicController from './basicController'

const App = () => {
  const app = useApp()
  const controller = basicController()

  return (
    <div>
      {controller.errors.map((errorMsg) => (
        <div>{errorMsg}</div>
      ))}
    </div>
  )
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

不太明显的是，`basicController`是一个自定义的 react 挂钩，就像`useApp`一样，并迫使开发人员查看和读取代码内部，以真正找出真相。如果我们保持它的一致性，它就不会变成那样，因为我们可以让它变得明显:

```
const app = useApp()
const controller = useBasicController() 
```

Enter fullscreen mode Exit fullscreen mode

## 8。组件化重复元素

组件化只是“将重复的元素转换成它们自己的可重用组件”的一种花哨说法。

每个人都有在 React 中编写重复代码的理由，无论是有意还是无意。

不管是什么原因，不要让大量的代码原封不动是个好主意。

首先，你可能正在形成一种习惯，可能会再次这样做，因为你不关心以前的重复代码。你这样做是如何成为一名团队成员的？你在将来给你的队友增加了负担，因为他们可能会因为看到重复的元素而感到沮丧，甚至会感到困惑，尤其是当他们被安排去编辑它们的时候。

对他们来说，最糟糕的是当他们甚至没有写代码的时候，却被重复的代码批评。当他们这么做的时候，就代表你为团队做一件事。通过避免将来的重复来回报他们！

让我们看看下面的代码，并将重复的部分组件化:

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
    <div>
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
    </div>
  </Body>
) 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果有人告诉你将网格大小从`xs={12} sm={6}`更改为`xs={12} sm={4}`，这将成为一个麻烦，因为你必须更改四次。

强制执行的美妙之处在于，你只需做出一个改变，它就会在所有网格中反映出来

```
const SomeComponent = ({ classes, xs = 12, sm = 6, md, lg }) => {
  const BodySection = ({ header, src }) => {
    const gridSizes = { xs, sm, md, lg }
    return (
      <Section height={500}>
        <Grid spacing={16} container>
          <Grid {...gridSizes} item>
            <div className={classes.groupsHeader}>
              <Header center>{header}</Header>
            </div>
          </Grid>
          <Grid {...gridSizes} item>
            <div>
              <img src={src} alt="" className={classes.img} />
            </div>
          </Grid>
        </Grid>
      </Section>
    )
  }

  return (
    <Body noBottom>
      <Header center>Title</Header>
      <Divider />
      <Background grey>
        <BodySection header="Groups" src={photos.groups} />
      </Background>
      <div>
        <BodySection header="Labs" src={photos.labs} />
      </div>
    </Body>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

在最基本的提取层面上，这变得更容易让人阅读和维护，同时仍然保持正常的实现！

## 9。保持你的组件简单

我在制作 web 应用时学到的东西不是让你的组件简单，而是避免让你的组件变得复杂。

下面是一个不必要复杂的组件示例:

*confirm avail ability . js*T2】

```
import React from 'react'
import Grid from '@material-ui/core/Grid'
import Typography from '@material-ui/core/Typography'
import MenuItem from '@material-ui/core/MenuItem'
import Select from '@material-ui/core/Select'
import Time from 'util/time'

/**
 * Timezone picker. Automatically detects the timezone from the client's device but also displays
 * a clock using this timezone to make sure it is correct. If not, the user may override it.
 *
 * NOTE: Be careful about Date().getTimezoneOffset(). It does two things differently from standard
 *      1\. Time difference is in minutes
 *      2\. Time difference is from local to UTC, not UTC to local. This means it will be negative of
 *          the expected UTC format
 */
export default class TimeZonePicker extends React.Component {
  state = {
    time: new Date(),
    offset: -(new Date().getTimezoneOffset() / 60),
  }

  componentDidMount() {
    this.props.setOffset(this.state.offset)
  }

  handleChange = (event) => {
    const d = new Date()
    d.setTime(
      d.getTime() +
        d.getTimezoneOffset() * 60 * 1000 +
        event.target.value * 3600 * 1000,
    )
    this.setState({
      time: d,
      offset: event.target.value,
    })
    this.props.setOffset(event.target.value)
  }

  render() {
    const timezones = []
    for (let i = -12; i <= 14; i++) {
      timezones.push(
        <MenuItem key={i} value={i}>
          {i > 0 ? '+' : null}
          {i}
        </MenuItem>,
      )
    }

    return (
      <React.Fragment>
        <Grid container justify="space-between">
          <div>
            <Typography>Current time</Typography>
            <Typography variant="h6" gutterBottom>
              {Time.formatTime(this.state.time)}
            </Typography>
          </div>
          <div>
            <Typography>Set timezone</Typography>
            <Select value={this.state.offset} onChange={this.handleChange}>
              {timezones}
            </Select>
          </div>
        </Grid>
      </React.Fragment>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该组件原本是一个简单的组件，但是由于逻辑是紧密耦合的，所以它负责多种事情。在编写这段代码的时候，react hooks 还没有发布，但是仍然有更高阶的组件和渲染道具。因此，我们将使用其中一种模式来重写，使其更简单，以演示如何使您的组件更简单(而不改变功能):

`SelectTimeZone.js`

```
import React from 'react'

/**
 * Timezone picker. Automatically detects the timezone from the client's device but also displays
 * a clock using this timezone to make sure it is correct. If not, the user may override it.
 *
 * NOTE: Be careful about Date().getTimezoneOffset(). It does two things differently from standard
 *      1\. Time difference is in minutes
 *      2\. Time difference is from local to UTC, not UTC to local. This means it will be negative of
 *          the expected UTC format
 */

class SelectTimeZone extends React.Component {
  state = {
    time: new Date(),
    offset: -(new Date().getTimezoneOffset() / 60),
  }

  componentDidMount() {
    this.props.setOffset(this.state.offset)
  }

  handleChange = (event) => {
    const d = new Date()
    d.setTime(
      d.getTime() +
        d.getTimezoneOffset() * 60 * 1000 +
        event.target.value * 3600 * 1000,
    )
    this.setState({
      time: d,
      offset: event.target.value,
    })
    this.props.setOffset(event.target.value)
  }

  getTimeZones = () => {
    const timezones = []
    for (let i = -12; i <= 14; i++) {
      timezones.push(
        <MenuItem key={i} value={i}>
          {i > 0 ? '+' : null}
          {i}
        </MenuItem>,
      )
    }
    return timezones
  }

  render() {
    return this.props.render({
      ...this.state,
      getTimeZones: this.getTimeZones,
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`TimeZonePicker.js`

```
import React from 'react'
import Grid from '@material-ui/core/Grid'
import Typography from '@material-ui/core/Typography'
import MenuItem from '@material-ui/core/MenuItem'
import Select from '@material-ui/core/Select'
import Time from 'util/time'

const TimeZonePicker = () => (
  <SelectTimeZone
    render={({ time, offset, getTimeZones, handleChange }) => (
      <Grid container justify="space-between">
        <div>
          <Typography>Current time</Typography>
          <Typography variant="h6" gutterBottom>
            {Time.formatTime(time)}
          </Typography>
        </div>
        <div>
          <Typography>Set timezone</Typography>
          <Select value={offset} onChange={handleChange}>
            {getTimeZones()}
          </Select>
        </div>
      </Grid>
    )}
  />
)

export default TimeZonePicker 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有了一个更清晰的方法，并从它的对应表示中提取出了逻辑。单元测试这些组件现在变得更加容易！

## [10](#10-use-raw-usereducer-endraw-if-raw-usestate-endraw-becomes-complex)。如果`useState`变得复杂，使用`useReducer`

当你需要跟踪多个状态时，使用`useState`开始变得难以管理。

这可能是这样的:

```
import React from 'react'
import axios from 'axios'

const useFrogs = () => {
  const [fetching, setFetching] = React.useState(false)
  const [fetched, setFetched] = React.useState(false)
  const [fetchError, setFetchError] = React.useState(null)
  const [timedOut, setTimedOut] = React.useState(false)
  const [frogs, setFrogs] = React.useState(null)
  const [params, setParams] = React.useState({ limit: 50 })
  const timedOutRef = React.useRef()

  function updateParams(newParams) {
    if (newParams != undefined) {
      setParams(newParams)
    } else {
      console.warn(
        'You tried to update state.params but the parameters were null or undefined',
      )
    }
  }

  function formatFrogs(newFrogs) {
    const formattedFrogs = newFrogs.reduce((acc, frog) => {
      const { name, age, size, children } = frog
      if (!(name in acc)) {
        acc[name] = {
          age,
          size,
          children: children.map((child) => ({
            name: child.name,
            age: child.age,
            size: child.size,
          })),
        }
      }
      return acc
    }, {})
    return formattedFrogs
  }

  function addFrog(name, frog) {
    const nextFrogs = {
      ...frogs,
      [name]: frog,
    }
    setFrogs(nextFrogs)
  }

  function removeFrog(name) {
    const nextFrogs = { ...frogs }
    if (name in nextFrogs) delete nextFrogs[name]
    setFrogs(nextFrogs)
  }

  React.useEffect(() => {
    if (frogs === null) {
      if (timedOutRef.current) clearTimeout(timedOutRef.current)

      setFetching(true)

      timedOutRef.current = setTimeout(() => {
        setTimedOut(true)
      }, 20000)

      axios
        .get('https://somefrogsaspi.com/api/v1/frogs_list/', { params })
        .then((response) => {
          if (timedOutRef.current) clearTimeout(timedOutRef.current)
          setFetching(false)
          setFetched(true)
          if (timedOut) setTimedOut(false)
          if (fetchError) setFetchError(null)
          setFrogs(formatFrogs(response.data))
        })
        .catch((error) => {
          if (timedOutRef.current) clearTimeout(timedOutRef.current)
          console.error(error)
          setFetching(false)
          if (timedOut) setTimedOut(false)
          setFetchError(error)
        })
    }
  }, [])

  return {
    fetching,
    fetched,
    fetchError,
    timedOut,
    frogs,
    params,
    addFrog,
    removeFrog,
  }
}

export default useFrogs 
```

Enter fullscreen mode Exit fullscreen mode

如果您将它转换成`useReducer` :
，这将变得更易于管理

```
import React from 'react'
import axios from 'axios'

const initialFetchState = {
  fetching: false
  fetched: false
  fetchError: null
  timedOut: false
}

const initialState = {
  ...initialFetchState,
  frogs: null
  params: { limit: 50 }
}

const reducer = (state, action) => {
  switch (action.type) {
    case 'fetching':
      return { ...state, ...initialFetchState, fetching: true }
    case 'fetched':
      return { ...state, ...initialFetchState, fetched: true, frogs: action.frogs }
    case 'fetch-error':
      return { ...state, ...initialFetchState, fetchError: action.error }
    case 'set-timed-out':
      return { ...state, ...initialFetchState, timedOut: true }
    case 'set-frogs':
      return { ...state, ...initialFetchState, fetched: true, frogs: action.frogs }
    case 'add-frog':
      return { ...state, frogs: { ...state.frogs, [action.name]: action.frog }}
    case 'remove-frog': {
      const nextFrogs = { ...state.frogs }
      if (action.name in nextFrogs) delete nextFrogs[action.name]
      return { ...state, frogs: nextFrogs }
    }
    case 'set-params':
      return { ...state, params: { ...state.params, ...action.params } }
      default:
        return state
  }
}

const useFrogs = () => {
  const [state, dispatch] = React.useReducer(reducer, initialState)
  const timedOutRef = React.useRef()

  function updateParams(params) {
    if (newParams != undefined) {
      dispatch({ type: 'set-params', params })
    } else {
      console.warn(
        'You tried to update state.params but the parameters were null or undefined',
      )
    }
  }

  function formatFrogs(newFrogs) {
    const formattedFrogs = newFrogs.reduce((acc, frog) => {
      const { name, age, size, children } = frog
      if (!(name in acc)) {
        acc[name] = {
          age,
          size,
          children: children.map((child) => ({
            name: child.name,
            age: child.age,
            size: child.size,
          })),
        }
      }
      return acc
    }, {})
    return formattedFrogs
  }

  function addFrog(name, frog) {
    dispatch({ type: 'add-frog', name, frog })
  }

  function removeFrog(name) {
    dispatch({ type: 'remove-frog', name })
  }

  React.useEffect(() => {
    if (frogs === null) {
      if (timedOutRef.current) clearTimeout(timedOutRef.current)

      timedOutRef.current = setTimeout(() => {
        setTimedOut(true)
      }, 20000)

      axios
        .get('https://somefrogsaspi.com/api/v1/frogs_list/', { params })
        .then((response) => {
          if (timedOutRef.current) clearTimeout(timedOutRef.current)
          const frogs = formatFrogs(response.data)
          dispatch({ type: 'set-frogs', frogs })
        })
        .catch((error) => {
          if (timedOutRef.current) clearTimeout(timedOutRef.current)
          console.error(error)
          dispatch({ type: 'fetch-error', error })
        })
    }
  }, [])

  return {
    fetching,
    fetched,
    fetchError,
    timedOut,
    frogs,
    params,
    addFrog,
    removeFrog,
  }
}

export default useFrogs 
```

Enter fullscreen mode Exit fullscreen mode

尽管当你看到它的时候，这可能会比`useState`方法不干净，但是当你使用`useReducer`版本实现定制钩子时，它*更容易管理，因为你不必担心跟踪钩子的多个部分的状态更新，因为你将在`reducer`中的一个地方定义它。*

我们现在还定义了一组“官方”规则，说明如何在`reducer`函数中操纵 state.frogs，并且有一个直接的、更清晰的逻辑分离。换句话说，如果我们继续为此使用`useState`，将不会有一个预定义的实体，不像`useReducer`中所有的逻辑都放在`reducer`中。

在`useState`版本中，我们必须在钩子内部声明函数，以便找出状态的下一部分，除了编写逻辑，而在`useReducer`版本中我们不必这样做，而是将它们移到了`reducer`函数中。我们只需要*调用*的动作类型，这就是它需要担心的一切:)

## 11。在枯燥的地方使用函数声明

一个很好的例子就是`useEffect`清理处理器:

```
React.useEffect(() => {
  setMounted(true)

  return () => {
    setMounted(false)
  }
}, []) 
```

Enter fullscreen mode Exit fullscreen mode

作为知道这是做什么的 react 开发者，这不是问题。但是如果你假设其他人将会阅读你的代码，那么像这样使用函数声明来明确代码是一个好主意，因为我们可以根据自己的优势来命名它们。例如:

```
React.useEffect(() => {
  setMounted(true)

  return function cleanup() {
    setMounted(false)
  }
}, []) 
```

Enter fullscreen mode Exit fullscreen mode

这更清楚地描述了当你返回函数时会发生什么。

## 12。使用更漂亮的

[更漂亮](https://prettier.io/)帮助你和你的团队保持代码格式的一致性。它节省了时间和精力，并减少了在代码评审中讨论风格的需要。它还强制执行干净的代码实践，您可以根据自己的意见配置什么是正确的，什么是不正确的。

## 13。在大片段上使用小片段

*小片段*

```
const App = () => (
  <>
    <FrogsTable />
    <FrogsGallery />
  </>
) 
```

Enter fullscreen mode Exit fullscreen mode

*大片段*

```
const App = () => (
  <React.Fragment>
    <FrogsTable />
    <FrogsGallery />
  </React.Fragment>
) 
```

Enter fullscreen mode Exit fullscreen mode

## 14。把东西整理好

写代码时我喜欢做的一件事是把事情按顺序排好，比如导入文件时(除了`react`导入):

```
import React from 'react'
import { useSelector } from 'react-redux'
import styled from 'styled-components'
import FrogsGallery from './FrogsGallery'
import FrogsTable from './FrogsTable'
import Stations from './Stations'
import * as errorHelpers from '../utils/errorHelpers'
import * as utils from '../utils/' 
```

Enter fullscreen mode Exit fullscreen mode

你们中的一些人可能会想，这甚至没有按照字母顺序排列。这只是这个订购方案的一部分。

我喜欢以一种干净的方式订购我的进口产品，按照优先顺序使用这些指南:

1.  反应导入
2.  库导入(按字母顺序)
3.  项目的绝对导入(按字母顺序)
4.  相对进口(按字母顺序)
5.  `import * as`
6.  `import './<some file>.<some ext>'`

而且我还喜欢用其他方式给变量排序:

```
const character = (function() {
  return {
    cry() {
      //
    },
    eat() {
      //
    },
    hop() {
      //
    },
    jump() {
      //
    },
    punch() {
      //
    },
    run() {
      //
    },
    scratch() {
      //
    },
    scream() {
      //
    },
    sleep() {
      //
    },
    walk() {
      //
    },
    yawn() {
      //
    },
  }
})() 
```

Enter fullscreen mode Exit fullscreen mode

遵循指南有助于构建更整洁的代码库。

## 结论

本帖到此结束！我希望你发现这是有用的，并期待更多！

在[媒体](https://medium.com/@jsmanifest)上找到我