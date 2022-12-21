# Redux 中的高级操作

> 原文：<https://dev.to/fosteman/advanced-actions-in-redux-mi9>

我将介绍 Redux 中关于动作和缩减器的一些细粒度细节

*为了让每一位读者都步入正轨，我将从头开始跟踪-*

我将展示极简的**动作有效载荷**并提高复杂度，顺便用**动作创建者**，可选的**有效载荷**和**有效载荷结构**提高可读性，减少*气味*并达到这种金色:

"*那会管用一阵子*"

那就走吧！

# 最小动作有效载荷

一个切换物联网驱动的窗口处理器的示例操作，它将打开/关闭框架

```
function applyWindowToggler(state, action) {
    return state.map(window => window.id === action.id
        ? Object.assign({}, window, {toggle: !window.toggle})
        : window
    );
} 
```

这里，`toggle`属性是原始`window`对象的副本的否定值，它覆盖副本的属性，最后，将新对象返回给 reducer。

良好的实践是保持动作负载最小。用有效负载的属性替换缩减器产生的属性。

想象一下，有一个授权令牌、请求动作的作者、执行确认、api 等等。如果在前端处理**所有的**，这听起来像是紧耦合。在这种情况下，你可能会想出某种中间件 api，或者是**商店**的**增强器**，来获取必要的对象。

例如，如果在状态的*归约*过程中有属性要添加，比如`dateModified`，就把它放在归约器里面

```
function applyWindowToggler(state, action) {
    return state.windows.map(window => window.id === action.id
        ? Object.assign({}, window, {toggle: !window.toggle}, {dateModified: () => new Date()})
        : window
    );
} 
```

## 动作类型

动作由 **`type`评估。**为了减少来自减速器的不确定性气味，有一个**动作型提取**的练习，避免碰到**型**错别字。

```
const SET_THERMOSTAT_TEMP = 'SET_THERMOSTAT_TEMP';
const TOGGLE_WINDOW = 'TOGGLE_WINDOW';
const SHUT_WINDOWS = 'SHUT_WINDOWS'; 
```

此外，将动作类型提取为变量，可以在单独的模块中定义 reducer 和动作类型。仅导入操作类型以用于特定的操作和减少器。毕竟，一个动作类型可以在多个减速器中使用。查看我的高级减速器帖子！

## 动作创建者

**动作创造者**是一种可有可无的便利，有时会滋生困惑。先说清楚。

**动作创建器**将一个**动作**和可选的**负载**封装在一个**可重用的纯函数**中

```
function shutWindows(reason, ...id) {
    return {
        type: SHUT_WINDOWS,
        reasom,
        windows: [...id]
}

store.dispatch(shutWindows('DeepWork time!', [0, 1]); 
```

Action creator 将返回普通 Action 对象，即使不强制使用它们，这种做法也为您的代码库提供了额外的解耦、可重用性和测试功能。

## 可选有效载荷

注意，本地状态管理可能被用于 UI 琐事，然而，本文是关于 **Redux** ，而不是前端(React \ Vue)。

有人说"*有效载荷是可选的"*，"...只有操作类型是必需的”。这很好地耦合了视图中的实体、下拉列表和模态等组件，但是，这是有争议的

```
const TOGGLE_LOGIN_MODAL = 'TOGGLE_LOGIN_MODAL';
const openLoginModal = {
    type: TOGGLE_LOGIN_MODAL
}; 
```

你提到冗长了吗？正如您现在所看到的，真正拥有控制权的是开发人员(true \ false 打开\关闭),或者只是在完全移除有效载荷的情况下拨动开关。

```
const openLoginModal = {
    type: TOGGLE_LOGIN_MODAL,
    isLoginModalOpen: true
} 
```

我们可以更进一步，编写一个**动作创建器**，为包含有效负载的*控制的*动作增加灵活性。

```
function doToggleLoginModal(open) = {
    return {
        type: TOGGLE_LOGIN_MODAL,
        isLoginModalOpen: open
    };
} 
```

在 Redux 中，动作应该力求抽象，否则会有冗长和难以忍受的代码库。但是如果失去对 UI 的控制，最好不要冒险。

## 有效载荷结构

复杂的有效负载，如果包含记录发出`SHUT_WINDOWS`动作的人或设备之间关系的`auth_token`，看起来会像

```
{
    type: SHUT_WINDOWS,
    authorizedBy: { id: 0, name: 'Timofei Shchepkin' },
    windows: [0, 1]
} 
```

属性在水平方向上相加，最终传达一个最重要的属性: **`type`。**有人可能会说，这听起来很罗嗦，这是对的。嵌套有效载荷因此建议更深一层，使它们第一眼就可见

```
{
    type: SHUT_WINDOWS,
    payload: {
        authorizedBy: { id: 0, name: 'Timofei Shchepkin' },
        windows: [0, 1]
    }
} 
```