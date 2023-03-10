# Redux 中的选择器

> 原文：<https://dev.to/fosteman/selectors-in-redux-394>

Redux 中选择器的概念支持从状态中检索派生属性。**选择器**是一个将状态作为参数并返回子状态或其派生属性的函数。可能他们只返回你的全局状态的一个子状态，或者他们已经预处理了你的状态以返回派生的属性。该函数可以采用可选参数来支持对派生属性的选择。

## 普通选择器

选择器通常遵循相同的语法。选择器的强制参数是它必须从中进行选择的州。可以有起支持作用的可选参数来选择子状态或派生属性。

```
state => derived properties
(state, arguments) => derived properties 
```

选择器是可选的，无论何时你想到 Redux，动作、reducers 和存储本身都是必须的——动作创建者也是可选的——选择器简化了开发者的体验。

```
function mapStateToProps(state) {
    return {
        windows: state.windows,
    }
} 
```

通过**选择器**隐式进行子状态检索

```
function getWindows(state) {
    return state.windows;
}
function maStateToProps(state) {
    return {
        windows: getWindows(state)
    }
} 
```

它类似于动作和减速器的概念。您将使用 action 和 reducer 来间接改变状态，而不是直接在 Redux 存储中操作状态。这同样适用于不直接检索派生属性而是间接从全局状态检索的选择器。

这是一个优势，如果不是想太多的话。选择器是一个纯函数，因此它可以被重用，在应用程序的其他地方可能需要测试派生的属性。

此外，它们可能不像上面的例子那么简单——它们可能利用了一个规格化器库，我之前对其进行了粗略的描述。因此，它可能会将`mapStateToProps()`从过度的复杂性中卸载出来。