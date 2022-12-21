# 使用浏览器自定义事件

> 原文：<https://dev.to/dropconfig/using-browser-custom-events-1do9>

很多时候，当你写东西的时候，你可能想要对页面上的某些事件做出反应。

我们一直在用像`click`或`keydown`这样的内置函数这样做

但是我们也可以自定义事件，让浏览器为我们处理所有的工作！由于它是 DOM API 的一部分，我们无需安装另一个库或推出我们自己的错误版本就可以获得免费的事件代码。

[`CustomEvent`](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent/CustomEvent) 就是我们将要用到的。我们将对其进行一些包装，使其使用起来更加整洁。

## 制作自定义事件

很简单

```
const event = new CustomEvent("myevent", {details: {some: "data"}});

document.dispatchEvent(event); 
```

注意，我们必须将自己的定制数据放入事件的`details`键中。这只是他们工作方式的一个怪癖。

## 监听自定义事件

```
function eventHandler(event){
    const data = event.details.data;
    console.lo(data);
}
document.addEventListener("myevent", eventHandler) 
```

## 停止监听

```
document.removeEventListener("myevent", eventHandler); 
```

很简单的东西。

最棒的是，我们还可以在元素上调度事件，这样它就不会冒泡到 dom。让我们的代码事件更加模块化。只需用你拥有的另一个元素替换`document`。

## 一点包装纸。

因为每次你想使用一个事件的时候都要写这些东西，这有点麻烦。让我们稍微包装一下。

```
function publish(eventName, data){
    const event = new CustomEvent(eventName, {details: data});
    document.dispatchEvent(event);
}
const events = [];
function subscribe(eventName, cb){
    const wrapper =  function(event){
        cb(event.details);
    }
    document.addEventListener(eventName, wrapper);

    events.push({
        cb, wrapper, eventName
    })
}

function unsubscribe(eventName, cb){
    events.forEach((event)=>{
        if(event.eventName === eventName && cb === event.cb){
            document.removeEventListener(eventName, event.wrapper);
        }
    })

}

export {subscribe, unsubscribe, publish}; 
```

### 用法

```
function eventHandler(data){
    console.log(data);
}
subscribe("myEvent", eventHandler))
publish("myEvent", {some: "data"});
unsubscribe("myEvent", eventHandler); 
```

就这样

如果你喜欢我的东西，请查看我的网站[https://dropconfig.com](https://dropconfig.com)