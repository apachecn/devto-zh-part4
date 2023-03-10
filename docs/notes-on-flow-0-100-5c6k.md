# 流量注释 0.100

> 原文：<https://dev.to/wgao19/notes-on-flow-0-100-5c6k>

流量 [0.100](https://github.com/facebook/flow/releases/tag/v0.100.0) 输出💯。一如既往地阅读和学习关于 Flow 和 JavaScript 的知识。这一次，我写了一篇关于对象析构、React 类型定义的短文，并提供了一篇解释方差符号的好文章的链接。

Flow 0.100 有非常好的发行说明和示例。所以我不会重复已经说过的话。以下是我在试图了解这些变化时记下的一些要点。

* * *

## 析构函数参数中的对象

> 如果生成的绑定未被使用，则析构模式以前可能包括缺失的属性。即使不使用，这现在也是一个错误。[试流示例](https://flow.org/try/#0C4TwDgpgBA8lC8UDeUwC4oGdgCcCWAdgOZQC+A3AFAD01UAojjgPY4YAqAFhDtHplALNULSDlBQABgEdJUQlFCQpMSQBooAQwHMAZlABiAG2YB3KADcADADoAjFdtXKugK4EAxsDzMCUXQAUSGAa0qQYMACUyKRAA)

```
type O = { p: string }
// Error: There is no property `q` in type `O`, as of Flow v0.100.0
function f({ p, q }: O) {} 
```

我遇到的一个困惑是

> 这是否意味着所有对象都是精确的？

所以我借此机会澄清了一些事情。精确意味着在这里传入的对象*不能*包含额外的字段([链接到文档](https://flow.org/en/docs/types/objects/#toc-exact-object-types))。

另一方面，密封意味着对象可能包含其他字段，但是除非您对它们进行注释，否则您可能无法访问它们。当我们用一些字段编写一个对象类型时，默认情况下它是*密封的*。试图访问一个密封对象的未标注字段将导致错误，不管它是否准确:

```
type Sealed = { foo: string }

const sealed = {
  foo: 'foo',
}
sealed.bar = 'bar' // error
const { foo, bar } = sealed // error 
```

对于需要密封对象的函数，仍然可以传入带有额外属性的对象。

```
function usingSealed(x: Sealed) {
  // does things
}
usingSealed({ foo: 'foo', bar: 'bar' }) // ok 
```

当函数期望*精确的*对象:
时就不是这样了

```
type Exact = {| foo: string |}

function usingExact(x: Exact) {
  // does things
}
usingExact({ foo: 'foo', bar: 'bar' }) // error 
```

我们可以通过析构对象来访问函数参数的字段:

```
// destructuring on function parameter
function goodUsingSealed({ foo }: Sealed) { // ok
  // does things
} 
```

但是由于析构意味着访问对象，我们*不应该*能够访问密封对象的额外属性。这在 0.100 中是固定的:

```
// fixed in 0.100
function badUsingSealed({ foo, bar }: Sealed) { // error after 0.100
  // does things
} 
```

* * *

## 反应元件类型

React 库定义有很多变化。所以这是一个很好的机会，让我们后退一步，看看它们到底是什么。

[这里的](https://github.com/facebook/flow/blob/master/lib/react.js)是到 React 库定义的链接。

我的常用清单:

*   `React$Component<Props, State = void>`
*   `AbstractComponent<-Config, +Instance = mixed>`
*   `React$ComponentType<-Config>`
*   `React$ElementType`
*   `React$Element<+ElementType: React$ElementType>`
*   (新)`React$MixedElement = React$Element<React$ElementType>`
*   (新)`MixedElement = React$MixedElement`
*   `React$Ref<ElementType: React$ElementType>`

在上面的定义中，带尖括号的是多态类型。这意味着你需要为那些多态提供类型参数。

### `React$Component<Props, State = void>`

`React$Component`是 ES6 React 类的基类，建模为多态类，其主要类型参数是 Props 和 State。

### `React$AbstractComponent`

`React$AbstractComponent`是 Flow 使用的类型名，由 React 库声明中的一些不同类型的 React 类和组件使用。

`React.AbstractComponent`直接使用它，它有两个参数，`Config`表示道具类型，`Instance`表示实例。功能组件有`Instance: void`。

### `React$ComponentType`

```
declare type React$ComponentType<-Config> = React$AbstractComponent<
  Config,
  mixed
> 
```

别名为`React$AbstractComponent<Config, mixed>`，用作 React 中组件的类型。它可以是:

*   无状态功能组件。接受 props 作为参数并返回 React 节点的函数。
*   ES6 类组件。使用 ES6 类语法或传统的`React.createClass()`助手定义状态的组件。

### `React$ElementType`

```
declare type React$ElementType = string | React$AbstractComponent<empty, mixed> 
```

React 中元素的类型。反应元素可以是:

*   这些元素是依赖于 React 渲染器实现的内在元素。
*   反应组件。有关其不同变体的更多信息，请参见`ComponentType`。

几更与`ElementType`有关:

A `React$Element`是 React 元素的类型。React 元素通常是使用 JSX 文本创建的，它被 React.createElement 调用(见下文)。

```
declare type React$Element<+ElementType: React$ElementType> = {|
  +type: ElementType,
  +props: React$ElementProps<ElementType>,
  +key: React$Key | null,
  +ref: any,
|} 
```

最后，这里是两个新类型:

```
declare type React$MixedElement = React$Element<React$ElementType>
declare type MixedElement = React$MixedElement 
```

敏锐的读者可能已经注意到，以上或多或少是对 Flow 代码库的注释和代码片段的复制和粘贴。它们确实写得非常好，代码本身的很大一部分是不言自明的。

* * *

## 方差符号

变异符号在库定义中无处不在。如果这组同义词对您有用，请随意将协方差视为只读，将逆变视为只写。

这篇文章 [Flow 的绝密](https://medium.com/@forbeslindesay/covariance-and-contravariance-c3b43d805611)解释的很好。

## 🐶下次见

[流量 0.101](https://github.com/facebook/flow/releases/tag/v0.101.0) 已经出来了，我落后了😭是 [JSConf。亚洲](https://2019.jsconf.asia/)现在在家。下周我会冷静下来，回到我正常的状态:]