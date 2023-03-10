# 这里有 7 种高阶函数可以改善你的生活

> 原文：<https://dev.to/jsmanifest/here-are-7-ways-higher-order-functions-can-improve-your-life-1j>

在[媒体](https://medium.com/@jsmanifest)上找到我。

JavaScript 的基本构件之一是函数。在学习 JavaScript 时，这足以引起任何人对函数语义的兴趣。

但是函数比这更有趣。

“比如什么”，你问？

它们可以成为*高阶函数*——将其他函数作为参数的函数，或者返回函数作为输出的函数。

在这条线上有一大堆的*函数*——JavaScript 是*没有什么不同*。在 JavaScript 中，函数到处都是*。你应该为他们感到幸福！*

 *以下是高阶函数可以改善你生活的 7 个原因:

## 1。它们增强了您现有的代码，增加了信心

[![higher order functions can enhance your existing code](img/e866fbecf0d378cf97ab5a06d47a33d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gzvJ3QTK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/7-ways-higher-order-functions-can-improve-your-life/1.jpg)

假设有一个函数的任务是从 API 中检索青蛙的列表，使用返回的列表计算青蛙舌头的平均宽度并返回结果。

例如:

```
import axios from 'axios'

const getFrogs = async (params) => {
  try {
    const response = await axios.get(
      'https://frogs-and-their-tongues.com',
      params,
    )
    const frogs = response.data.result
    return frogs
  } catch (error) {
    throw error
  }
}

const calcAverageWidthOfTongues = async (params) => {
  try {
    const frogs = await getFrogs(params)
    const tongueWidths = frogs.reduce((sum, frog) => {
      return sum + frog.tongueWidth
    }, 0)
    const averageWidth = tongueWidths / frogs.length
    return averageWidth
  } catch (error) {
    throw error
  }
}

calcAverageWidthOfTongues({
  username: 'bob',
  password: 'the_builder100',
})
  .then((result) => {
    console.log(result)
  })
  .catch((error) => {
    console.error(error)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们只能返回一个数字。但是，如果有一种方法可以在不改变原始函数的情况下将其转换为对象*呢？*

这里的*增强*能够传递额外的选项来定制和转换输出，以便更好地控制:

```
import axios from 'axios'

const getFrogs = async (params) => {
  try {
    const frogs = await axios.get('https://frogs-and-their-tongues.com', params)
    return data
  } catch (error) {
    throw error
  }
}

const calcAverageWidthOfTongues = async (params) => {
  try {
    const frogs = await getFrogs(params)
    const tongueWidths = frogs.reduce((sum, frog) => {
      return sum + frog.tongueWidth
    }, 0)
    const averageWidth = tongueWidths / frogs.length
    return averageWidth
  } catch (error) {
    throw error
  }
}

const useTongueObj = (fn, options) => {
  return async (params) => {
    const newParams = { ...params }
    if (options.limit !== undefined) {
      newParams.limit = options.limit
    }

    let averageWidth = await fn(newParams)

    if (typeof options.multiplyBy === 'number') {
      averageWidth = averageWidth * options.multiplyBy
    }

    return {
      averageWidth,
      multiplied: typeof options.multiplyBy === 'number',
      size: averageWidth < 2 ? 'small' : 'large', // size in inches
    }
  }
}

const calcTongueWidths = useTongueObj(calcAverageWidthOfTongues, {
  multiplyBy: 2,
})

calcTongueWidths({ limit: 10 })
  .then((tongueObj) => {
    console.log(tongueObj)
    /*
      result:
        {
          averageWidth: 8,
          multiplied: true,
          size: 'large'
        }
    */
  })
  .catch((error) => {
    console.log(result)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

## 2。它们节省了宝贵的时间

[![higher order functions can help save precious and valuable time](img/8ef71b3e1cd9ff936c4033059b96c9db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvT9f9YW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/7-ways-higher-order-functions-can-improve-your-life/2.jpg)

让我举一个现实生活中的例子，因为我想在这个例子上多强调一点。

高阶函数最大的好处之一是，当正确使用时，它会为*你*和*你周围的人*节省*很多*时间。

在我的工作中，我们使用[react-to-stify](https://github.com/fkhadra/react-toastify)来显示通知。我们在任何地方都使用它*。此外，他们还为 UX 最后一分钟的快速决策提供了很好的逃生通道:“我们应该如何处理这个错误？只显示一个祝酒通知！”完成了。*

 *然而，我们开始注意到，当应用程序变得越来越大，复杂程度越来越高时，我们的 toast 通知变得过于频繁。这很好——但是，我们没有办法防止重复。这意味着一些 toast 通知会在屏幕上出现多次，即使它们与上面的 toast 完全相同。

因此，我们最终利用该库提供的 api，使用*toast . disass()*通过 *id* 来帮助删除活动的 toast 通知。

为了解释前面的部分，在继续之前显示我们从中导入祝酒词的文件可能是个好主意:

```
import React from 'react'
import { GoCheck, GoAlert } from 'react-icons/go'
import { FaInfoCircle } from 'react-icons/fa'
import { MdPriorityHigh } from 'react-icons/md'
import { toast } from 'react-toastify'

/*
  Calling these toasts most likely happens in the UI 100% of the time.
  So it is safe to render components/elements as toasts.
*/

// Keeping all the toast ids used throughout the app here so we can easily manage/update over time
// This used to show only one toast at a time so the user doesn't get spammed with toast popups
export const toastIds = {
  // APP
  internetOnline: 'internet-online',
  internetOffline: 'internet-offline',
  retryInternet: 'internet-retry',
}

// Note: this toast && is a conditional escape hatch for unit testing to avoid an error.
const getDefaultOptions = (options) => ({
  position: toast && toast.POSITION.BOTTOM_RIGHT,
  ...options,
})

const Toast = ({ children, success, error, info, warning }) => {
  let componentChildren
  // Sometimes we are having an "object is not valid as a react child" error and children magically becomes an API error response, so we must use this fallback string
  if (!React.isValidElement(children) && typeof children !== 'string') {
    componentChildren = 'An error occurred'
  } else {
    componentChildren = children
  }
  let Icon = GoAlert

  if (success) Icon = GoCheck
  if (error) Icon = GoAlert
  if (info) Icon = FaInfoCircle
  if (warning) Icon = MdPriorityHigh

  return (
    <div style={{ paddingLeft: 10, display: 'flex', alignItems: 'center' }}>
      <div style={{ width: 30, height: 30 }}>
        <Icon style={{ color: '#fff', width: 30, height: 30 }} />
      </div>
      <div style={{ padding: 8, display: 'flex', alignItems: 'center' }}>
        &nbsp;&nbsp;
        <span style={{ color: '#fff' }}>{componentChildren}</span>
      </div>
    </div>
  )
}

export const success = (msg, opts) => {
  return toast.success(<Toast success>{msg}</Toast>, {
    className: 'toast-success',
    ...getDefaultOptions(),
    ...opts,
  })
}

export const error = (msg, opts) => {
  return toast.error(<Toast error>{msg}</Toast>, {
    className: 'toast-error',
    ...getDefaultOptions(),
    ...opts,
  })
}

export const info = (msg, opts) => {
  return toast.info(<Toast info>{msg}</Toast>, {
    className: 'toast-info',
    ...getDefaultOptions(),
    ...opts,
  })
}

export const warn = (msg, opts) => {
  return toast.warn(<Toast warning>{msg}</Toast>, {
    className: 'toast-warn',
    ...getDefaultOptions(),
    ...opts,
  })
}

export const neutral = (msg, opts) => {
  return toast(<Toast warning>{msg}</Toast>, {
    className: 'toast-default',
    ...getDefaultOptions(),
    ...opts,
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

听我说，我知道这看起来不太吸引人。但我保证两分钟后会好的。

这是我们在一个单独的组件中用来检查之前的 toast 是否已经在屏幕上。如果有，它将尝试移除该 toast 并重新显示新 toast。

```
import { toast } from 'react-toastify'

import {
  info as toastInfo,
  success as toastSuccess,
  toastIds,
} from 'util/toast'

const onOnline = () => {
  if (toast.isActive(toastIds.internetOffline)) {
    toast.dismiss(toastIds.internetOffline)
  }
  if (toast.isActive(toastIds.retryInternet)) {
    toast.dismiss(toastIds.retryInternet)
  }
  if (!toast.isActive(toastIds.internetOnline)) {
    toastSuccess('You are now reconnected to the internet.', {
      position: 'bottom-center',
      toastId: toastIds.internetOnline,
    })
  }
}

const onOffline = () => {
  if (!toast.isActive(toastIds.internetOffline)) {
    toastInfo('You are disconnected from the internet right now.', {
      position: 'bottom-center',
      autoClose: false,
      toastId: toastIds.internetOffline,
    })
  }
}

useInternet({ onOnline, onOffline })

return <App /> 
```

Enter fullscreen mode Exit fullscreen mode

这工作得很好——然而，我们在整个应用程序中有其他的祝酒词需要以同样的方式修改。我们必须仔细检查每一个显示祝酒词的文件来删除重复的。

当我们想到*在 2019 年检查每一个文件*时，我们立即知道这不是解决方案。因此，我们查看了`util/toast.js`文件，并对其进行了重构，以解决我们的问题。这是后来的样子:

> src/util/toast.js

```
import React, { isValidElement } from 'react'
import isString from 'lodash/isString'
import isFunction from 'lodash/isFunction'
import { GoCheck, GoAlert } from 'react-icons/go'
import { FaInfoCircle } from 'react-icons/fa'
import { MdPriorityHigh } from 'react-icons/md'
import { toast } from 'react-toastify'

/*
  Calling these toasts most likely happens in the UI 100% of the time.
  So it is safe to render components/elements as toasts.
*/

// Keeping all the toast ids used throughout the app here so we can easily manage/update over time
// This used to show only one toast at a time so the user doesn't get spammed with toast popups
export const toastIds = {
  // APP
  internetOnline: 'internet-online',
  internetOffline: 'internet-offline',
  retryInternet: 'internet-retry',
}

// Note: this toast && is a conditional escape hatch for unit testing to avoid an error.
const getDefaultOptions = (options) => ({
  position: toast && toast.POSITION.BOTTOM_RIGHT,
  ...options,
})

const Toast = ({ children, success, error, info, warning }) => {
  let componentChildren
  // Sometimes we are having an "object is not valid as a react child" error and children magically becomes an API error response, so we must use this fallback string
  if (!isValidElement(children) && !isString(children)) {
    componentChildren = 'An error occurred'
  } else {
    componentChildren = children
  }
  let Icon = GoAlert
  if (success) Icon = GoCheck
  if (error) Icon = GoAlert
  if (info) Icon = FaInfoCircle
  if (warning) Icon = MdPriorityHigh
  return (
    <div style={{ paddingLeft: 10, display: 'flex', alignItems: 'center' }}>
      <div style={{ width: 30, height: 30 }}>
        <Icon style={{ color: '#fff', width: 30, height: 30 }} />
      </div>
      <div style={{ padding: 8, display: 'flex', alignItems: 'center' }}>
        &nbsp;&nbsp;
        <span style={{ color: '#fff' }}>{componentChildren}</span>
      </div>
    </div>
  )
}

const toaster = (function() {
  // Attempt to remove a duplicate toast if it is on the screen
  const ensurePreviousToastIsRemoved = (toastId) => {
    if (toastId) {
      if (toast.isActive(toastId)) {
        toast.dismiss(toastId)
      }
    }
  }

  // Try to get the toast id if provided from options
  const attemptGetToastId = (msg, opts) => {
    let toastId
    if (opts && isString(opts.toastId)) {
      toastId = opts.toastId
    } else if (isString(msg)) {
      // We'll just make the string the id by default if its a string
      toastId = msg
    }
    return toastId
  }

  const handleToast = (type) => (msg, opts) => {
    const toastFn = toast[type]
    if (isFunction(toastFn)) {
      const toastProps = {}
      let className = ''
      const additionalOptions = {}
      const toastId = attemptGetToastId(msg, opts)
      if (toastId) additionalOptions.toastId = toastId
      // Makes sure that the previous toast is removed by using the id, if its still on the screen
      ensurePreviousToastIsRemoved(toastId)
      // Apply the type of toast and its props
      switch (type) {
        case 'success':
          toastProps.success = true
          className = 'toast-success'
          break
        case 'error':
          toastProps.error = true
          className = 'toast-error'
          break
        case 'info':
          toastProps.info = true
          className = 'toast-info'
          break
        case 'warn':
          toastProps.warning = true
          className - 'toast-warn'
          break
        case 'neutral':
          toastProps.warning = true
          className - 'toast-default'
          break
        default:
          className = 'toast-default'
          break
      }
      toastFn(<Toast {...toastProps}>{msg}</Toast>, {
        className,
        ...getDefaultOptions(),
        ...opts,
        ...additionalOptions,
      })
    }
  }

  return {
    success: handleToast('success'),
    error: handleToast('error'),
    info: handleToast('info'),
    warn: handleToast('warn'),
    neutral: handleToast('neutral'),
  }
})()

export const success = toaster.success
export const error = toaster.error
export const info = toaster.info
export const warn = toaster.warn
export const neutral = toaster.neutral 
```

Enter fullscreen mode Exit fullscreen mode

最简单的解决方案是创建一个高阶函数，而不是遍历每个文件。这样做可以让我们“颠倒”角色，这样我们就不用在文件中搜索，而是将祝酒词*导向我们的高阶函数*。

通过这种方式，文件中的代码没有被*修改或者*接触过*。它们仍然正常工作，我们获得了删除重复祝酒词的能力，最终不用去任何地方写不必要的代码。*这样节省了时间*。*

## 3。他们赋予创造“*私人世界*的能力

[![higher order functions can create private worlds](img/c8c01b1fb8288a45180f8e3eaea21113.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dqaw42E3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/7-ways-higher-order-functions-can-improve-your-life/3.jpg)

我说的“私人世界”是什么意思？

嗯，考虑这个例子:

```
const preparePeopleWithFavoriteColor = (color) => {
  const _people = []

  return {
    getPeople() {
      return _people
    },
    addPeople(people) {
      _people.push(...people)
    },
    addPerson(person) {
      _people.push(person)
    },
    gather(people) {
      if (Array.isArray(people)) {
        people.forEach((person) => {
          if (color === person.favoriteColor) {
            _people.push(person)
          }
        })
      }
    },
  }
}

const peopleWhoLoveRed = preparePeopleWithFavoriteColor('red')

axios
  .get('https://someapi.com/peoples')
  .then((response) => {
    const people = response.data.result

    if (people.length) {
      peopleWhoLoveRed.gather(people)
    }

    return axios
      .get('https://someapi.com/other-peoples')
      .then((response) => {
        const morePeople = response.data.result
        if (morePeople.length) {
          everyoneWhoLovesRed.gather(morePeople)
        }
        return
      })
      .then(() => {
        // finally, add me last because i love red too
        peopleWhoLoveRed.addPerson({
          nickName: 'jsmanifest',
          favoriteColor: 'red',
        })

        return axios.post('https://api.redlovers.com/v1/subscribers/', {
          people: peopleWhoLoveRed.getPeople(),
        })
      })
  })
  .catch((error) => {
    console.error(error)
  }) 
```

Enter fullscreen mode Exit fullscreen mode

在代码片段中，*prepareppeoplewithfavorite color*在将下一个函数返回给调用者之前被调用时，会在其块内创建一个*私人世界*。这个“私人世界”没有因名称冲突而污染外部范围，并保持自己的*私有*变量和值。

此外，它创建自己的*接口和本地 api* 来管理自己的*人员*列表。外部的作用域将*永远不会*知道内部发生了什么——他们这样做的唯一方式依赖于它返回的公共方法。

如果你足够狡猾，你可以在这些可爱的小程序块中偷偷调用一个秘密的 API，在每次代码运行时向你发送喜欢红色的人的列表——用户甚至无法分辨，因为它不会影响应用程序中的任何其他内容。

## 4。它们可以被用作快速而简单的解决方案，暂时减轻压力

在最黑暗的时候，高阶函数通常可以挽救你的生命，因为它可以是解决代码问题的最快方法，而不会对你的应用程序产生任何明显的变化。

我曾经遇到过一个问题，用户在退出 react 应用后，甚至在后的*,仍然会在导航条上看到一个“仪表盘”按钮。仪表板按钮应该只有在应用程序检测到他们已经登录时才可见。当他们单击仪表板按钮时，会被重定向到他们的用户仪表板页面。*

当时，他们退出的方式是前往他们的个人资料页面，然后单击“退出”按钮。单击该按钮时，他们应该已经完全注销，并被重定向回登录页面。

但是*为什么*T2 仪表盘按钮仍然显示在导航栏中？用户退出了，当我调试这个问题时，客户机中的所有东西，包括本地存储，都被清空了——至少我是这样认为的。

然后我意识到问题是*还原状态*的某些部分仍然存在。*重定向*使用了一个*导航*，该导航被优化为将用户重定向到一个不同的路径*，而无需刷新整个页面*。这导致了缓存问题。

那么，我如何确保 redux 中的整个状态在用户注销后重置呢？

注销后，我们已经 redux 调度一个动作类型为 *LOGOUT* 的动作创建器，它应该向应用程序发出用户正在注销的信号。

我提供解决方案的直接焦点是以某种方式修改动作创建器。这似乎是提供这种增强的最佳地点。

然后我意识到这不是最好的解决方案，因为有一个*更好的*解决方案:使用一个*高阶函数*(但这里的*不是*)。

这里有一个很好的例子来说明为什么在 JavaScript 中高阶函数如此强大:

我们在`src/reducers/index.js`中的根 reducer 文件导出了这个:

```
export default combineReducers({
  app,
  form: formReducer,
}) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们应用了一个*高阶函数*来包装它，并且每当它检测到类型为*注销*的动作被分派时，应用一个*重置状态*逻辑:

```
const appReducer = combineReducers({
  app,
  form: formReducer,
})

// Higher order reducer that resets the redux state when we dispatch a logout action
const rootReducer = (state, action) => {
  if (['LOGOUT'].includes(action.type)) {
    state = undefined
  }
  return appReducer(state, action)
}

export default rootReducer 
```

Enter fullscreen mode Exit fullscreen mode

这将状态重置回其初始状态，因为我们将状态重新分配给了未定义的。 [Redux 会调用状态未定义的 reducer，并返回 app 的初始状态](https://redux.js.org/basics/reducers#handling-actions)。

## 5。它可以保存、操作并在任何地方传递被操作的数据，让您可以轻松地测试不同的解决方案

[![higher order functions can help manipulate and pass data around](img/5b161f9d7f3bc00e99668e2d160a9ce0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2rYvcNwE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/7-ways-higher-order-functions-can-improve-your-life/4.jpg)

你可以用高阶函数做的最酷的事情之一是保存一个私有的数据缓存，操纵它并在你喜欢的应用程序中的任何地方传递它。任何东西都不会被外界篡改。

例如，如果您想要找到一个存储访问令牌的位置，以便当令牌在用户会话中途 30 分钟内过期时，您可以刷新该令牌并重新设置它以供将来使用:

```
const Api = function(params) {
  const _store_ = {
    accessToken: null,
  }

  return {
    getAccessToken() {
      return _store.accessToken
    },
    async login() {
      try {
        const response = await axios.post(
          'https://something.com/v1/auth',
          params,
        )
        return response.data
      } catch (error) {
        throw error
      }
    },
    async refreshToken() {
      try {
        const response = await axios.post(
          'https://something.com/v1/access_token/',
          params,
        )
        const { token } = response.data
        _store.accessToken = token
        return token
      } catch (error) {
        throw error
      }
    },
    setAccessToken(token) {
      if (token === undefined) {
        throw new Error('token is undefined')
      }
      _store.accessToken = token
    },
    // ...other methods
  }
}

const api = Api({
  username: 'bob',
  password: 'the_builder123',
})

api
  .refreshToken())
  .then((token) => console.log(token))
  .catch(console.error) 
```

Enter fullscreen mode Exit fullscreen mode

## 6。它给你创造新版本的能力

假设你决定开发一个 RPG 游戏交给你 4 岁的侄子，试图让他不要每天烦你。在这个游戏中你决定要创造一批勇士:

```
const characters = []

const Warrior = function createWarrior(name) {
  this.name = name
  this.hp = 100
  this.mp = 100
  this.defence = 60

  return {
    // Slash the enemy, decreasing their hp down by 35
    slash(target) {
      target.hp -= 35
      return target
    },
    // Increases the warrior's defence by 100 for 40 seconds.
    // Each second will decrement their defence by 1 as the time runs out.
    battleCry() {
      this.defence += 100
      this.battleCryInterval = setInterval(() => {
        this.defence -= 1
      }, 1000)
      this.battleCryTimeout = setTimeout(() => {
        this.defence = 60
      }, 40000)
    },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以创建一个*高阶函数*，它首先接受一个创建战士的名字列表，然后返回一个帮助创建战士的新函数:

```
const massCreateWarriors = function(names) {
  return (onCreate) => {
    const warriors = []
    names.forEach((name) => {
      const newWarrior = new Warrior(name)
      if (onCreate) onCreate(newWarrior)
      warriors.push(newWarrior)
    })
    return warriors
  }
}

const prepareWarriors = massCreateWarriors(['bob', 'joe', 'sally', 'woodie'])

const newWarriors = prepareWarriors(function onCreate(newWarrior) {
  if (newWarrior.name === 'sally') {
    newWarrior.theme = 'red'
  }
  characters.push(newWarrior)
}) 
```

Enter fullscreen mode Exit fullscreen mode

创建一个助手函数来为你创建大量的字符，而不是每次都为不同的字符进行硬编码，这不是很好吗？

## 7。它可以帮助你与老板和同事建立健康的关系

[![higher order functions can help you develop a healthy relationship with your boss and co workers](img/b53adb6bc8f7833af8a0469731c35741.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aeoJOXZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://jsmanifest.s3-us-west-1.amazonaws.com/posts/7-ways-higher-order-functions-can-improve-your-life/5.jpg)

高阶函数可以帮助解决许多问题。也就是说，高阶函数可以带来许多有用的好处，比如减少代码大小和可重用性，这样使用时，你会减少给周围人带来压力的可能性。你的同事会喜欢你不仅仅是一个开发人员，而是一个努力做大事的开发人员。此外，您还*增加了*新开发人员学习您的代码以帮助他们提高技能的可能性。这让你对团队、老板和整个公司都非常有价值。

...当你的同事高兴时，他们很可能会带着免费的甜甜圈进来。

## 结论

帖子到此结束！我希望你喜欢它，并期待我在未来的更多职位！在[媒体](https://medium.com/@jsmanifest)上找到我

星期五快乐！**