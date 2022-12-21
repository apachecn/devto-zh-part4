# 单个 eventListener 的多个函数

> 原文：<https://dev.to/weirdmayo/multiple-functions-for-single-eventlistener-1ckg>

所以，最近我遇到了管理我的听力功能的挑战。我的一个组件需要与最初的`window.keyDown`事件直接连接/挂钩。

一开始我有这样的东西:

```
//... other component stuff
if( firstUpdate === 0 ){
window.addEventListener("keydown", function(event){
    //... function stuff
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码只会在模块更新一次(来自 render，Mithril.js)后绑定函数。

问题是，只要这个组件稍后出现，不重新加载页面，它就会再次绑定函数，它要么重写，要么两者都触发。

所以我找到了一个更好的解决方案，把所有的`window.keyDown`事件放在一个模块中，然后导出必须被触发的函数的`array`。

```
// initiating the array
let keyDown = [];
window.addEventListener( "keydown", function(event){
    keyDown.forEach( func => { func(event) } );
}

export { keyDown }; 
```

Enter fullscreen mode Exit fullscreen mode

上面的函数将运行数组中的函数，并将事件数据传递给它们。在文件的最后，我只导出了数组，以便以后由我的组件进行修改。

示例 Mithril.js 组件:

```
import { keyDown } from 'WindowEvents.js'

const Component = {
    oninit(vnode){
        this.open = false;
        this.keyDownFunction = function(e){ this.open = !this.open };
        // add function to the array
        keyDown.push ( this.keyDownFunction )
    },
    onremove(vnode){
        // remove function from the array
        keyDown.splice( keyDown.indexOf(this.keyDownFunction), 1);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到，我可以从数组中删除该函数，以便在以后再次渲染组件时，不会让它触发两次。

如果你有一个 MVC 库，使用窗口事件不是一个好主意，只要使用它们的事件。但在某些情况下，你必须进入窗口，这对我来说是一个解决方案。