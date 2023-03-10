# Redux 中的高级减速器

> 原文：<https://dev.to/fosteman/advanced-reducers-in-redux-403o>

*没有什么是强制性的，但是，建议一定要。*

我概述了状态管理架构中的最佳实践、常见使用模式和应用扩展。

随着功能的增加，减速器将水平扩展，以线性速率增加动作类型。为了获得额外的可伸缩性，reducer 被分成更小的 reducer，每个 reducer 关注特定的领域(语义)问题。因此，我把我巨大的减速器拆分成:

1.  窗口缩减器(控制智能房屋中的物联网窗框)
2.  恒温器减压器

这里的模式是领域专用减速器- **组合减速器**的责任推断。Redux 给我们`combineReducer()`助手来产生`rootReducer`，稍后在状态中被初始化。这个函数将一个对象作为输入，这个对象将一个状态作为属性名，将一个缩减器作为值

```
const rootReducer = combineReducers({
    windowState: window 
```

# 初始状态

`const store = createStore(reducer, []);`

空列表`[]`是 Redux 存储在初始化时采用的初始状态，之后**存储**将运行每个 Redux 一次。

在减速器中接收到的初始化动作也可以伴随着初始状态，随后在存储初始化中省略初始状态将使其`undefined`。

`const store = createStore(reducer);`

可以用 ES6 默认参数指定初始状态

```
function reducer(state = [], action) {
    ...
} 
```

# 嵌套数据结构

如果初始状态是一个空窗口列表，空列表是一个很好的初始值，但是，考虑这样的属性:`auth_token`、`reason`、`toggle`、`errorStatus`、`currentTemperature`在第一次开发迭代之后立即出现。

定义超出减速器默认参数化状态的初始状态

```
const initialStore = {
    auth_token: null,
    thermostat: {
        currentTemperature: 0.0
    },
    windows: [
    {id: 0, toggle: false},
    {id: 1, toggle: false}
    ],
};
const store = createStore(reducer, initialState); 
```

两个减速器作用于`windows`列表和`thermostat`对象

```
function applyShutWindows(state, action) {
    return state.windows.map(window => action.windows.find(action.id)
        ? Object.assign({}, window, {toggle: false})
        : window);
}
function applySetTemperature(state, action) {
    return Object.assign({}, state.thermostat, { currentTemperature: action.temp });
} 
```

嵌套的数据结构是合理的，并且在 Redux 中通常是好的，但是尽量避免深度嵌套的结构，因为，正如你可能注意到的，复杂性增加了——可读性下降了。

虽然 npm 上有一个整洁的`normalizr`库，解决了 https://www.npmjs.com/package/normalizr**深层嵌套**关注
T4

# 组合减速器

使用 Redux 中的子状态缩放状态。

或许你已经从官方文件[中读到过关于多减速器的内容](%5Bhttps://redux.js.org/basics/reducers#designing-the-state-shape%5D(https://redux.js.org/basics/reducers#designing-the-state-shape))

减速器`switch`可以随着动作类型的增加而水平增长，但这是*线性缩放*。如果将 reducers 按语义(领域相关)分类拆分如下:恒温器、windows 控件——缩放现在是可行的。这种方法保证可以扩展到远远超过线性的 T4 增长。

这里的模式是领域专用减速器- **组合减速器**的责任推断。Redux 给我们`combineReducer`助手来产生`rootReducer`，稍后在状态中被初始化。这个函数将一个对象作为输入，这个对象将一个状态作为属性名，将一个缩减器作为值

### 联合减速器

Redux 库提供了一个助手函数`combineReducers`,该函数将对象作为输入，将**子状态**描述为属性名，将**缩减器**描述为值

```
const rootReducer = combineReducers({
    windowsState: windowsReducer,
    thermostatState: thermostatReducer,
});
const store = createStore(rootReducer); 
```

窗户和恒温器的独立减压器

```
function windowsReducer(state = [
        {id: 0, toggle: true}, 
        {id: 1, toggle: false}
        ], action) {
    switch(action.type) {
        case SHUT_WINDOWS: {
                return applyShutWindows(state, action);
      }
        default: return state;  
}
function thermostatReducer(state  = { currentTemperature: 0.0 }, action) {
    switch(action.type) {
        case SET_TEMP: {
            return applySetTemp(state, action);
    }
        default: return state;
} 
```

`combineReducers`为全局状态对象引入了一个*中间状态层* ( **子状态**)。当使用组合缩减器时，全局状态对象将根据传递给`combineReducers`的对象进行查看

```
{
    windowsState: ...,
    thermostatState: ...,
} 
```

*中间层*的属性键是那些在传递给`combineReducers()`的对象中定义的键。请注意，reducer 中的默认参数不再影响这个全局状态，只有它自己的**子状态、**此外，**子状态**是孤立的，彼此不了解，也不了解全局状态。单个减速器现在可以操作根`state`变量，因为它们在自己的**子状态**中

```
function applyShutWindows(state, action) {
    return state.map(window => action.windows.find(action.id)
        ? Object.assign({}, window, {toggle: false})
        : window);
}
function applySetTemperature(state, action) {
    const thermostatUpdate = Object.assign({}, state, { currentTemperature: action.temp });
    return Object.assign({}, state, thermostatUpdate);
} 
```

这似乎是一个不必要的争论，但是启用*非线性*状态缩放，子状态授权**代码库**可维护性是至关重要的

# 澄清初始状态

`createStore`中的初始状态为减速器的默认参数

→在**一个普通减速器的情况下，**减速器中的初始状态只有在输入的初始状态为`undefined`时才起作用，因为只有这时它才能应用默认状态。

注意:如果初始状态已经在`createStore()`中定义-减速器不会与之竞争。

→另一方面，当使用**组合减速器**时:最好采用**子状态**初始化的默认参数化用法。

注意:`createStore()`中传递的初始状态不一定包括`combineReducers()`引入的所有**子状态**，因此当
子状态为`undefined`时，减速器可以定义自己的默认子状态。否则，使用来自`createStore()`的默认对象层级..

# 嵌套还原器

水平缩放减压器，尽管在组合减压器中有一个额外的垂直水平，归结为:

1.  缩减器可以关注不同的动作类型
2.  一个归约器可以拆分成多个归约器，也可以组合成一个根归约器用于存储初始化

有时，在试验中的复杂领域问题的情况下，引入一个新的垂直层的 reducers 是合理的。以“[物联网窗户](https://en.wikipedia.org/wiki/Smart_glass)框架”为例，想象这些窗户也具有电控着色层。

```
function windowsReducer(state = [

        {id: 0, toggle: true, tint: { degree: 0.0 }}, 

        {id: 1, toggle: false, tint: { degree: 0.0 }}

        ], action) {

    switch(action.type) {

        case SHUT_WINDOWS: {

                return applyShutWindows(state, action);

      }

        case TINT_WINDOWS: {

        //Vertical expansion

        //tintReducer will tap into it's boundaries of substate.

                            return tintReducer(undefined, action);

        }

        default: return state;

}

function tintReducer(state, action) {

    switch(action.type) {

        case TINT_WINDOWS: {

            return applyTintWindows(state, action);

        }

    default : return state;

    }

} 
```

# 
 [T3】
结论](#conclusion) 

这就是所有关于普通 Redux 的用法。它有助于管理可预测的单例状态对象。减少悬挂物背后的通量原理是通用的，因此可以应用于任何 SPA (React，Vue，Angular)。当然，在简单的 React 应用程序中，本地状态可能满足服务的需求，然而，随着跨平台版本的发布，大量的 API 将被提取，分析将被三角化——使用 Redux 将所有的意大利面组织成等级和文件是一个安全的赌注。