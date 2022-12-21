# 带有 React 挂钩的递归 setTimeout

> 原文：<https://dev.to/aaronpowell/recursive-settimeout-with-react-hooks-168n>

我目前正在做一个项目，我需要能够定期轮询 API，我正在使用 React 构建应用程序。我还没有机会使用 [React 钩子](https://reactjs.org/docs/hooks-intro.html),所以我把它作为一个机会来学习一些关于它们的知识，看看如何解决一些我通常用基于类的组件和状态来做的事情，但是用钩子来做。

当我开始的时候，我不断遇到问题，要么是钩子没有更新状态，要么是它在设置计时器时过于激进，以至于我会有几十个同时运行。

在做了一些研究之后，我看到了丹·阿布拉莫夫写的一篇关于如何实现一个钩子来和 [`setInterval`](https://overreacted.io/making-setinterval-declarative-with-react-hooks/) 一起工作的帖子。Dan 很好地解释了需要采取的方法以及特定方法的原因，所以在继续我的帖子之前，请继续阅读它，因为我不会公正地处理它。

最初，我从 Dan 开始使用这个钩子，因为它做了我需要做的事情，不幸的是，我发现我正在访问的 API 具有不一致的响应时间，这导致了并发请求的爆炸，我正在折腾服务器，这不是一个好主意！但这是使用`setInterval`所预期的，它不会等到最后一个响应完成后再启动另一个间隔计时器。相反，我应该以递归的方式使用`setTimeout`，就像这样:

```
const callback = () => {
    console.log("I was called!");
    setTimeout(callback, 1000);
};
callback(); 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，控制台大约每秒被写入一次，但是如果由于某种原因，它花费了比*更长的时间，基本上立即*写入控制台(比方说，您有一个断点)，一个新的计时器不会启动，这意味着只有一个挂起的调用。

这是一种比使用`setInterval`更好的轮询方式。

## 用 React 钩子实现递归`setTimeout`

我用 React 创建了一个[自定义钩子](https://reactjs.org/docs/hooks-custom.html)，就像丹的`useInterval` :

```
import React, { useEffect, useRef } from "react";

function useRecursiveTimeout<T>(
    callback: () => Promise<T> | (() => void),
    delay: number | null
) {
    const savedCallback = useRef(callback);

    // Remember the latest callback.
    useEffect(() => {
        savedCallback.current = callback;
    }, [callback]);

    // Set up the timeout loop.
    useEffect(() => {
        let id: NodeJS.Timeout;
        function tick() {
            const ret = savedCallback.current();

            if (ret instanceof Promise) {
                ret.then(() => {
                    if (delay !== null) {
                        id = setTimeout(tick, delay);
                    }
                });
            } else {
                if (delay !== null) {
                    id = setTimeout(tick, delay);
                }
            }
        }
        if (delay !== null) {
            id = setTimeout(tick, delay);
            return () => id && clearTimeout(id);
        }
    }, [delay]);
}

export default useRecursiveTimeout; 
```

Enter fullscreen mode Exit fullscreen mode

其工作方式是，`tick`函数将调用提供的`callback`(这是递归调用的函数)，然后用`setTimeout`调度它。一旦回调完成，检查返回值看它是否是一个`Promise`，如果是，在调度下一次迭代之前等待`Promise`完成，否则它将调度它。这意味着它可以以同步和异步方式使用:

```
useRecursiveTimeout(() => {
    console.log("I was called recusively, and synchronously");
}, 1000);

useRecursiveTimtoue(async () => {
    await fetch("https://httpstat.us/200");
    console.log("Fetch called!");
}, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个演示:

[https://codesandbox.io/embed/quizzical-sun-r8zkq](https://codesandbox.io/embed/quizzical-sun-r8zkq)

## 结论

钩子很酷，但是将它们与 JavaScript 中的一些 API 集成在一起可能有点棘手，比如使用定时器。希望这个带有`setTimeout`的例子对你有用，你可以随意复制代码或者自己把它放到`npm`上。