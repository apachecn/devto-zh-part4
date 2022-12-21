# 创建/调度自定义 JS 事件

> 原文：<https://dev.to/bbenefield89/creating-dispatching-custom-js-events-23mc>

下面是创建和调度自定义事件的快速介绍。很像`click`和`change`，我们也能够创建、让元素监视和触发我们自己的事件。

我们可以访问两个 API:

[事件](https://developer.mozilla.org/en-US/docs/Web/API/Event/Event):

*   少数配置选项
*   一直向前
*   创建新事件

[客户事件](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent):

*   同`Event`
*   灵活的
*   可定制的配置对象

## 简单的自定义事件

```
/**
 * Simple custom event
 */
// Grab a reference to the <p> element
const p = document.querySelector('p')

// create a new event
const pEvent = new Event('pEvent')

// listen for the 'pEvent' on the <p> element
p.addEventListener('pEvent', e => {
  console.log('pEvent')
})

// trigger, or 'dispatch', the 'pEvent' event
p.dispatchEvent(pEvent)  // -> 'pEvent' 
```

## 给自定义事件添加“事件冒泡”

```
/**
 * Custom event with bubbling
 * Read more about 'bubbling' and 'capturing': https://javascript.info/bubbling-and-capturing
 */
const div = document.querySelector('div')
const divEvent = new Event('divEvent', { bubbles: true })

div.addEventListener('divEvent', e => {
  // should read 'Event dispatched from HTMLParagraphElement'
  console.log(`Event dispatched from ${e.target.constructor.name}`)
})

p.addEventListener('pEvent', e => {
  e.target.dispatchEvent(divEvent)
})

// Dispatch the event from the <p> element
// Notice that the event 'bubbles' up to the parent <div> tag
p.dispatchEvent(pEvent) 
```

## 触发内置于 JS 的事件

```
/**
 * Dispatching existing JS events (click, change, etc.)
 */
const button = document.querySelector('button')

button.addEventListener('click', () => {
  console.log('Button Clicked')
})

button.dispatchEvent(new Event('click'))  // Dispatch click using 'dispatchEvent'
button.click()  // Directly calling 'click' also works in this example 
```

[repl.it 实例这些实例的实际应用](https://repl.it/@bbenefield89/CreatingDispatching-Custom-JS-Events)

## 举例说明何时使用

比方说，你不能访问你被分配的项目的代码库。比方说，你只能使用普通的 JavaScript。现在，让我们假设你正在做的项目使用了 HTML/CSS/JS 和 Vue 组件的大杂烩，它们都挂在同一个页面上。

现在在这个场景中，您需要访问由 inputs `change`事件触发的输入方法，类似于`<input class="my_input" :onChange="makeChange" />`事件，但是只有在页面上的按钮被点击之后。嗯，如果我们正在注入一个脚本，而我们没有访问任何组件、方法和属性的权限，这可能是一个非常具有挑战性的任务。

这里快速简单的解决方案是调度 elements `change`事件，类似于上面的例子。

```
const myInput = document.querySelector('.my_input')
const myButton = document.querySelect('.my_button')

myButton.addEventListener('click', () => {
  // dispatches the inputs 'change' event
  myInput.dispatchEvent(new Event('change'))
}) 
```