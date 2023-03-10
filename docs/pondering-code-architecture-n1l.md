# 代码的干净依赖图和标识

> 原文：<https://dev.to/kelerchian/pondering-code-architecture-n1l>

清理代码的依赖图和身份
下一步:结束声明性和命令性代码之间几十年的战争——复杂性最小化是尾部风险管理的一种形式——将功能范式提升到组件级别

为了满足我新发现的倾注我的思想的痴迷，这次不是以代码的形式，我分享了我在重写一个大的、[劣质的](https://martinfowler.com/articles/is-quality-worth-cost.html)代码库时意识到的四件随机事情中的一件。

这篇文章是关于干净的依赖图和代码的身份。

我在这篇文章中使用了 [typescript](https://www.typescriptlang.org/) 、TSX(现在流行于网络社区的 JSX 的 typescript 版本)，以及一点 React。我希望你能像看其他代码一样看它，甚至像读故事一样读它。

## 清理依赖图

<figure>

[![DGPlain.svg](img/8a077079d1fdc5f761528ed07387a50d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vIokEX62--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Kelerchian/blog-assets/master/dev.to/posts/1-pondering/DGPlain.svg)

<figcaption>A dependency graph</figcaption>

</figure>

我正在做的工作在自然界是非常罕见的。我们需要解决的挑战之一是编写一个方便的可分支代码，它易于 git 合并并且是可定制的——这是一个令人生畏的组合。

下面显示的代码是类似于真实代码的示例代码。由于真实的是专有的，我不能展示它(不是我想)。

干净的依赖图产生灵活的代码。依赖图是表示代码中模块之间依赖关系的有向图。例如，导入一类可共享状态`B`的渲染器模块`A`将是`A->B`。

一个清晰的依赖图的黄金法则是，如果粗略地水平和垂直划分，它满足以下前提:

1.  同一行中的模块有相似的方法
2.  同一列中的模块具有相同的域
3.  模块从最不稳定到最不稳定垂直排序，箭头不应该指向下。

### 同一行中的模块应该有相似的方法

<figure>

[![DGRow.svg](img/15eabd0661ed1b8d33b0bf3686c50e07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WkHalOLm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Kelerchian/blog-assets/master/dev.to/posts/1-pondering/DGRow.svg)

<figcaption>Modules in the same row has similar role for different domain.</figcaption>

</figure>

以一组 API 调用模块为例:

```
// UserAPI.ts

export function fetchUserById({ userId }:{userId:string}){
  return decodeOrError(
    () => networkManager.withCredentials().get(`http://domain/some/path?userId=${userId}`),
    UserDecoder,
    UserDecoderError
  )
}
// DepotsAPI.ts

export function fetchBuildByBuildIds({ buildIds }:{buildIds: string[]}){
  return decodeOrError(
    () => networkManager
      .withCredentials()
      .get(`http://domain/api/builds/?buildIds=${buildIds.join(",")}`),
    BuildDecoder,
    BuildDecoderError
  )
}

// TagsAPI.ts
export function addTag({ tags },{tags: string[]}){
  return decodeOrError(
    () => networkManager
      .withCredentials()
      .post(`http://domain/api/builds/?buildIds=${buildIds.join(",")}`),
    Decoder.unknown,
    CommonDecodeError
  )
} 
```

涉及三个不同领域的三个模块完成得很相似，并为这些模块形成了一个*角色*，在本例中是调用 API 端点、解码、保护和转换类型。从开发的角度来看，在具有相同角色的模块和它们的测试之间复制和粘贴代码很容易，因此减少了认知负荷。它既适用于开发团队，也适用于单个开发人员。

### 同一列中的模块具有相同的域

这是非常直接和直观的。

[![DGColumn.svg](img/60b0292365598ac2316779daf9eae20b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rFLXmKsi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Kelerchian/blog-assets/master/dev.to/posts/1-pondering/DGColumn.svg)

这个概念类似于微前端，其中关注点分离是关键。它创建了业务逻辑的清晰定义，从模型定义一直到表示层。

```
// BuildModel

export const BuildDecoder = createDecoder({ 
...
})
export type Build = Type<typeof Build>

// BuildAPICall

import { BuildDecoder } from "src/module/build/model"

export function fetchBuilds(){
  return decodeOrError(
    () => networkManager
      .withCredentials()
      .get(`http://domain/api/builds/`),
    Decoder.array(BuildDecoder),
    BuildDecoderError
  )
}

export function addBuild({ build }: Build){
  return decodeorError(
    () => networkManager
      .withCredentials()
      .post('http://domain/api/builds/', build),
    BuildDecoder,
    BuildDecoderError
  )
}

// BuildsViewState

import { StateManager } from "src/utils/state-manager"
import { Build } from "src/module/build/model"
import { fetchBuilds, addBuild } from "src/module/build/api"

type State = {isFetching: boolean, builds: Build[] | null, error: Error | null}

export class BuildsViewState extends StateManager<State>{
  state: State = {
    isFetching: boolean,
    builds: null,
    error: null
  }

  // override empty init method
  init(){
    try{
      if(this.state.isFetching) return
      this.setState({ isFetching: true })
      this.setState({ builds: await fetchBuilds(result => {
          if(result.error) throw result.error
          return result.response.data
        }) 
      })
    } catch(error) {
      this.setState({ error })
    } finally {
      this.setState({ isFetching: false })
    }
  }

  // inherits empty deinit method
}

// BuildViewPage

import { React } from "react"
import { Loading, CommonError } from "src/common/components/loading"
import { BuildViewState } from "src/utils/build/page/view"

export class BuildViewPage extends React.Component {
  sharedState: new BuildViewState();

  componentDidMount(){
    this.sharedState.init()
    this.sharedState.subscribe(() => this.setState({}))
  }

  componentWillUnmount(){
    this.sharedState.deinit()
  }

  render(){
    const { isFetching, builds, error } = this.sharedState.state
    return (
      <section>
        {isFetching && (
          <Loading>
            Loading your Builds. Please Wait.
          </Loading>
        )}
        {error && (
          <CommonError error={error} />
        )}
        {builds && builds.map(build => (
          <pre key={build.id}>
            {JSON,stringify(build, null, 2)}
          </pre>
        )}
      </section>
    )
  }
} 
```

行和列之间的交集创建了模块的**标识**，假设该应用程序是一个管理软件版本构建的 web 应用程序，它有一个*build viewpage*-*build viewpage*可以定义为呈现构建(域)视图(角色)的模块。

关键在下一条规则。

### 模块从易失性最小到易失性最大垂直排序，箭头不得向下。

如今，导入其他模块就像按下`alt+enter`一样简单，甚至一些 IDE 也支持不按任何键的特性。姑且称之为进口便利。导入的便利性使我们不再考虑为什么以及如何导入模块，或者用我的话来说，保持依赖图整洁的艺术。

这个规则吸取了导入的本质，即不太易变的模块不应该导入更易变的模块。这里的易变指的是容易发生变化。

<figure>

[![DGViolationOfRule3.svg](img/fae67d863caf093584b78983b1fefe04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V3TO03Y0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/Kelerchian/blog-assets/master/dev.to/posts/1-pondering/DGViolation.svg)

<figcaption>Violation of Rule 3\. A module imports from a more volatile module</figcaption>

</figure>

有时不考虑后果就导入任何东西是很诱人的，但编译器从来不会禁止。但是请注意，当一个依赖模块发生变化时，该依赖模块也可能发生变化。依赖项可以显式更改(需要更改代码)，也可以隐式更改(更改是继承的)。

假设下面的事实是真的:

```
let B->A or A<-B means B depends on A

AND:
- A<-B
- B<-C
- B<-D
- B<-E 
```

当 A 发生变化时，B、C、D 和 E 也可能发生变化。
模块 A 的变化导致至少 0 个变化，最多 4 个变化。
这意味着对一个代码更改至少 0 次额外的努力，对代码更改至多 4 次额外的努力。和测试这些变化的额外努力。

所以最少 2 个工作单位，最多 10 个。

我可能听起来很懒，因为只计算了很少的变化，直到这个规则被应用到一个更大的规模上，一个具有复杂依赖图的大项目上。

但这只是我需要重写的一个小原因。原因是旧代码没有清晰的图形可视性。

### “规则 3 没有意义”

乍一看，规则 3 有时没有意义。

例如，下面是一个存储有关构建信息的文件。这个文件被导入到任何地方，包括助手模块、工厂模块、表示模块、共享状态模块等等。

```
// build/model.ts

type Build = {
  id: string,
  version: string,
  patches: Patch[]
}

type Patch = {
  srcUrl: string,
  position: number
}

type BuildResponse = {
  data: null,
  errorCode: number
} | { 
  data: Build[], 
  errorCode: null 
}

function deduceBuildError(errorCode){
  switch(errorCode){
    case 1000: return "Build not found"
    case 1001: return "Build corrupt"
    case 1002: return "Build being switched"
    default: return "Unknown Error"
  }
} 
```

令人费解的是,`deduceBuildError`被表示模块用来显示错误信息，而且由于 UX 的原因，有很多对`deduceBuildError`函数进行修改的请求。虽然这种改变应该只隐含地影响表示模块，但是它有隐含地改变其他模块的风险。

这是由于文件`build/model.ts`的**身份**被破坏。作为一个模型，它不应该有基于`errorCode`推断错误信息的`deduceBuildError`。推断构建错误消息根本不是它的职责。

正确的做法是将其移出模型。它可以在表示模块内部，因为它是唯一应该受其变化影响的东西。表示模块的**身份**没有被破坏，因为添加`deduceBuildError`后表示模块的角色没有改变。它也可以放入另一个文件中，该文件位于表示模块的正上方。

## 最脏的一种依赖图

最肮脏、最糟糕的依赖图是没有依赖图，并且没有防范无意义的依赖，循环依赖。

没有依赖关系图意味着:
*不知道在哪里添加东西*
*不知道什么时候改变东西*
*不知道什么时候应用 DRY 或者复制粘贴*
*不知道什么时候从模块中取出东西*
*没有快速开发*
*没有生产力*

## 在系统架构层面清理依赖图

依赖发生在系统架构级别之间。它变脏的可能性比代码小得多，因为它比代码的变化要慢。然而，一个失误会导致组件间交互经常发生的问题，例如，网络调用的瓶颈。

组件的身份是您必须关注的。把一个特性放在不合适的地方，错误命名一个端点，隐含的行为，会使系统倾向于一个更脏的依赖图。

稍后可能会有更多的介绍。