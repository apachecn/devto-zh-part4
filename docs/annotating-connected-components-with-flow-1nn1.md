# 用流标注连接的组件

> 原文：<https://dev.to/wgao19/annotating-connected-components-with-flow-1nn1>

在流 0.85 之后，流开始[在每个文件导入-导出周期内对更高阶组件的隐式调用上要求所需的注释](https://medium.com/flow-type/asking-for-required-annotations-64d4f9c1edf8)。这有助于流程在遍历类型结构并进行类型推断之前，从文件依赖关系*中合并类型信息。*

这有助于 Flow 更好地覆盖高阶组件。但是它也要求我们在文件导出时显式地注释连接的组件。导出对`connect`的隐式调用将引发错误:

```
Missing type annotation for OP. OP is a type parameter declared in function type [1] and was implicitly
instantiated at call of connect [2]. 
```

一般来说，在 0.85 之后使用 connect 使 Flow 满意是一个两阶段的修复。首先，您需要在文件导出时显式注释每个连接的组件。这将清除所有“隐式实例化”的错误。然后，如果您的代码库包含组件定义和使用之间不匹配的类型，Flow 将在您修复隐式实例化错误后报告这些错误。

## 修复调用`connect`时的“隐式实例化”错误

> **注意:**我们需要来自流量 v0.89+的`React.AbstractComponent`

### 在函数返回时进行注释

注释连接组件最简单的方法是在函数调用返回时进行注释。为此，我们需要了解组件中的道具类型:

*   `OwnProps`:可能包含或等于你需要的作为`mapStateToProps`的第二个参数。如果有不被`mapStateToProps`使用的道具，即“穿越”的道具，在这里也包括在`OwnProps`中
*   `Props` : `OwnProps`加上`mapStateToProps`和`mapDispatchToProps`传进来的道具

> **注意:**不精确的物体传播不太好`$Diff`。强烈建议您始终对连接的组件使用精确对象。

```
type OwnProps = {|
  passthrough: string,
  forMapStateToProps: string
|};

type Props = {|
  ...OwnProps,
  fromMapStateToProps: string,
  dispatch1: () => void
|}; 
```

弄清楚了`OwnProps`和`Props`之后，我们现在就可以注释连接的组件了。

在*组件定义*中，用`Props`标注道具。该组件将可以从`connect` :
访问所有注入的道具

```
import * as React from "react";

const MyComponent = (props: Props) => (
  <div onClick={props.dispatch1}>
    {props.passthrough}
    {props.fromMapStateToProps}
  </div>
); 
```

当我们导出时，这也是我们通常调用`connect`的时候，用*只是* `OwnProps` :
来注释导出的组件

```
import * as React from "react";

// const MyComponent = ...

export default (connect(
  mapStateToProps,
  mapDispatchToProps
)(MyComponent): React.AbstractComponent<OwnProps>); 
```

### 通过提供显式类型参数进行标注

我们也可以在 React Redux 的[最新流类型库定义的帮助下，通过在调用`connect`时提供显式类型参数来注释连接的组件。注意这也需要流量 v0.89+。](https://github.com/flow-typed/flow-typed/blob/master/definitions/npm/react-redux_v5.x.x/flow_v0.89.x-/react-redux_v5.x.x.js)

流类型库定义声明`connect`如下:

```
declare export function connect<-P, -OP, -SP, -DP, -S, -D>(
  mapStateToProps?: null | void,
  mapDispatchToProps?: null | void,
  mergeProps?: null | void,
  options?: ?Options<S, OP, {||}, MergeOP<OP, D>>
): Connector<P, OP, MergeOP<OP, D>>; 
```

libdef 还包含一个缩写词的[词汇表，它将签名解密为:](https://github.com/flow-typed/flow-typed/blob/master/definitions/npm/react-redux_v5.x.x/flow_v0.89.x-/react-redux_v5.x.x.js#L14) 

```
connect<Props, OwnProps, StateProps, DispatchProps, State, Dispatch>(…) 
```

对于连接组件的最常见方式，我们不需要所有的参数。正常情况下，我们只需要在`connect`的召唤下`OwnProps`和`Props`，在`mapStateToProps`的定义下`State`。

我们可以用`_` ( [这是什么？](https://github.com/facebook/flow/commit/ec70da4510d3a092fa933081c083bd0e513d0518))作为占位符放在未使用的类型参数位置。一个普通的`connect`呼叫可能是这样的:

```
connect<Props, OwnProps, _, _, _, _>(…) 
```

我们包括用 Flow 注释`connect`的三个主要用例的例子:

*   用`mapStateToProps`连接无状态组件
*   用动作创建者的`mapDispatchToProps`连接组件
*   用动作创建者的`mapStateToProps`和`mapDispatchToProps`连接组件

#### 连接无状态组件与`mapStateToProps`

```
type OwnProps = {|
  passthrough: number,
  forMapStateToProps: string,
|};
type Props = {|
  ...OwnProps,
  fromStateToProps: string
|};
const Com = (props: Props) => <div>{props.passthrough} {props.fromStateToProps}</div>

type State = {a: number};
const mapStateToProps = (state: State, props: OwnProps) => {
  return {
    fromStateToProps: 'str' + state.a
  }
};

const Connected = connect<Props, OwnProps, _, _, _, _>(mapStateToProps)(Com); 
```

#### 连接组件与动作创建者的`mapDispatchToProps`

```
type OwnProps = {|
  passthrough: number,
|};
type Props = {|
  ...OwnProps,
  dispatch1: (num: number) => void,
  dispatch2: () => void
|};
class Com extends React.Component<Props> {
  render() {
    return <div>{this.props.passthrough}</div>;
  }
}

const mapDispatchToProps = {
  dispatch1: (num: number) => {},
  dispatch2: () => {}
};
const Connected = connect<Props, OwnProps, _, _, _, _>(null, mapDispatchToProps)(Com);
e.push(Connected);
<Connected passthrough={123} />; 
```

#### 连接组件与动作创建者的`mapStateToProps`和`mapDispatchToProps`

```
type OwnProps = {|
  passthrough: number,
  forMapStateToProps: string
|};
type Props = {|
  ...OwnProps,
  dispatch1: () => void,
  dispatch2: () => void,
  fromMapStateToProps: number
|};
class Com extends React.Component<Props> {
  render() {
    return <div>{this.props.passthrough}</div>;
  }
}
type State = {a: number}
type MapStateToPropsProps = {forMapStateToProps: string}
const mapStateToProps = (state: State, props: MapStateToPropsProps) => {
  return {
    fromMapStateToProps: state.a
  }
}
const mapDispatchToProps = {
  dispatch1: () => {},
  dispatch2: () => {}
};
const Connected = connect<Props, OwnProps, _, _, _, _>(mapStateToProps, mapDispatchToProps)(Com); 
```

### 用连接标注嵌套的高阶组件

如果您的组件被嵌套的高阶组件所包裹，那么通过提供显式类型参数来进行注释可能会更加困难，因为这样做可能需要您繁琐地去掉每一层的道具。在函数 return:
处进行注释更加容易

```
type OwnProps = {|
  passthrough: number,
  forMapStateToProps: string,
|}
type Props = {|
  ...OwnProps,
  injectedA: string,
  injectedB: string,
  fromMapStateToProps: string,
  dispatch1: (number) => void,
  dispatch2: () => void,
|}

const Component = (props: Props) => { // annotate the component with all props including injected props
  /** ... */
}

const mapStateToProps = (state: State, ownProps: OwnProps) => {
  return { fromMapStateToProps: 'str' + ownProps.forMapStateToProps },
}
const mapDispatchToProps = {
  dispatch1: number => {},
  dispatch2: () => {},
}

export default (compose(
  connect(mapStateToProps, mapDispatchToProps),
  withA,
  withB,
)(Component): React.AbstractComponent<OwnProps>)  // export the connected component without injected props 
```

## 这个版本的好处

修复隐式实例化错误后，如果您的代码包含连接组件之间不匹配的类型，错误总数可能会上升*到*。这是 Flow 提高覆盖率的结果。如果您使用控制台输出显示流错误，则在清除其他错误之前，您可能看不到这些错误。这些额外的错误被组合在一起，所有这些都与 React Redux 的库定义联系在一起，并且有友好的错误消息，它会将您定位到错误的代码行。

[![](img/ab246edb32bb994c8030b812c14af29e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xlR5Ab9D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/mt79yaC.png)

## 参考文献

**文章**

*   [询问所需注释](https://medium.com/flow-type/asking-for-required-annotations-64d4f9c1edf8)

**升级导轨**

*   [维尔和乔丹·布朗指南:*添加类型参数连接*T3】](https://gist.github.com/jbrown215/f425203ef30fdc8a28c213b90ba7a794)
*   [快速注释修正`connect`0.89](https://dev.to/wgao19/quick-note-fixing-connect-flowtype-annotation-after-089-joi)后的流类型注释

**会谈**

*   [流量快乐](https://engineers.sg/video/flow-be-happy-reactjs-singapore--3419)提升流量过 0.85，[幻灯片](https://flow-be-happy.netlify.com/)

**其他**

*   [流动笔记](https://github.com/wgao19/flow-notes)一个回购，我把我的笔记集中在流动上
*   [React Redux`connect`](https://github.com/flow-typed/flow-typed/blob/master/definitions/npm/react-redux_v5.x.x/flow_v0.89.x-/test_connect.js)的流型测试
*   [流式/# 2946:0.85 后的讨论](https://github.com/flow-typed/flow-typed/issues/2946)
*   增加对流量 0.89+的支持: [#3012](https://github.com/flow-typed/flow-typed/pull/3035) ， [#3035](https://github.com/flow-typed/flow-typed/pull/3035)
*   [什么是`_`？](https://github.com/facebook/flow/commit/ec70da4510d3a092fa933081c083bd0e513d0518)