# 轻松地将 Ramda 集成到您的 React 工作流中

> 原文：<https://dev.to/hint/easily-integrate-ramda-into-your-react-workflow-3kh4>

*最初发布于 [Hint 的博客](https://hint.io/blog/react-and-ramda)。*

在这里，我们经常使用 React 来编写我们的用户界面。我们喜欢它的声明式 API，这种心理模型使得与团队的交流和协作更加容易，尤其是最近增加的钩子。然而，React 并没有提供完整的工具包。它缺少一些现成的东西:数据获取、处理异步函数、以实用的方式应用样式等等。

在我学习 React 时，React 特性集中最大的漏洞实际上是 JavaScript 本身的问题。与 Ruby 或 Elixir 等其他大量使用工具包的语言相比，JavaScript 并没有给你太多的工作空间。我开始编写自己的助手库，直到一个朋友告诉我关于 Ramda 的事情。直接从[他们的主页](https://ramdajs.com/):

> 面向 JavaScript 程序员的实用函数库。

嘿！我喜欢功能性的东西，库，JavaScript...而我是程序员！这是一见钟情(不，我并不为此感到羞耻)。

Ramda 的第一个障碍是函数式编程。如果你从未涉足功能领域，请阅读 Randy Coulman 的“在 Ramda 中思考”系列，非常精彩。

Ramda 的第二个障碍(作为 React 开发人员)是知道如何有效地使用 React*。我仍然在学习和尝试这两个库如何一起工作，我想分享一些我在过去几年中坚持的模式。让我们开始吧！*

 *## 用`isNil`和`isEmpty`让你的代码读起来更好

有时候，React 代码不是最容易阅读的。我认为后挂钩变得更糟了。越来越多的逻辑被添加到组件的主体中，如果没有自动帮助组织代码的生命周期方法，我会接受任何帮助来清理。

拉姆达的`isNil`和`isEmpty`是让你的组件的身体炫🕺.的一个很好的开始比如:

```
 const Entry = ({ client }) => (
    <Query query={currentUserQuery}>
      {({ loading, data }) => {
        if (!loading && !data.user.posts)
          return <NoPosts />

        if (data.user) {
          setErrorTrackingContext(data.user)
          getPostMetaData(data.user, client)
        }

        return (
          // code that renders things here
        )
      }}
    </Query>
  ) 
```

*代码示例说明:本文中的所有代码都是基于我编写的真实代码。有一些参考[阿波罗的反应库](https://github.com/apollographql/react-apollo)，提示爱。为了简洁起见，大多数导入都被删除了。这里没有博客，没有填充，没有伪代码。接近生产就绪。*

注意第一个`if`:如果我们完成了加载并且`data.user.posts`为 falsy，我们将提前返回一个组件。第二个`if`:如果我们有一个用户，让我们为我们正在使用的任何错误跟踪设置上下文(在提示我们爱[蜜獾](https://www.honeybadger.io/))，然后获取一些帖子元数据。让我们不要担心这些函数的任何实现，而是专注于我们的逻辑。乍一看，事情没那么糟，但“没那么糟”不是标准。优秀就是！让我们再来一遍，但是用 Ramda:

```
 import { isNil, isEmpty } from 'ramda'

  const Entry = ({ client }) => (
    <Query query={currentUserQuery}>
      {({ loading, data }) => {
        if (isNil(loading) && isEmpty(data.user.posts))
          return <NoPosts />

        if (data.user) {
          setErrorTrackingContext(data.user)
          getPostMetaData(data.user, client)
        }

        return (
          // code that renders things here
        )
      }}
    </Query>
  ) 
```

注意顶部的`import`和对第一个`if`的更新。如果`loading`为`null`或`undefined`，则`isNil`将返回`true`。这个函数非常有用，因为它不只是检查值是否是`falsy`，这本质上是它之前所做的(`!loading`)。从一只讨厌的虫子那里救回来的后腿！

在同一行上，如果传入的值是`''`、`[]`或`{}`，`isEmpty`将返回`true`。当使用 GraphQL 时，如果你请求一个东西的集合，但是没有，通常你会得到一个空数组。我们之前的逻辑检查，`!data.user.posts`可能也引入了一个意想不到的 bug！后躯再次得救。

### 亲提示

第一分就已经是职业小费了？今天是个好日子。

Ramda 是由许多具有单一特定用途的微小函数构建而成的。组装在一起，你可以创造一些有趣的东西！让我们创建一个与`isNil` :
相反的助手

```
 import { isNil, isEmpty, complement } from 'ramda'

  const isPresent = complement(isNil)

  const Entry = ({ client }) => (
    <Query query={currentUserQuery}>
      {({ loading, data }) => {
        if (isNil(loading) && isEmpty(data.user.posts))
          return <NoPosts />

        if (isPresent(data.user)) {
          setErrorTrackingContext(data.user)
          getPostMetaData(data.user, client)
        }

        return (
          // code that renders things here
        )
      }}
    </Query>
  ) 
```

`complement`将函数作为第一个参数，将值作为第二个参数。如果调用时返回一个 falsy 值，输出将是`true`(反之亦然)。使用`complement`让我们的第二个`if`更好一点。

你可能会说，“这真的很简单。为什么拉姆达不来一个那样的帮手？”将 Ramda 功能想象成独立的乐高积木。它们本身做不了太多，但是把它们放在一起，你可以创造出非常有用的东西。如果你想要一个更*【全面的公用事业设置】*，看看 [Ramda 附属品](https://char0n.github.io/ramda-adjunct)。

## 单独对物体进行操作很危险！取这些函数:`prop`和`path`

*+1 互联网积分如果你得到标题笑话*

作为开发人员，没有什么比深度访问一个对象更可怕的了。如果这没有让你有点畏缩:

```
if (foo.bar.baz.theLastPropertyIPromise.justKiddingOneMore) doTheThing() 
```

那我们需要谈一谈。如果这是您建议的解决方案:

```
if (
  foo &&
  foo.bar &&
  foo.bar.baz &&
  foo.bar.baz.theLastPropertyIPromise &&
  foo.bar.baz.theLastPropertyIPromise.justKiddingOneMore
)
  doTheThing() 
```

那么我们真的需要谈谈。

玩笑归玩笑，我们都经历过。很容易完全掩盖复杂的检查，或者编写占用太多字节并且难以阅读的条件。Ramda 给了我们`prop`和`path`来安全地访问对象。让我们看看他们是如何工作的:

```
import { prop, path, pipe } from 'ramda'

const obj = { foo: 'bar', baz: { a: 1, b: 2 } }

const getFoo = prop('foo')
getFoo(obj) // => 'bar'

const getBazA = path(['baz', 'a'])
getBazA(obj) // => 1 
```

太好了！“但那是安全的吗？你要的房产都有了！”很高兴你问了:

```
import { path, pipe } from 'ramda'

const obj = { foo: 'bar', baz: { a: 1, b: 2 } }

const getSomethingThatDoesNotExist = path([
  'foo',
  'bar',
  'baz',
  'theLastPropertyIPromise',
  'justKiddingOneMore'
])
getSomethingThatDoesNotExist(obj) // => undefined 
```

谢谢拉姆达！后腿又一次得救了。注意`undefined`，返回一个假值。非常有用的存在检查！让我们将新的知识应用到我们的`<Entry />`组件:

```
 import { isNil, isEmpty, complement, prop } from 'ramda'

  const getUser = prop('user')
  const userIsPresent = pipe(
    getUser,
    complement(isNil)
  )

  const Entry = ({ client }) => (
    <Query query={currentUserQuery}>
      {({ loading, data }) => {
        if (isNil(loading) && isEmpty(data.user.posts))
          return <NoPosts />

        if (userIsPresent(data)) {
          const user = getUser(data)
          setErrorTrackingContext(user)
          getPostMetaData(user, client)
        }

        return (
          // code that renders things here
        )
      }}
    </Query>
  ) 
```

看起来肯定更好。进一步的重构可以在我们的第二个`if`条件中完成。有趣的是，看看您是否能弄清楚如何使用 Ramda 将那个`if`合并到一个函数中。答案在本帖末尾！

## 用`evolve`准备你的道具

将组件道具转换成有用的东西是常见的做法。让我们看一下这个例子，在这个例子中，我们连接了一个名字和一个姓氏，并格式化了一个日期:

```
const NameAndDateDisplay = ({ date, firstName, lastName }) => (
  <>
    <div>
      Hello {firstName.toUpperCase()} {lastName.toUpperCase()}!
    </div>
    <div>It is {dayjs(date).format('M/D/YYYY dddd')}</div>
  </>
) 
```

很简单，但是这段代码有些可疑。你能发现它吗？问题是这有点太简单了。当处理真实数据、真实 API 和人类编写的真实代码时，事情并不总是简单明了的。有时，您正在处理一个使用第三方 API 的项目，并且您无法完全控制从服务器获得什么。

在这些情况下，我们倾向于把所有的逻辑都放在我们的组件体中，就像这样:

```
const NameAndDateDisplay = ({ date, firstName, lastName }) => {
  const formattedDate = formatDate(date)
  const formattedFirstName = formatFirstName(firstName)
  const formattedLastName = formatLastName(lastName)

  return (
    <>
      <div>
        Hello {firstName} {lastName}!
      </div>
      <div>It is {formattedDate}</div>
    </>
  )
} 
```

这带来了一些问题。一些非常重要的逻辑被绑定到我们组件的主体上，使得测试变得困难。测试这些格式化程序的唯一方法是呈现组件。此外，*真的是*膨胀了我们组件的主体。在 Rails 中，你会看到“胖模特，瘦控制器”；React 中的一个类似术语是“胖助手，瘦组件体”。

幸运的是，Ramda 的`evolve`真的可以帮我们解决问题。`evolve`取两个自变量；第一个参数是值为函数的对象，第二个参数是要操作的对象。

```
import { evolve, toUpper } from 'ramda'

evolve({ foo: toUpper }, { foo: 'weeee' })
// => { foo: 'WEEEE' } 
```

相当整洁！关于`evolve`，需要注意两件重要的事情:它是递归的，它不会对第一个参数中没有指定的值进行操作。

```
import { evolve, toUpper, add } from 'ramda'

const format = evolve({
  foo: toUpper,
  numbers: { a: add(2) },
  dontTouchMe: 'foobar'
})
format({ foo: 'weeee', numbers: { a: 3 } })
// => { foo: 'WEEEE', numbers: { a: 5 }, dontTouchMe: 'foobar' } 
```

有了这个新发现的知识，让我们重构我们的组件:

```
import { evolve, pipe } from 'ramda'

const prepProps = evolve({
  date: formatDate,
  firstName: formatFirstName,
  lastName: formatLastName
})

const NameAndDateDisplay = ({ date, firstName, lastName }) => (
  <>
    <div>
      Hello {firstName} {lastName}!
    </div>
    <div>It is {date}</div>
  </>
)

export default pipe(
  prepProps,
  NameAndDateDisplay
) 
```

恶心！我们已经成功地将格式化代码从渲染代码中分离出来。

## 包装完毕

React 和 Ramda 都是非常强大的工具。了解它们如何一起工作和交互可以简化和加快开发时间。

展望未来，当您发现自己从一个项目向另一个项目复制和粘贴助手库时，请记住 Ramda。有可能存在一个 Ramda 函数可以完成相同的任务，甚至更多！还有很多很多的 Ramda 函数没有在本文中介绍。查看 [Ramda 的文档](https://ramdajs.com/docs/)了解更多信息。

## 重构答案

我们的第二个`if`条件，完全重构:

```
// setErrorTrackingContextAndGetPostMetaData.js
import { prop, pipe, complement, when, converge, curry, __ } from 'ramda'

const getUser = prop('user')
const userIsPresent = pipe(
  getUser,
  complement(isNil)
)
const curriedGetPostMetaData = curry(getPostMetaData)

const setErrorTrackingContextAndGetPostMetaData = client =>
  when(
    userIsPresent,
    converge(getUser, [
      setErrorTrackingContext,
      curriedGetPostMetaData(__, client)
    ])
  )

export default setErrorTrackingContextAndGetPostMetaData

// Entry.js
// in the body of <Entry />

// ...
setErrorTrackingContextAndGetPostMetaData(client)(data)
// ... 
```*