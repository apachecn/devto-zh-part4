# React 自定义挂钩:useEventListener

> 原文：<https://dev.to/adrianbdesigns/custom-react-hooks-useeventlistener-1kp>

这是一个非常简单明了的方法，也是一个非常有用的定制钩子。这个定制的 React 挂钩负责在组件挂载时设置事件监听器，并在组件卸载时删除监听器。

## addEventListener 和 removeEventListener

让我们看一下`addEventListener`和`removeEventListener`函数的定义，这样我们就可以知道我们需要传递给定制钩子什么参数，以使它对所有用例都是灵活的。

```
target.addEventListener(type, listener[, options]);
target.removeEventListener(type, listener[, options]); 
```

Enter fullscreen mode Exit fullscreen mode

从定义中我们可以看到，这两个函数都使用了以下参数:`target`、`type`、`listener`和`options`。现在我们已经建立了输入参数，我们可以继续创建我们的钩子了。

## 自定义挂钩

```
import { useEffect } from 'react';

const useEventListener = (target, type, listener, ...options) => {
  React.useEffect(
    () => {
      target.addEventListener(type, listener, ...options);
      return () => {
        target.removeEventListener(type, listener, ...options);
      };
    },
    [target, type, listener, options]
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`useEffect` React 钩子来处理组件挂载和卸载时发生的事情。如果你想了解更多关于`useEffect` hook 的信息，我在这里已经详细介绍过了:

[![adrianbdesigns image](img/46b991310ae299c1939838211aac489a.png)](/adrianbdesigns) [## 反应:用生命周期方法解释使用效果

### 阿德里安·贝斯 1919 年 8 月 5 日 2 分钟阅读

#react #webdev #javascript](/prototyp/react-useeffect-explained-with-lifecycle-methods-296n)

这就是我们如何在另一个组件中启动钩子(例如，我们正在监听窗口调整大小事件)。

```
useEventListener(window, 'resize', handleWindowResize); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们传递一个`document`或`window`或对象，这个钩子很好用。如果这是我们定制钩子的用例，那么我们可以让它保持原样。然而，如果我们打算传递 refs，这个定制钩子需要做一些小的修改，以便也能处理 refs。

### 添加参照功能

```
import { useEffect } from 'react';

const useEventListener = (target, type, listener, ...options) => {
  React.useEffect(
    () => {
      const targetIsRef = target.hasOwnProperty("current");
      const currentTarget = targetIsRef ? target.current : target;
      if (currentTarget)
        currentTarget.addEventListener(type, listener, ...options);
      return () => {
        if (currentTarget)
          currentTarget.removeEventListener(type, listener, ...options);
      };
    },
    [target, type, listener, options]
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

在`useEffect`钩子中，我们检查传递的目标是 ref 还是`window`或者`document`元素。

这就是我们初始化钩子的方法(我们可以在同一个组件中初始化任意多的监听器):

```
// Window resize listener
useEventListener(window, 'resize', handleWindowResize);

// You can also pass refs
const elementRef = useRef(null);
useEventListener(elementRef, 'mousedown', handleElementClick); 
```

Enter fullscreen mode Exit fullscreen mode

## Codepen 示例

[https://codepen.io/AdrianBece/embed/pozGGdE?height=600&default-tab=result&embed-version=2](https://codepen.io/AdrianBece/embed/pozGGdE?height=600&default-tab=result&embed-version=2)

* * *

感谢你花时间阅读这篇文章。如果你觉得这很有用，请给它一个❤️或🦄，分享评论。