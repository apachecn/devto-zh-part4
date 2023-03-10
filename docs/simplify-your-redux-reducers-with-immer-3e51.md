# 用 Immer 简化您的 Redux 减速器

> 原文：<https://dev.to/mercatante/simplify-your-redux-reducers-with-immer-3e51>

* * *

*本帖最初发表于[https://stevenmercatante.com](https://stevenmercatante.com/simpify-redux-reducers-with-immer/)T3】*

* * *

## 问题

我在一个项目中使用 Redux，看到我的 Redux 变得有点粗糙。每当我需要处理嵌套数据时，代码似乎都会呈指数级增长。这是因为 Redux 坚持使用不可变数据。我是使用不可变数据的忠实粉丝，但绝对不止于此...与可变数据相比，很难处理。我们来看一些例子:

```
case ADD_TIMER: {
  return { [action.payload.id]: action.payload, ...state };
} 
```

我知道你在想什么...“这段代码并不笨拙——它只是使用 spread 操作符将一个项目添加到一个现有的对象中。容易！”好吧，我们继续...

```
case REMOVE_TIMER: {
  const newState = { ...state };
  delete newState[action.payload.id];
  return newState;
} 
```

好的，这还不算太糟，但是我想做的只是从一个对象中删除一个项目。我不需要创建现有状态的副本，从副本中删除项目，然后返回副本。

```
case INCREMEMT_RUNNING_TIMERS: {
  const updatedTimers = Object.values(state)
    .filter(timer => timer.running)
    .reduce((acc, timer) => {
      timer.totalTime = getTotalTime(true, timer.starts, timer.stops);
      acc[timer.id] = timer;
      return acc;
    }, {});
  return { ...state, ...updatedTimers };
} 
```

祝你能说服我这是不可能改进的。如果您想知道，我正在遍历对象，只过滤我想要的对象，将它们减少到一个具有一些更新属性的新对象中，最后将它合并到返回的状态中。呀。

## 解

[Immer](https://github.com/immerjs/immer) 来救援！Immer 让您“通过简单地修改当前树来创建下一个不可变的状态树。”那是什么意思？我们把上面的代码例子转换一下看看。

```
case ADD_TIMER: {
  draft[action.payload.id] = action.payload;
  break;
} 
```

```
case REMOVE_TIMER: {
  delete draft[action.payload.id];
  break;
} 
```

```
case INCREMEMT_RUNNING_TIMERS: {
  Object.values(draft).forEach(timer => {
    if (timer.running) {
      timer.totalTime = getTotalTime(true, timer.starts, timer.stops);
    }
  });
  break;
} 
```

*(不要担心那个`draft`变量——我们稍后会谈到它...)*

看那个！代码更短，更容易阅读，更容易理解。但是，这不是打破了 Redux 对不可变操作的需求吗？没有。Immer 在幕后执行不可变的操作，但是它让我们可以编写可变的操作，10 次中有 9 次更容易推理(更不用说更快地编写)。这个秘密就是 Immer 的 a `draftState`概念。

## 草稿状态

与其自己解释，不如让我来解释一下 Immer 是如何定义`draftState`的:

> 基本思想是将所有更改应用到临时的 draftState，它是当前状态的代理。一旦你所有的突变都完成了，Immer 将会基于草稿状态的突变产生下一个状态。这意味着您可以通过简单地修改数据来与数据进行交互，同时保留不可变数据的所有优势。”

您需要向您的缩减器添加一点代码。这里有一个完整的例子:

```
import produce from 'immer'

export default (state = {}, action) =>
  produce(state, draft => {
    switch (action.type) {
      case ADD_TIMER: {
        draft[action.payload.id] = action.payload
        break
      }

      case REMOVE_TIMER: {
        delete draft[action.payload.id]
        break
      }

      case INCREMEMT_RUNNING_TIMERS: {
        Object.values(draft).forEach(timer => {
          if (timer.running) {
            timer.totalTime = getTotalTime(true, timer.starts, timer.stops)
          }
        })
        break
      }

      default:
        return draft
    }
  }) 
```

请确保您没有忘记对`produce`的呼叫——没有它，您的减速器将无法工作！

## 把东西包起来

每当我从事 Redux 项目时，Immer 已经成为我的首选工具之一。向同事和贡献者解释只需很少的时间，并提供一系列好处，包括:

*   需要编写的代码更少
*   需要维护的代码更少
*   更容易理解的代码

如果你需要进一步的说服，看看 React 的一个核心维护者是怎么说的:

> 如果你喜欢 MobX，我强烈推荐跟随 [@mweststrate](https://twitter.com/mweststrate?ref_src=twsrc%5Etfw) 在 Immer 上的作品。虽然 MobX 与 React 的愿景相去甚远。伊莫完全正确。
> 
> — Sebastian Markbåge (@sebmarkbage) [August 23, 2018](https://twitter.com/sebmarkbage/status/1032684851063705600?ref_src=twsrc%5Etfw)

👋喜欢这篇文章吗？

加入我的[时事通讯](https://stevemerc.com/newsletter/?r=dev)并在 Twitter[@ mercantate](https://twitter.com/mercatante)上关注我以获得更多类似的内容。