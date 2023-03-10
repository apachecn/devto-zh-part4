# 带有 React 钩子的 JavaScript 定时器

> 原文：<https://dev.to/rbreahna/javascript-timer-with-react-hooks-560m>

这篇文章是关于[chroniker.co](https://chroniker.co/)的来龙去脉系列文章的一部分

chroniker.co 的主要焦点是时间跟踪。因此，准确跟踪时间的方法是必要的。这似乎是一个可以用 **setTimeout** 或 **setInterval** 解决的小问题。然而，您很快就会发现，使用这些函数并不那么简单。

随着时间的推移，我在这个网站上用来记录时间的代码发生了很大的变化。我从一个简单的 react 钩子开始，它使用了一个 **setInterval** ，并且工作正常。它并不花哨，而且与秒表相比，它非常准确。但是当你长时间开着它，奇怪的事情就开始发生了。

它变得非常不准确。如果你设置 **setInterval** 每秒触发一次，它绝对不会这么精确。有时候会等，有时候会点。你得到的结果是一个随着每次迭代而增长的错误。有一篇很棒的[文章](https://javascript.info/settimeout-setinterval)详细讨论了这个问题。

另一件要注意的事情是当运行它的浏览器标签不活动/没有焦点时，setInterval 会发生什么。浏览器将把它的资源重新路由到聚焦的选项卡，让 **setInterval** 以很大的延迟运行。即使您将它设置为更频繁地运行，它也会默认为每秒一次。当电脑进入睡眠模式时，也会发生类似的事情。如果你的网站是围绕时间追踪建立的，这是非常不方便的。

底线是:永远不要相信 **setInterval** 会按照你指定的时间间隔运行你的代码。

这个问题的解决方案有多种形式和大小。但是它们都有一个共同点:使用 Date()的绝对值。这样做的目的是节省间隔开始前和执行时的时间。这样你就可以用一个减去另一个，得到实际的间隔。然后，您可以调整间隔或消耗间隔的逻辑，以获得一些准确的读数。

例如:

```
const doSomething = () => {
//your code
}

setInterval(() => {
  doSomething();
}, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码试图每秒运行 **doSomething** ，所以很容易预测它结束后的绝对时间应该是:

```
new Date().getTime() + 1000; 
```

Enter fullscreen mode Exit fullscreen mode

然而在现实中，这个间隔结束的绝对时间总是会因为上述原因而变化，你将会有一个比你的预测更大或更小的新时间。从一个中减去另一个，你将得到你在计时计算中需要考虑的变化。

即使使用这种方法，你的“时钟”也不会像典型的数字秒表那样精确到毫秒，但是它会非常接近。只有在以天为单位计算大的时间间隔时，它才会开始漂移。这种精确度对我来说已经足够了。

当我遇到这样的逻辑时，我总是试图将它们打包到一个 react 钩子中。以下是我想到的:

```
 import { useEffect, useRef, useState } from 'react';

const usePreciseTimer = (handler, periodInMilliseconds, activityFlag) => {
  const [timeDelay, setTimeDelay] = useState(1);
  const savedCallback = useRef();
  const initialTime = useRef();

  useEffect(() => {
    savedCallback.current = handler;
  }, [handler]);

  useEffect(() => {
    if (activityFlag) {
      initialTime.current = new Date().getTime();
      const id = setInterval(() => {
        const currentTime = new Date().getTime();
        const delay = currentTime - initialTime.current;
        initialTime.current = currentTime;
        setTimeDelay(delay / 1000);
        savedCallback.current(timeDelay);
      }, periodInMilliseconds);

      return () => {
        clearInterval(id);
      };
    }
  }, [periodInMilliseconds, activityFlag, timeDelay]);
};

export default usePreciseTimer; 
```

Enter fullscreen mode Exit fullscreen mode

首先让我解释一下钩子的定义:

```
usePreciseTimer = (handler, periodInMilliseconds, activityFlag) 
```

Enter fullscreen mode Exit fullscreen mode

这个钩子希望我们给它传递一个**处理程序**，它可以每隔**周期毫秒**运行一次，并且它应该只在**活动标志**评估为真时运行它。因为我需要显示总的运行时间，所以我还将过期时间传递给处理程序，以便它可以将其添加到当前运行时间中。

```
 useEffect(() => {
    savedCallback.current = handler;
  }, [handler]); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我使用内置的 useRef 钩子来确保在本地处理程序发生变化时更新它。

```
if (activityFlag) {
      initialTime.current = new Date().getTime();
      const id = setInterval(() => {
        const currentTime = new Date().getTime();
        const delay = currentTime - initialTime.current;
        initialTime.current = currentTime;
        setTimeDelay(delay / 1000);
        savedCallback.current(timeDelay);
      }, periodInMilliseconds);

      return () => {
        clearInterval(id);
      };
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是执行主要逻辑的地方。如果 **activityFlag** 为真，我们首先保存当前时间作为参考。一旦**周期毫秒**过去，我们的**设置间隔**应该开始执行。此时，我们进行另一次测量。

我们从真实的绝对时间中减去我们的开始时间，以**延迟**结束。由此产生的延迟是实际经过的时间，它可以大于或小于**周期毫秒**。

然后，我们将延迟时间转换为秒，并将其传递给我们的处理程序，以便告诉它已经过了多少时间，但不是在为下一个周期用新的**当前时间**更新**初始时间**之前。

```
return () => {
        clearInterval(id);
      }; 
```

Enter fullscreen mode Exit fullscreen mode

这里，当钩子被卸载时，我们清理 setInterval。

最后，这里是这个钩子的实际用法:

```
 usePreciseTimer(updateTime, 1000, state.isActive); 
```

Enter fullscreen mode Exit fullscreen mode

你可以在[chroniker.co](https://chroniker.co/)现场检查精确度

我很好奇如何进一步改善这一点，让我知道你的想法:)