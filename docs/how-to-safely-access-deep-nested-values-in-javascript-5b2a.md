# 🛡️如何安全地访问 javascript 中深度嵌套的值？

> 原文：<https://dev.to/tbetous/how-to-safely-access-deep-nested-values-in-javascript-5b2a>

我想分享我今天遇到的一个问题以及我是如何解决的。它是关于在 javascript 中访问深度嵌套值的方法。这里有一个简单的例子:

```
const options = {
    notification: {
        enablePrivateMessage: true,
        enableCommentResponse: false
    }
}

const enablePrivateMessage = options.notification.enablePrivateMessage
const enableCommentResponse = options.notification.enableCommentResponse 
```

Enter fullscreen mode Exit fullscreen mode

## 有什么问题吗？

这很好，但是如果`options`或`notification`是`undefined`会发生什么？

```
const options = undefined
const enablePrivateMessage = options.notification.enablePrivateMessage
// Uncaught TypeError: Cannot read property 'notification' of undefined

const options = {}
const enablePrivateMessage = options.notification.enablePrivateMessage
// Uncaught TypeError: Cannot read property 'enablePrivateMessage' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

是啊，真尴尬！如果不确定是否设置了中间值，那么访问深度嵌套的值是不安全的。在前面的例子中，我试图访问`notification`的属性`enablePrivateMessage`，但不幸的是`options`中没有设置`notification`，因此等于`undefined`。错误信息告诉我，我试图从某个东西`undefined`访问`enablePrivateMessage`属性。

## 如何解决？

这个问题的第一个解决方案是一次浏览一个嵌套属性，并在访问下一个嵌套值之前检查它们的值是`null`还是`undefined`。

```
const options = {}
const enablePrivateMessage = options && options.notification && options.notification.enablePrivateMessage 
// enablePrivateMessage == undefined

// alternative way
const enablePrivateMessage = !options ? 
    undefined : !options.notification ? 
        undefined : options.notification.enablePrivateMessage 
// enablePrivateMessage == undefined 
```

Enter fullscreen mode Exit fullscreen mode

虽然这适用于简单的情况，但是如果你的对象很深，那么写起来会很痛苦。代码会很长，难以阅读。幸运的是，和 [lodash `get` function](https://github.com/lodash/lodash/blob/master/get.js) 一样，我们可以设计一个函数来安全地访问属性。就是这里:

```
export const getPropValue = (object, path = '') =>
    path.split('.')
        .reduce((o, x) => o == undefined ? o : o[x]
        , object)

const options = {}
const enablePrivateMessage = getPropValue(options, 'notification.enablePrivateMessage')
// enablePrivateMessage == undefined 
```

Enter fullscreen mode Exit fullscreen mode

它是如何工作的？`getPropValue`是一个带两个参数的函数。第一个是要查询的`object`,第二个是我们希望找到的嵌套道具的`path`。当执行函数时，我们在一个数组中分割路径，以便浏览所有嵌套的属性。

```
// Example
// path = 'notification.enablePrivateMessage'
'notification.enablePrivateMessage'.split('.')
// ['notification', 'enablePrivateMessage'] 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们从数组中执行一个 [`reduce`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Array/reduce) 函数，该函数带有一个最初用要查询对象设置的聚集器。`reduce`函数浏览路径中的所有属性，如果其中一个有未定义的值，那么结果将是`undefined`。否则，返回嵌套属性的值。

```
// Example 1 : All properties are defined in the path
// object = {notification: {enablePrivateMessage: true}}
// path = 'notification.enablePrivateMessage'
['notification', 'enablePrivateMessage'].reduce((o, x) => {
    console.log(o, x)
    return o == undefined ? o : o[x]
}, {notification: {enablePrivateMessage: true}})
// {notification: {enablePrivateMessage: true}} 'notification'
// {enablePrivateMessage: true} 'enablePrivateMessage'
// true

// Example 2 : notification is undefined
// object = {}
// path = 'notification.enablePrivateMessage'
['notification', 'enablePrivateMessage'].reduce((o, x) => {
    console.log(o, x)
    return o == undefined ? o : o[x]
}, {})
// {} 'notification'
// undefined 
```

Enter fullscreen mode Exit fullscreen mode

## ES6 析构是怎么回事？

嗯，那很好！但是从 ES6 开始，Javascript 中提供了析构特性。这个特性真的很好，允许开发人员用对象的嵌套属性轻松地声明和设置变量。

```
const options = {
    notification: {
        enablePrivateMessage: true,
        enableCommentResponse: false
    }
}

const {notification: {enablePrivateMessage, enableCommentResponse}} = options

console.log(enablePrivateMessage)
// true

console.log(enableCommentResponse)
// false 
```

Enter fullscreen mode Exit fullscreen mode

然而，在这种情况下我不能使用我的`getPropValue`函数。如果`options`是`undefined`，那么之前的析构将导致一个错误。

```
const options = undefined
const {notification: {enablePrivateMessage, enableCommentResponse}} = options
// Uncaught TypeError: Cannot destructure property `notification` of 'undefined' or 'null'. 
```

Enter fullscreen mode Exit fullscreen mode

如果`options`是`undefined`，保护我们的代码不出错的一个简单方法是设置一个回退值。但这还不够。

```
const options = undefined
const {notification: {enablePrivateMessage, enableCommentResponse}} = options || {}
// Uncaught TypeError: Cannot destructure property `enablePrivateMessage` of 'undefined' or 'null' 
```

Enter fullscreen mode Exit fullscreen mode

一旦你这样做了，你需要为你所有的嵌套属性设置默认值，然后才能得到你想要的属性。

```
const options = undefined
let {notification: {enablePrivateMessage, enableCommentResponse} = {}} = options || {}

console.log(enablePrivateMessage)
// undefined

console.log(enableCommentResponse)
// undefined 
```

Enter fullscreen mode Exit fullscreen mode

如果你想要的属性很有深度，这就没有`getPropValue`函数方便了。在这种情况下，您应该考虑到析构可能不是访问您的属性的最佳方式，因为您的指令太长而不可读。在这种情况下，我建议使用`getPropValue`功能一次访问一个属性。

## Lodash 可以帮到你！

你要知道`getPropValue`是我为这个帖子设计的一个功能。在现实生活中，我使用 [lodash `get`函数](https://lodash.com/docs/4.17.11#get)。这在很多情况下确实是有用的，而且很有可能奏效`getPropValue`。如果您的路径在获取目标属性之前到达了一个未定义的值，那么有一个额外的参数允许您设置一个回退值。

```
import { get } from 'lodash'

const options = {
    notification: {
        enablePrivateMessage: true,
        enableCommentResponse: false
    }
}

// Example 1 : Simple case
get(options, 'notification.enablePrivateMessage')
// true

// Example 2 : Error case with 'fallback' as fallback value
get(options, 'toto.tata', 'fallback')
// 'fallback' 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！您已经知道了在 javascript 中安全访问深层嵌套值所需要知道的一切！希望你喜欢我在[dev to](https://dev.to)的第一篇帖子！