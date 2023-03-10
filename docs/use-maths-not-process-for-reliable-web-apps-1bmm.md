# 为可靠的网络应用程序使用数学而不是过程

> 原文：<https://dev.to/johnkazer/use-maths-not-process-for-reliable-web-apps-1bmm>

功能性前端 JavaScript 和状态机的结合导致单元和业务逻辑测试可以接近数学证明。[观看](https://github.com/johnkazer/hyperapp-xstate-demo/blob/xstate/cypress/videos/tests.js.mp4)Cypress 通过应用程序，使用自动生成的状态机图定义应用程序可能的 UI 动作和转换。

免责声明——要阅读这篇文章，您可能会了解 Hyperapp 不变地管理状态，使用虚拟 DOM 并托管纯函数来处理动作(如点击处理程序),效果如 http 请求和对外部事件或流的订阅。XState 管理状态机节点和转换以及由转换触发的动作。这个演示的关键是关联 Hyperapp 和 XState 动作。熟悉一些基本的 Cypress 测试也会有所帮助。

然而，了解一些关于 [Hyperapp](https://github.com/jorgebucaran/hyperapp) 动作和事件以及[状态机](https://xstate.js.org/docs/)的知识可能会有所帮助，因为这些都是超出本文范围的大主题。

## 为什么是状态机？

减轻应用程序开发的压力。

你不仅会知道你的应用程序的*纯功能*可以工作(现有单元测试)，而且你还会知道将它们连接在一起的*业务逻辑*也可以工作！

你可以使用容易理解的数学原理来构建可靠的应用程序，而不是依赖于软件过程。本文关注的是业务逻辑，而不是单元测试。

使用 XState 机器意味着你可以预定义和测试用户动作和事件序列如何导致状态改变，从而改变应用程序的行为。逻辑清晰且易于推理(而不是隐藏在功能集合或难以维护的独立流程图中)。您可以通过交互式状态机图表[可视化](https://statecharts.github.io/xstate-viz/)逻辑，并轻松创建[测试](https://glebbahmutov.com/blog/hyperapp-state-machine/#testing-from-state-machine)。稍后将详细介绍测试，但是请注意，状态机驱动 UI 测试来证明功能与逻辑相匹配。

*More* -如果您更新需求并改变状态机，测试也会自动更新。

# 如何？

Hyperapp 演示

这里我介绍一个 [Hyperapp](https://hyperapp.dev/) 演示，它用 [XState](https://xstate.js.org) 实现了一个简单的多媒体 [PWA](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) (渐进式 Web 应用)。你可以拍一张照片，创建一个记录，并模拟上传到云。如果脱机，PWA 将保存在本地，并在您重新联机时自动“上传”。很抱歉请求的浏览器权限-视频，音频和推送通知-它们是应用程序功能所必需的。但是没有东西离开你的浏览器！

这里的库是[这里的](https://github.com/johnkazer/hyperapp-xstate-demo)。主分支只使用 Hyperapp，而 xstate 分支修改应用程序以使用 XState 状态机。使用“npm start”构建并运行使用包。有两个状态机，一个用来控制拍照和保存照片，另一个用来录音。

注意，与大多数 Hyperapp 实现不同，这个应用使用 [Pug 来呈现 HTML](https://dev.to/johnkazer/hyperapp-with-pug-templates-517e) 。您可能更喜欢转换到更常用的 JSX 或 hyperscript 方法。

## 工作原理- Hyperapp

Hyperapp 维护一个中央状态和消息处理器，监听用户动作和浏览器事件。当一个动作或事件改变状态时，Hyperapp 使用一个虚拟 DOM 来更新应用程序。存在一个循环，一半对开发人员可见，一半在框架内。

动作->事件->监听->状态管理->虚拟 DOM-> DOM-更改->动作...

## 工作原理- XState

状态机是一种历史悠久的数学工具。他们对应用程序的实际应用与 Hyperapp 如何定义应用程序行为有一些共同的实践和概念特征。主要区别在于， [XState](https://xstate.js.org) 使得动作、事件和状态之间的关系可以在一个地方明确定义。

在 JSON 中创建了一个状态机，而 XState 系统提供了与它交互的纯函数。您的应用程序可以响应机器的动作和事件驱动的更改，或者直接请求状态更改。

操作-->[过渡-->可能的操作太多选择-->操作...

演示中有两个状态机，一个用于管理拍照，另一个用于录音。

你可以[可视化](https://statecharts.github.io/xstate-viz/)并与 machines.js 中定义的机器进行交互。我发现将视觉逻辑的体验与使用实际应用程序或模型的体验进行比较非常有用。你可以专注于功能，而不会被形式分散注意力。

## 工作原理 Hyperapp 中的 XState

管理链接的两个新功能。一个捕获事件(比如按钮点击)，另一个将 XState 动作转换成 Hyperapp 动作。实际上，这些函数充当了 Hyperapp 动作和状态以及 XState 动作和转换之间的桥梁。

app 实现的一些重要特性有助于这一过程。特别是，我通过对 UI 元素使用与它们相关的 Hyperapp 动作函数名称和 XState 转换相同的 id 名称，很容易在 UI 和逻辑之间建立链接。相反，在不同的状态机之间切换有点麻烦——在整体式和分布式之间取得平衡。

对于应用程序的视频处理部分，每个按钮都有这个`onclick=[updateVideoState, targetId]`元组作为点击处理程序。

点击处理函数`updateVideoState`从 Hyperapp 接收`state`和元素的`id`，Hyperapp 处理 DOM 事件。它将这些和状态机细节传递给下面所示的`processNewState`。该函数调用`runActions`将与转换相关的状态机动作链接到 Hyperapp 动作的执行。它必须处理 Hyperapp 动作(新状态)、事件(返回`[state, event]`元组)和一次性效果(没有返回对象)的结果。最后`processNewState`将更新后的状态和最新的状态机一起返回给 Hyperapp。

```
const processNewState = (state, { machineState, machineName, machine, id }) => {
    const previousState = pathOr(machineState, [machineState, 'value'], state)
    const newMachineState = machine.transition(previousState, id) // 'id' is synonymous with 'newState'
    const requests = runActions(state, newMachineState, id)
    const videoState = machineName === STATE_MACHINES.VIDEO_MACHINE ? newMachineState : state.videoState
    const audioState = machineName === STATE_MACHINES.AUDIO_MACHINE ? newMachineState : state.audioState
    if (requests.length === 1) { // capture the result of an action
        return { ...requests[0], videoState, audioState }
    } else if (requests.length === 2) { // capture the result of an action-effect tuple
        return [
            { ...requests[0], videoState, audioState },
            requests[1]
        ]
    }
    return { ...state, videoState, audioState } // state machine was updated
} 
```

`runActions`函数执行机器定义的动作，并允许 Hyperapp 处理结果。这些动作都是常规的 Hyperapp 动作函数，与常规使用 Hyperapp 的唯一不同之处在于，当有效转换发生时，状态机决定执行什么动作。注意，XState 中的一个动作是作为一个`action.exec`函数数组来维护的。

```
const runActions = (state, calcState, evtObj) => { // make recursive or map
    let requests = []
    calcState.actions.forEach(action => {
        const stateChangeRequest = action.exec(state,evtObj)
        const isArray = Array.isArray(stateChangeRequest)
        requests = concat(requests, isArray ? stateChangeRequest : [stateChangeRequest])
    });
    return requests
} 
```

# 测试！

测试问题有两个部分——纯函数的单元测试和由函数集生成的应用程序逻辑测试。

## 单元测试纯函数

由许多其他人写的...

## 用状态机测试 app 逻辑

自写测试！有什么不喜欢的？

嗯，不完全是自己写的但也不远了。XState 提供了一个[图形特性](https://xstate.js.org/docs/packages/xstate-graph/)，它生成了一组对象和数组，描述了通过应用程序状态机的路径。我已经使用 [Cypress](https://www.cypress.io/) 实现了一种方法，其中应用程序测试的控制由这些路径管理。参见 repo 的 xstate 分支中的 cypress/integration/tests/tests . js。

这里有一个 Cypress 输出的例子，展示了实现“上传”的 3 阶段路径的执行。再次注意 DOM 元素 id 和转换之间的共享命名。

[![Cypress test example following an XState path](img/5aefe468915d13b9534595ef622aed0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--txcaKyHA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1otsi69ksgdv8y9l2vzi.png)

在本例中，机器中定义了四种状态和动作:

```
const videoMachine = Machine ({
    id: 'videoMachine',
    initial: 'videoState',
    states: {
        videoState: {
            on: {
                captureImage: {
                    target: 'capture',
                    actions: ['captureImage']
                }
            }
        },
        capture: {
            on: {
                success: 'captured',
                fail: 'videoState'
            }
        },
        captured: {
            on: {
                uploadImage: {
                    target: 'upload',
                    actions: ['uploadImage']
                },
                discardImage: {
                    target: 'videoState',
                    actions: ['discardImage']
                }
            }
        },
        upload: {
            on: {
                uploadSuccess: {
                    target: 'videoState',
                    actions: ['discardImage']
                },
                uploadFail: 'captured'
            }
        }
    }
}) 
```

沿着状态机跟踪 Cypress 结果。下面是一个摘要，过渡“动作”在[]中

视频状态->*捕获图像*->捕获- > [ *成功*->捕获- > [ *上传图像*->上传- > [ *上传成功*->视频状态

第一个转换`captureImage`拍摄一张照片，并在`div`中显示结果。如果这是成功的，第二个过渡不涉及动作，因为 Hyperapp 解决了背景中的效果的结果。`success`的效果允许显示新的按钮——上传或丢弃。该路径为我们提供了一个`uploadImage`动作，然后是 Hyperapp 在后台管理“uploadSuccess”效果的最后一步。

最终的“uploadSuccess”步骤的结果并不明显，但是 Cypress 的“time-travel”(向后滚动每个测试步骤)显示我们请求了上传，并在之后收到了成功的响应。

提出上传请求(按钮点击):
[![Click the upload button](img/d73cbe8133e1d357d9eae73a39a77633.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jCpvG09o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/15h33d6gl7n6ccv28owq.png)

上传并准备拍摄下一张图片:
[![Successful upload](img/6c4e577e62be003d52f250e0c226c1c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G2rjqSgD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k4hzr3lgzo68bllw67ac.png)

这是 Cypress 测试代码的核心(我没有展示 XState-graph 对象，因为它们很复杂，但本质上是转换列表)。

```
const checkTransition = ({ state, event }) => {
    cy.log(`checking transition from ${state.value} due to ${event.type}`)
    // if there are paths[x].state.actions[y] then have Hyperapp and XState run the action(s) for us
    // Optionally we could drive the machine from here too
    if (state.actions.length > 0) {
        cy.log(view(machineName, state))
        const machine = currentMachine(state)
        // machine.transition(...)
        return
    }
    // else run paths[x].event
    // add logic to check for DOM elements with id === paths[x].event
    return cy.get(`button[id="${event.type}"]`).click()
}
const listOfPaths = curry((selectPaths, currentState) => {
    cy.log(`List of paths state: ${currentState}`)
    return selectPaths(currentState)
})
const checkPath = (path) => map(checkTransition, path)

// the test
it('tests each state path', () => {
    cy.visit('http://localhost:1234')
    const listOfVideoPaths = listOfPaths(selectSimpleVideoPaths)
    const testVideo = map(compose(appIsReady, reload, checkPath, listOfVideoPaths))
    cy.log('Test video paths')
    testVideo(videoStateList)
}) 
```

因此，核心代码对于任何 XState-graph 都是非常可重用的，唯一需要的定制调整是选择实际的 UI 动作，在本例中是`.click()`。

## 接下来是什么？

app 的其他方面可能适合状态机，包括上传/http、在线/离线状态和 PWA 安装。

Hyperapp 和 XState 之间有一个有趣的责任边界，这个演示刚刚开始探索——比如状态管理和动作定义。

建立一个用于定义被操作的 DOM 元素和期望的动作(例如，点击、悬停等)之间的关系的系统将是有用的。).目前这是相当手动的。

还可以查看一下关于状态机实现的 ROS maro——它有一个方便的图形用户界面来创建机器，而不是手工编写 JSON。

什么角色组件？

需要多少状态机，应该有多大？

XState/graph 返回可能路径的子集，但不是全部，因此这将是一个有用的升级，以确保完全覆盖。

它是一个可扩展的概念吗？

请补充评论！