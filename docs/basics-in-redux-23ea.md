# Redux 中的基础知识

> 原文：<https://dev.to/fosteman/basics-in-redux-23ea>

Redux 采用了 Flux 架构中的一些约束: **Actions** 封装信息，供 **reducer** 确定性地更新状态。事实上，它只有一家**店**。单个通量分配器被多个小的**减速器**所取代，这些减速器从动作中提取信息，并将其“还原”到新的状态，然后保存在**存储器**中。当**商店**中的状态改变时，根据**订阅的视图**接收道具。

概念:

```
 View -> Action -> Reducer -> Store -> View 
```

因此，“Redux”这个名称结合了“Redux”和“Flux”两个词。不同之处在于状态不再存在于视图中，它只与视图的相连接，这意味着两端(视图和存储)现在都负责动作-更新-通知单向轮。

值得注意的是，Redux 可以独立运行！

## 动作

Redux 爱物件。而**动作**确实是对象。它有一个**动作类型**和**有效载荷**。

```
 {
        type: 'set_thermostate_temp',
        payload: {
                            id: 'basement', 
                            temprature: 24.0,
                            authorizedBy: 'Timofei Shchepkin'
                            }
    } 
```

执行此**动作**将提示*调度*改变(或不改变)**商店**的状态。

## 减速器

在单向数据流中，视图*将* **动作**分派给**减速器**。

根据函数式编程的定义， **reducer** 是一个纯函数，因此产生确定性的结果，没有副作用。

**减速器**有两个输入:状态和**动作**。State 是来自**库的整个状态对象，**action 是带有类型和可选负载的调度对象。

减速器*将*减少到**商店。**

```
 (state, action) => newState 
```

此外，它利用了**存储**中状态的不变性。以下是不正确的，因为它会改变先前的状态

```
 (state, action) => state.push(action.payload) 
```

下面是允许的，因为`concat`将不可变存储连接到有效负载。

```
 (state, action) => state.concat(action.payload) 
```

### 动作类型

我还没有提到类型。故事是当动作对象到达 reducer 的土地时，仿佛通过一个`switch`语句，动作类型变成了一把锁的钥匙(正确的 reducer)。如果钥匙没有打开任何门，那么状态保持不变。

一个*减速器落地的例子*

```
 function reducer(state, action) {
        switch(action.type) {
            case: 'set_thermostate_temp': {
                //do some magic IOT stuff
            }
            case: 'toggle_window_shutters': {
                return state.map(windows => window.id === action.id
                    ? Object.assign({}, windows, {opened: !window.opened})
                    : windows 
                );
            }
            default: return state;
        }
    } 
```

在`open_window_shutters`的情况下，`map`用于在底层的可控窗口上迭代，并切换唯一标识的实例`on`

`map`的功能总是返回新的数组，它不变异之前的状态。

返回一个新的对象，而不改变旧的对象，它将所有给定的对象从前者合并到后者。如果前一个对象与后一个对象共享相同的属性，将使用后一个对象的属性。因此，更新的`window`项的完成属性将是旧的`window`项的否定状态。

值得注意的是，actions 和 reducers 都是普通的 JS，到目前为止还没有 Redux magic。

此外，保持凉爽，干用止汗剂-提取物案件分支到纯功能

```
 function reducer(state, action) {
        switch(action.type) {
            case: 'set_thermostate_temp': {
                return applyThermostateTemp(state, action);
            }
            case: 'toggle_window_shutters': {
                return applyWindowToggler(state, action);
            }
            default: return state;
        }
    }
    function applyThermostateTemp(s, a) {
        return state.thermostate.currentTemperature = a.temp; // IOT ~~magic~~
    }
    function applyWindowToggler {
        return state.map(windows => window.id === action.id
                    ? Object.assign({}, windows, {opened: !window.opened})
                    : windows 
                );
    } 
```

## 商店

单一存储保存一个全局状态对象。存储将动作委托给缩减器。存储触发操作。Store 更新状态并通知订阅的组件。最后，它是 Redux 中遇到的第一个库依赖。

`import { createStore } from 'redux'`

要创建单例实例，传递强制参数- `reducer`和可选的初始状态

`const store = createStore(reducer, {})`

## 调度一个**动作**

`store.dispatch(action)`

## 从**存储器中读取全局状态**

`store.getState()`

订阅(和取消订阅)商店以收听更新

```
 const unsubscribe = store.subscribe(() => { 
        console.log(store.getState());
    });
    // eventually unsubscribe
    unsubscribe(); 
```

# 结论

你现在已经知道 Redux 的所有基础知识了。一个视图在存储上调度一个动作，该动作通过所有的 reducer，并被关心它的 reducer 减少。存储保存新的状态对象。最后，监听器用新状态更新视图。

## 附加材料

[https://Facebook . github . io/flow/](https://facebook.github.io/flux/)
[https://youtu . be/nykdraprdcw？list = plb 0 iamt 7-](https://youtu.be/nYkdrAPrdcw?list=PLb0IAmt7-)GS 188 xdye-u1 shqmffgbrk 0v
[https://Twitter . com/Dan _ abramov](https://twitter.com/dan_abramov)[【https://Twitter . com/acdlite](https://twitter.com/acdlite) [【https://www . YouTube . com/watch】v = xssnoqsynth](https://www.youtube.com/watch?v=xsSnOQynTHs)
[【https://www . YouTube . com/watch】？v=uvAXVMwHJXU](https://www.youtube.com/watch?v=uvAXVMwHJXU)