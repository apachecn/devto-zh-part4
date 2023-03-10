# 使用 Redux 构建 React 应用时不要做的 12 件事

> 原文：<https://dev.to/jsmanifest/12-things-not-to-do-when-building-react-apps-with-redux-n5i>

在[媒体](https://medium.com/@jsmanifest)上找到我。

当您构建一个 react 应用程序时，在代码架构方面，小项目通常比大项目更灵活一些。虽然用为大型应用程序设计的最佳实践构建一个小应用程序并没有什么错，但是可能没有必要应用所有的重大决策。应用程序越小，偷懒就越“没问题”。

然而，本文中的一些最佳实践建议应用于任何规模的 react 应用程序。

如果您从未有过在生产环境中构建应用程序的经验，那么本文可以帮助您为构建下一个大规模应用程序做准备。可能发生在你身上的最糟糕的事情是在你的工作中构建一个应用程序，并意识到你必须重构大量的代码架构，以便更具可伸缩性和可维护性——特别是如果你错过了*单元测试*！

相信我。我去过那里。 **____** 给了我几个任务要我完成 **____** 。起初，我认为一切都很顺利，很完美。我认为仅仅因为我的 web 应用程序*工作*并且仍然保持*快速*，我在开发和维护我的代码方面做得非常好。我知道如何使用 redux 并使 UI 组件正常交互。减少和行动对我来说是一个简单的概念。感觉*无敌*。

*直到未来爬起来*。

几个月和 15 个以上的特性之后，事情变得无法控制。我使用 redux 的代码不再容易维护。

"*为什么？*“你可能会问。

你不是不可战胜的吗

嗯，我也这么想。这是一颗等待灾难发生的定时炸弹。如果在大型项目中正确使用，Redux 具有惊人的保持事物可维护性的能力。

如果您计划构建可伸缩的 react web 应用程序，请继续阅读，了解哪些事情*不该*做。

## 1。将动作和常数放在一个地方

你可能会看到一些 redux 教程把常量和所有的动作放在一个地方。然而，随着应用程序变得越来越大，这很快就会成为一个麻烦。常量应该像`./src/constants`一样在一个单独的位置，这样就有*一个*的地方可以搜索，而不是在多个位置。

此外，当然*可以*创建一个单独的动作文件来表示*什么*或*如何*它将被使用，封装直接相关的动作。如果你正在构建一个新的街机/角色扮演游戏，引入一个*战士*、*女巫*和*弓箭手*职业，如果你像这样放置你的动作，它会更容易维护:

> src/actions/warrior . js
> src/actions/mosfess . js
> src/actions/archer . js

而不是类似于:

> src/actions/classes.js

如果应用程序真的变得很大，这可能是一个更好的方法:

> src/actions/warrior/skills . js
> src/actions/女巫/skills . js
> src/actions/archer/skills . js

如果我们像演示的那样将它们分开，那么包括使用该方法的其他操作的更大的图片将看起来像这样:

> src/actions/warrior/skills . js
> src/actions/warrior/quests . js
> src/actions/warrior/equipping . js
> src/actions/女巫/skills . js
> src/actions/女巫/quests . js
> src/actions/女巫/equipping . js
> src/actions/射手/skills . js
> src/射手/quests . js
> src/actions/射手/equipping

女巫行为的一个例子是:

> src/动作/女巫/技能

```
import { CAST_FIRE_TORNADO, CAST_LIGHTNING_BOLT } from '../constants/sorceress'

export const castFireTornado = (target) => ({
  type: CAST_FIRE_TORNADO,
  target,
})

export const castLightningBolt = (target) => ({
  type: CAST_LIGHTNING_BOLT,
  target,
}) 
```

Enter fullscreen mode Exit fullscreen mode

> src/动作/女巫/装备

```
import * as consts from '../constants/sorceress'

export const equipStaff = (staff, enhancements) => {...}

export const removeStaff = (staff) => {...}

export const upgradeStaff = (slot, enhancements) => {
  return (dispatch, getState, { api }) => {
    // Grab the slot in our equipment screen to grab the staff reference
    const state = getState()
    const currentEquipment = state.classes.sorceress.equipment.current
    const staff = currentEquipment[slot]
    const isMax = staff.level >= 9
    if (isMax) {
      return
    }
    dispatch({ type: consts.UPGRADING_STAFF, slot })

    api.upgradeEquipment({
      type: 'staff',
      id: currentEquipment.id,
      enhancements,
    })
    .then((newStaff) => {
      dispatch({ type: consts.UPGRADED_STAFF, slot, staff: newStaff })
    })
    .catch((error) => {
      dispatch({ type: consts.UPGRADE_STAFF_FAILED, error })
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们这样做的原因是因为*总会有*新的特性要添加，当你的文件变得越来越庞大时，你必须为它们做好准备！

开始时可能会觉得多余，但随着项目越来越大，这些方法将开始发光。

## 2。将异径管放在一个地方

当我的减压器开始变成这样:

```
const equipmentReducers = (state, action) => {
  switch (action.type) {
    case consts.UPGRADING_STAFF:
      return {
        ...state,
        classes: {
          ...state.classes,
          sorceress: {
            ...state.classes.sorceress,
            equipment: {
              ...state.classes.sorceress.equipment,
              isUpgrading: action.slot,
            },
          },
        },
      }
    case consts.UPGRADED_STAFF:
      return {
        ...state,
        classes: {
          ...state.classes,
          sorceress: {
            ...state.classes.sorceress,
            equipment: {
              ...state.classes.sorceress.equipment,
              isUpgrading: null,
              current: {
                ...state.classes.sorceress.equipment.current,
                [action.slot]: action.staff,
              },
            },
          },
        },
      }
    case consts.UPGRADE_STAFF_FAILED:
      return {
        ...state,
        classes: {
          ...state.classes,
          sorceress: {
            ...state.classes.sorceress,
            equipment: {
              ...state.classes.sorceress.equipment,
              isUpgrading: null,
            },
          },
        },
      }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这显然会很快造成很大的混乱，所以最好保持你的状态结构尽可能的简单和扁平化，或者试着组合所有的 reducers。

一个巧妙的技巧是创建一个生成 reducer 的高阶 reducer，[将每个包装的 reducer 映射到一个从动作类型到处理程序的对象映射](https://redux.js.org/recipes/reducing-boilerplate#generating-reducers)。

## 3。给变量起个糟糕的名字

给变量命名听起来很简单，但实际上这是编写代码时最难做到的事情之一。

这本质上是一种干净的编码实践...这个术语存在的原因是因为它在实践中的应用非常重要。给你的变量起个糟糕的名字是让你的团队成员和你未来的自己受苦的好方法！。

你有没有试过编辑别人的代码，结果却很难理解代码到底想做什么？你有没有运行过别人的代码，而结果却和你预期的不一样？

我敢打赌，代码的作者正在应用*肮脏的代码*实践。

在这种情况下，最糟糕的情况是不得不在大型应用程序中经历这种情况，这种情况通常发生在多个领域。

让我给你讲一个我经历过的真实生活:

我正在从应用程序代码中编辑一个现有的 react 挂钩，这时我收到一个任务，当病人点击他们时，添加并显示关于每个医生的附加信息。当他们选择(点击)一个医生时，医生信息从表格行中提取，这样他们就可以将信息附加到下一个后端请求中。

一切都很好，除了当我搜索代码中的*位置*时，我不必要地花费了更多的时间。

此时，我在脑海中寻找类似于*信息*、*数据发送*、*数据对象*或任何与刚刚收集的数据相关的词语。5-10 分钟后我找到了实现这个流程的部分，它所在的对象被命名为`paymentObject`。当我想到支付对象时，我会想到 CVV、后 4 位数字、邮政编码等。11 个属性中，只有*三个*与支付有关:*收费方式*、*支付档案 id* 和*优惠券*。

更糟糕的是，之后试图融入我的改变实在是太难了。

简而言之，尽量避免像这样命名函数或变量:

```
import React from 'react'

class App extends React.Component {
  state = { data: null }

  // Notify what?
  notify = () => {
    if (this.props.user.loaded) {
      if (this.props.user.profileIsReady) {
        toast.alert(
          'You are not approved. Please come back in 15 minutes or you will be deleted.',
          {
            position: 'bottom-right',
            timeout: 15000,
          },
        )
      }
    }
  }

  render() {
    return this.props.render({
      ...this.state,
      notify: this.notify,
    })
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

## 4。中途改变数据/类型结构

我犯过的最大的错误之一是在已经建立的应用程序流程中改变了某个东西*的数据/类型结构。新的数据结构将是一个*巨大的*性能提升，因为它利用对象查找来抓取内存中的数据，而不是映射到数组。但是已经太晚了。*

请不要这样做，除非你真的*知道*所有将会受到影响的区域。

### 有哪些后果？

如果某个东西从数组变成了对象，应用程序的多个区域都有可能无法正常工作。我犯的最大错误是*认为*我已经计划好了应用程序的每个部分，它们会受到结构化数据更改的影响，但是*总会有一个地方被遗漏*。

## 6。不使用代码片段进行开发

我曾经是 Atom 的粉丝，但我转向了 VScode，因为它比 Atom 快得多——同时仍然支持大量的功能，而没有明显的速度损失。

如果你正在使用 VSCode，我强烈推荐你下载一个叫做[项目片段](https://marketplace.visualstudio.com/items?itemName=rebornix.project-snippets)的扩展。这个扩展允许您为每个工作区声明定制的代码片段，供您在该项目中使用。它的工作方式与默认情况下 vscode 中内置的[用户片段](https://code.visualstudio.com/docs/editor/userdefinedsnippets)功能完全一样，除了您在项目中创建一个`.vscode/snippets/`文件夹，如下所示:

[![vscode project snippets workspace react project](img/b8cbb43500b976e2fee402ad425200d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lSPtlhhp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/12-things-not-to-do-when-building-react-and-redux-apps/vscode_snippet_dir_structure.JPG)

## 7。忽略单元/e2e/集成测试

随着应用程序变得越来越大，在没有任何测试的情况下编辑现有代码变得越来越可怕。您可能最终编辑了位于 src/x/y/z/的文件，并决定将更改推向生产，但是如果更改影响了应用程序的另一部分，而您没有注意到，错误将一直存在，直到真正的用户在浏览您的页面时发现它，因为您没有任何测试来提前提醒您。

## 8。跳过头脑风暴阶段

开发人员经常跳过头脑风暴阶段，因为他们不编码，特别是当他们有一周的时间来开发一个功能时。然而，从经验来看，这是最重要的一步，将会为你和你的团队节省很多时间。

### 何必头脑风暴呢？

应用程序越复杂，开发人员就越需要管理应用程序的某些部分。头脑风暴有助于减少你重构代码的次数，因为你已经计划好了可能出错的地方。很多时候，开发人员几乎没有时间坐下来应用所有整洁的实践来进一步增强应用程序。

这就是为什么头脑风暴很重要。您考虑架构中的所有代码设计和您需要的增强，这样您就可以从一开始就用一种战略性的方法来处理它们。不要养成过度自信，把一切都计划在脑子里的习惯。如果你这样做，你将无法记住所有的事情。一旦你做错了，更多的事情就会像多米诺骨牌效应一样出错。

头脑风暴也会让你的团队变得更容易。如果他们中的一个人陷入了一项任务，他们可以参考他们从一开始就有的头脑风暴，它可能已经存在了。

你在头脑风暴中做的笔记也可以为你和你的团队提供一个*议程*,并在开发应用程序时帮助你轻松地提供当前进度的一致感觉。

## 9。没有预先确定 UI 组件

如果你要开始构建你的应用程序，你应该决定你希望你的应用程序是什么样子的。有几个工具可以帮助您创建自己的模型。

我经常听说的一个模型工具是 [Moqups](https://moqups.com/) 。它的速度很快，不需要任何插件，内置于 HTML5 和 JavaScript 中。

完成这一步非常有助于为您提供将要出现在您创建的页面上的信息和数据。开发你的应用程序将变得轻而易举。

## 10。没有规划数据流

*几乎*应用程序的每个组件都会与某种数据相关联。有些将使用自己的数据源，但大多数将从树中更高的位置提供。对于应用程序中与多个组件共享数据的部分，最好将数据放在树中更高的位置，在那里它将充当一个集中的状态树。这就是 [redux](https://redux.js.org/) 的力量来拯救的地方:)

我建议列出数据将如何在应用程序中流动的列表。这将有助于你为你的应用程序创建一个更牢固的心智模型和书面模型。基于这些值，你的减速器应该很容易建立。

## 11。不使用存取函数

当应用程序变大时，组件的数量也会增加。当组件数量增加时，您使用*选择器* (react-redux ^v7.1)或 *mapStateToProps* 的次数也会增加。如果您发现您的组件或挂钩经常在应用程序的几个部分中选择像*use selector((state)=>state . app . user . profile . demographics . languages . main)*这样的状态片段，那么是时候考虑在一个共享位置创建*访问器函数*了，组件/挂钩可以从该位置导入和使用。这些访问函数可以是过滤器、解析器或任何其他数据转换函数。

以下是一些例子:

> src/访问器

```
export const getMainLanguages = (state) =>
  state.app.user.profile.demographics.languages.main 
```

Enter fullscreen mode Exit fullscreen mode

**连接**版本

> src/components/view 用户语言

```
import React from 'react'
import { connect } from 'react-redux'
import { getMainLanguages } from '../accessors'

const ViewUserLanguages = ({ mainLanguages }) => (
  <div>
    <h1>Good Morning.</h1>
    <small>Here are your main languages:</small>
    <hr />
    {mainLanguages.map((lang) => (
      <div>{lang}</div>
    ))}
  </div>
)

export default connect((state) => ({
  mainLanguages: getMainLanguages(state),
}))(ViewUserLanguages) 
```

Enter fullscreen mode Exit fullscreen mode

**使用选择器**版本

> src/components/view 用户语言

```
import React from 'react'
import { useSelector } from 'react-redux'
import { getMainLanguages } from '../accessors'

const ViewUserLanguages = ({ mainLanguages }) => {
  const mainLanguages = useSelector(getMainLanguages)

  return (
    <div>
      <h1>Good Morning.</h1>
      <small>Here are your main languages:</small>
      <hr />
      {mainLanguages.map((lang) => (
        <div>{lang}</div>
      ))}
    </div>
  )
}

export default ViewUserLanguages 
```

Enter fullscreen mode Exit fullscreen mode

保持这些函数*不可变* -没有*副作用*也是非常重要的。要找出原因，点击[这里](https://redux.js.org/faq/immutable-data#why-is-immutability-required-by-redux)。

## 12。不控制具有析构和传播属性的道具中的流

使用`props.something`相对于`something`有什么好处？

**无破坏性**

```
const Display = (props) => <div>{props.something}</div> 
```

Enter fullscreen mode Exit fullscreen mode

**同析构**

```
const Display = ({ something }) => <div>{something}</div> 
```

Enter fullscreen mode Exit fullscreen mode

使用*析构*，你不仅使你的代码对你自己和他人来说更具可读性，而且你还可以直接决定什么该进什么该出。当其他开发人员将来编辑您的代码时，他们不必扫描您的 render 方法中的每一行代码来找到组件正在使用的所有道具。

您还受益于从一开始就声明默认属性的能力，而不必添加更多的代码行:

```
const Display = ({ something = 'apple' }) => <div>{something}</div> 
```

Enter fullscreen mode Exit fullscreen mode

你可能以前见过这样的东西:

```
const Display = (props) => (
  <Agenda {...props}>
    {'  '}
    // forward other props to Agenda
    <h2>Today is {props.date}</h2>
    <hr />
    <div>
      <h3>Here your list of todos:</h3>
      {props.children}
    </div>
  </Agenda>
) 
```

Enter fullscreen mode Exit fullscreen mode

这不仅有点难以阅读，而且在这个组件中还出现了一个无意的错误。如果`App`也渲染孩子，你有*道具.孩子*被渲染两次。这会导致重复。当与除你之外的开发团队一起工作时，这些错误可能会意外发生，特别是如果他们不够小心的话。

相反，通过析构 props，组件可以直奔主题，减少不必要的 bug

```
const Display = ({ children, date, ...props }) => (
  <Agenda {...props}>
    {'  '}
    // forward other props to Agenda
    <h2>Today is {date}</h2>
    <hr />
    <div>
      <h3>Here your list of todos:</h3>
      {children}
    </div>
  </Agenda>
) 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

就这样，伙计们！我希望这些建议对你有所帮助，如果你有任何问题和/或顾虑，请给我留言。下次见！