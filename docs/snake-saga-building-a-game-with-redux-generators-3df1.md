# 蛇传奇-用 Redux 生成器构建游戏

> 原文：<https://dev.to/mariowhowrites/snake-saga-building-a-game-with-redux-generators-3df1>

在面试前端工作的过程中，我开始构建可以在一个周末内完成的短期项目，这既是为了练习编写可访问的 HTML/CSS，也是为了发现 JavaScript 的新功能。我最近参与的一个更有趣的项目是在 React 中构建一个贪吃蛇游戏——这不仅仅是因为这是我构建的第一个“视频游戏”。

我对这个项目特别感兴趣，因为它引入了我去年遇到的最独特的 JavaScript 特性之一:生成器。具体来说，因为我需要在每次蛇移动时采取行动，所以我对 Redux 生态系统中可用的主要副作用库进行了一些研究。

我的想法是，蛇的运动是“主要事件”，以及由它的运动而产生的所有潜在行为(吃水果、输掉比赛等)。)应在到达每个新广场后立即办理。因此，我的策略是将运动后的逻辑写入副作用中，副作用可以访问所有游戏信息，可以更新游戏，完全停止游戏，或者如果没有值得注意的事件发生，允许游戏继续进行。

我过去使用过 Redux Thunk，我相信我可以用 Redux Thunk 编写我的副作用逻辑，而不会有太多的麻烦。然而，因为这些附带项目的目的是学习新技能，并且因为 Redux Saga 的生成器模型似乎提供了更多的灵活性，如果我能够克服最初的学习曲线，那么这个库似乎是一个自然的选择。

此外，谁不喜欢自己的代码成为传奇的想法呢？想象一条小蛇在一艘带着维京帽的大船上航行，然后告诉我那不会让你笑逐颜开。

等等，别说了。通过写这个想法，我现在意识到航海蛇是可怕的。

在深入研究之前，如果你只是想看看代码，请查看这里的项目回购:[https://github.com/mariowhowrites/react-snake](https://github.com/mariowhowrites/react-snake)。请记住，这是一个周末项目，而不是生产作业。值得注意的是，如果我发行这款游戏，我会清理一些性能和样式问题——更不用说我需要编写一些测试了。

## 发电机:快速概述

我所见过的对生成器最直接的解释是，它们是你的程序可以随意启动和停止的函数。调用普通函数通常无法控制函数运行的方式和时间。你的程序简单地运行这个函数并滚动它，直到它返回一个值或者抛出一个错误。如果这个函数触发了一个无限循环，你的程序就会像[Bones 先生的狂野之旅](https://knowyourmeme.com/memes/mr-bones-wild-ride)中的可怜乘客一样等待出口(安全工作，过山车大亨内容)。

生成器的工作方式不同，它将执行控制权交给程序本身。换句话说，把生成器想象成一个循环，你的程序可以根据自己的时间递增。给定以下代码:

```
// the '*' marks this function as a generator
function* loopSayings() {
  yield "hello"
  yield "goodbye"
} 
```

第一次呼叫`loopSayings()`会启动发电机。为了进一步使用它，您可能希望将生成器保存为一个变量，比如`const loopGenerator = loopSayings()`。

从那里，您的程序可以通过调用`loopGenerator.next()`来控制生成器。每次调用方法`next()`时，生成器都会前进到函数中的下面的`yield`语句。

每当遇到一个`yield`语句，生成器就停止执行并返回一个具有两个属性的对象:

*   `value`将返回`yield`语句右边发生器停止的地方
*   `done`是一个布尔值，表示生成器是否已经到达最后的`yield`语句。在这一点之后进一步调用`next()`将给出一个未定义的`value`。

因此，在第一次启动发电机后，`loopGenerator.next().value`会返回“hello”。再次调用`loopGenerator.next().value`将返回值‘再见’，此时`done`属性将为真，并且所有未来的`next()`调用将返回未定义的值。

综上所述，生成器的示例用法如下所示:

```
function* loopSayings() {
  yield "hello"
  yield "goodbye"
}

const loopGenerator = loopSayings() // starts the generator
console.log(loopGenerator.next().value) // 'hello'
console.log(loopGenerator.next().value) // 'goodbye'
console.log(loopGenerator.next().value) // undefined, generator has finished 
```

## Redux Saga 中的发电机

现在我们已经对生成器的工作原理有了基本的了解，让我们看看这个模式是如何在 Redux Saga 库中应用的。让我们从显而易见的开始:Redux Saga 是一个构建在 Redux 状态管理库之上的库，它本身是管理 React 应用程序中复杂状态的最流行的工具。

具体来说，Redux Saga 主要作为 Redux **中间件工作。对于外行人来说，中间件本质上是一个花哨的术语，指的是在给定流程中间工作的任何逻辑。**

例如，如果我们正在构建一个 web 服务器，我们可以编写中间件来确定给定的用户是否可以访问特定的资源。这个中间件会在请求过程中发生，在用户发出请求之后，在我们的服务器开始获取资源之前。如果用户不能访问给定的资源——例如，如果他们没有登录，或者如果他们请求访问属于另一个用户的受保护数据——这个中间件可以立即停止请求，避免您的应用程序暴露敏感信息。

将这个模型应用到 Redux，所有的中间件在收到更新状态的请求后**运行，但是在**之前**你的 reducers 实际上已经更新以反映新的状态。这使得中间件能够在传入的状态请求到达 reducers 之前改变它们，提供了一种基于外部事件定制 Redux 逻辑的强大方法。**

在 Redux Saga 的例子中，因为库主要处理副作用，所以我们不会直接改变状态请求。然而，Redux Saga 充分利用了这样一个事实，即中间件不仅可以看到传入的动作，还可以看到 reducers 在分派动作时的当前状态。在我们的贪吃蛇游戏中，这种设置允许我们将当前的棋盘状态与正在分派的动作结合起来，以确定应该采取什么动作。

换句话说，根据我的经验，Redux Saga 提供了一个与其他语言和框架中的监听器或观察器非常相似的例子。它们观察外部事件，并可能触发新的动作来响应观察到的事件。

## 实践中的传奇故事

到目前为止，这种描述相当抽象——让我们用一些实际的 Snake 代码来为它打下基础。在我的 Snake 实现中，我将棋盘设置为方块的正方形网格。在我的 Redux 库中，我记录了哪些块代表墙、水果、空地和蛇本身。每一次，蛇向前移动一个块，新的位置作为 Redux 动作被调度。

在我的例子中，我写了四个不同的传奇故事来听游戏棋盘上发生的各种事件:

```
import { all } from "redux-saga/effects"

import watchPosition from "./watchPosition"
import watchFruitCollection from "./watchFruitCollection"
import { watchGameStart, watchGameEnd } from "./watchGameChange"

export default function* rootSaga() {
  yield all([
    watchPosition(),
    watchFruitCollection(),
    watchGameStart(),
    watchGameEnd(),
  ])
} 
```

`all()`方法接受一组 sagas 并将它们组合成一个中间件，该中间件在加载主应用程序前不久附加到 Redux 存储中。

让我们来看看水果收集传奇，每当收集到新的水果时就会触发:

```
import { takeEvery, put, select } from "redux-saga/effects"

import * as types from "../store/types"
import { makeFruit } from "../utils"

export default function* watchFruitCollection() {
  yield takeEvery(types.FRUIT_COLLECT, handleFruitCollection)
}

function* handleFruitCollection({ payload }) {
  const size = yield select(state => state.game.size)
  yield put({ type: types.FRUIT_ADD, payload: [makeFruit(size)] })
  yield put({ type: types.ADD_SCORE })
} 
```

注意，这个故事本身只包含一行代码，以`takeEvery`调用开始。这个函数告诉 Redux Saga“接受”每个类型为`FRUIT_COLLECT`的动作，并将动作传递给`handleFruitCollection`方法。

从那里，因为我们知道动作是类型`FRUIT_COLLECT`，我们知道蛇刚刚收集了一个新的水果，我们可以相应地分派动作。也就是说，当采集新的水果时，应该采取两个行动:

1.  玩家分数需要增加 1。
2.  一个新的水果需要被添加到游戏板上。

要在棋盘上添加一个新的水果，我们首先需要知道我们的游戏棋盘有多大，这样我们就不会意外地在不应该的地方添加水果——也就是说，在墙内或墙外。为了获得电路板尺寸，我们首先使用 Redux Saga 提供的`select`函数从我们的`game`减速器中提取`size`属性。从那里，我们使用由`makeFruit`创造的一个新水果分派一个新动作`FRUIT_ADD`，它在游戏板上的随机有效位置返回一个新水果。

完成后，剩下唯一要做的事情就是增加当前的分数。我们没有处理传奇故事中的状态变化，而是调度了一个类型为`ADD_SCORE`的新动作，我们的`game`缩减器将捕捉并使用它来更新玩家的分数。

这里有两个重要的过程:

1.  所有状态修改都被归入 reducers，而不是直接在 saga 中处理。这是一个有意的设计模式——Redux Sagas 被认为是副作用，而不是次要的还原剂。
2.  我们的处理程序生成器没有被直接调用。相反，Redux Saga 中间件负责调用我们的生成器，它遍历每个 Saga，直到生成器的`done`属性返回`true`为止。

## 到底为什么要用发电机？

因为生成器过程是由我们的 Redux Saga 中间件以同步方式处理的，所以您可能会奇怪为什么在这种情况下使用生成器。将我们所有的状态更新逻辑包含在 reducer 本身中不是更快更直接吗？有什么能阻止我们增加玩家分数，在`COLLECT_FRUIT`缩减器中添加新的果实，并完全跳过 Redux Saga？

对于您的应用程序来说，Redux Saga 是否是一个好主意主要取决于规模。对于一个简单的项目，在 reducer 函数本身中写出所有 Redux 数据突变可能是有意义的。然而，更复杂的应用程序通常需要更多的原因和结果之间的分离，这是通过将所有的逻辑分组到同一个文件中所无法获得的。通过将更新的所有“副作用”从更新本身中分离出来，我们可以保持我们的 reducer 简单明了并添加额外的副作用，而无需更改我们的 reducer 代码并向我们自己开放与状态相关的 bug。

为了更好地说明这一点，让我们看看 Snake 应用程序中的`watchPosition`传奇:

```
export default function* watchPosition() {
  yield takeEvery(types.CHANGE_POSITION, handlePositionChange)
}

const getState = state => ({
  fruitPositions: state.fruit.fruitPositions,
  snakeQueue: state.snake.snakeQueue,
  snake: state.snake.snake,
})

function* handlePositionChange({ payload: newPosition }) {
  const { fruitPositions, snakeQueue, snake } = yield select(getState)

  const gameIsOver = collidedWithSelf(snake, newPosition)

  if (gameIsOver) {
    yield put({ type: types.GAME_END })
    return
  }

  const fruitToRemove = findFruitToRemove(fruitPositions, newPosition)

  if (fruitToRemove >= 0) {
    yield put({ type: types.FRUIT_COLLECT, payload: fruitToRemove })
    yield put({ type: types.SNAKE_QUEUE, payload: newPosition })
  }

  if (snakeQueue.length >= 1) {
    yield put({ type: types.SNAKE_GROW })
  }
} 
```

我们看到`watchPosition`与上面的`watchFruitCollection`具有几乎相同的结构。所有`CHANGE_POSITION`类型的动作都发生在由`handlePositionChange`发生器领导的新传奇上。

然而，从那里开始，一系列更复杂的事件发生了。使用辅助方法，这个生成器检查各种游戏条件，比如蛇是否与自己发生了碰撞或者收集了一个水果。

在位置缩减器中处理水果收集逻辑有意义吗？对我来说，答案是否定的。通过将所有的效果工作委托给 sagas，我的每个 reducer 案例最多只需要大约五行代码。我可以在这个`watchPosition`生成器中添加尽可能多的功能，而不需要改变我的蛇在棋盘上移动的基本机制。因为`put`和`select`返回简单的 JavaScript 对象，所有这些代码都可以通过手动启动和迭代我们的传奇来轻松测试，就像我们在《生成器导论》中对`loopSayings`所做的一样。