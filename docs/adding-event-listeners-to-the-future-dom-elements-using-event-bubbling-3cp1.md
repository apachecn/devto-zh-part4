# 使用事件冒泡向未来的 DOM 元素添加事件侦听器

> 原文：<https://dev.to/akhil_001/adding-event-listeners-to-the-future-dom-elements-using-event-bubbling-3cp1>

### 简要介绍:

在我进入细节之前，让我先回答主要的问题，这样你就不用浪费时间阅读整篇文章了

*   这是给谁的？
    *   任何对 DOM API 有初步了解的本地 javascript 开发人员
*   我会学到什么？
    *   您将学习如何处理必须向元素添加事件侦听器的用例，这些元素可能会在未来的工作流中添加到 UI 中。简而言之，你将学习`Event Delegation`

### 问题陈述:

这一切都是从我使用普通 javascript 开发一个简单的 MVC 待办应用开始的。我遇到了一个需求，当用户单击“添加任务按钮”时，需要监听将要添加到 UI 中的任务项的单击事件。

然后我开始思考，如何将事件侦听器添加到将在未来工作流中添加的元素中？

一般来说，可以通过下面的方式将事件监听器添加到 DOM 中已经存在的元素中

```
 let taskDOMElement = document.querySelector('#task');
 taskDOMElement.addEventListener('click',clickHandler,false);
 function clickHandler()
 {
    //handle the event
 } 
```

Enter fullscreen mode Exit fullscreen mode

但是为了将事件侦听器添加到将来要追加到 DOM 的元素中，我们必须使用`Event Bubbling`的概念，其中事件从目标元素冒泡到父元素，调用它们各自的事件处理程序

### 解:

*   首先，我们必须找出 DOM 中最近的非动态元素，它不会在我们的工作流中改变(在我的例子中是 body 元素)

```
let rootElement = document.querySelector('body'); 
```

Enter fullscreen mode Exit fullscreen mode

*   然后将同一个事件侦听器附加到您希望从 future 元素中侦听的那个元素(在我的例子中，它是 click event)

```
let rootElement = document.querySelector('body');
rootElement.addEventListener('click',function(event){},true); 
```

Enter fullscreen mode Exit fullscreen mode

*   现在检查事件目标是否与您的选择器匹配(在我的例子中，它是一个 li 标记)

```
let rootElement = document.querySelector('body');
rootElement.addEventListener('click',function(event){
let targetElement = event.target
let selector = 'li';
if(targetElement.match(selector) {
//logic for handling the click event of li tag
 }
},true); 
```

Enter fullscreen mode Exit fullscreen mode

*   但是更准确地说，我们必须从目标遍历到根元素来捕获事件

```
rootElement.addEventListener('click',function(event){
let targetElement = event.target
let selector = 'li';
while(targetElement != null) {
if(targetElement.match(selector) {
   //logic for handling the click event of li tag
   return;
  }
  targetElement = targetElement.parentElement;
 }
},true); 
```

Enter fullscreen mode Exit fullscreen mode

*   因此，经过一些修改后，这里是处理未来元素的事件侦听器的通用函数

```
addCustomEventListener: function (selector, event, handler) {
        let rootElement = document.querySelector('body');
        //since the root element is set to be body for our current dealings
        rootElement.addEventListener(event, function (evt) {
                var targetElement = evt.target;
                while (targetElement != null) {
                    if (targetElement.matches(selector)) {
                        handler(evt);
                        return;
                    }
                    targetElement = targetElement.parentElement;
                }
            },
            true
        );
    }

//adding the Event Listeners to all the li tasks
addCustomEventListener('li#task','click',taskClickHandler); 
```

Enter fullscreen mode Exit fullscreen mode

*   因此，这就是如何将事件侦听器附加到未来元素，即未来动态添加的元素

### 链接

*   [事件委托](https://javascript.info/event-delegation)
*   [大卫·沃尔什关于赛事授权](https://davidwalsh.name/event-delegate)

### 学分

亚历杭德罗·阿尔瓦雷斯拍摄的封面图片

### 结论:

我希望这篇文章在某个时候对你有用。我很想听到你对这篇文章的反馈，并就此进行更多的讨论