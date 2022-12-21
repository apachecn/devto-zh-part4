# 带有 React 挂钩的动态页面标题

> 原文：<https://dev.to/rbreahna/dynamic-page-title-with-react-hooks-47l3>

这篇文章是关于[chroniker.co](https://chroniker.co/)的来龙去脉系列文章的一部分

没有详细说明 chroniker.co 实际上是为了什么。我将尝试说明我是如何以动态标题的形式向浏览器选项卡添加一些非常小但有用的功能的。

当站点选项卡不活动时，实际上很难理解时间是否在流逝。我想要某种活动指示器，只需要用户看一眼，不需要更多的交互。

鉴于浏览器标签总是可见的，合理的解决方案是在标题中添加一些东西。

从上下文来看，它必须是⌛表情符号。

我在 state 的某个地方保存了一个标志，告诉我应用程序是否正在运行，所以剩下的就是当标志为 1 时将表情符号添加到标题的开头，当标志为 0 时将其删除。我想实现的另一件事是，如果用户导航到一个不同的页面，在那里它不再相关，就删除它。

chroniker.co 完全是用 react 钩子编写的(第三方包中有一些例外)。自然，我也必须将这个特性放在一个钩子中。

下面是结果:

```
import { useEffect, useState } from 'react';

const useTitleActivity = (isActive = 1, sequenceActive = '\u23F3', sequenceInactive = '') => {
  const [title, setTitle] = useState(document.title);

  //  Provides consistent string across entire hook
  function getInsertedSequence(sequence) {
    return sequence === '' ? '' : `${sequence} `;
  }

  function cleanTitle(currentTitle) {
    return currentTitle
      .replace(new RegExp(`${getInsertedSequence(sequenceActive)}|${getInsertedSequence(sequenceInactive)}`, 'g'), '');
  }

  useEffect(() => {
    document.title = title;
  }, [title]);

  useEffect(() => {
    let newTitle = cleanTitle(title);
    if (isActive === 1) {
      newTitle = `${getInsertedSequence(sequenceActive)}${newTitle}`;
    } else if (isActive === 0) {
      newTitle = `${getInsertedSequence(sequenceInactive)}${newTitle}`;
    }

    setTitle(newTitle);

    return () => {
      document.title = cleanTitle(title);
    };
  }, [isActive, sequenceActive, sequenceInactive]);
};

export default useTitleActivity; 
```

Enter fullscreen mode Exit fullscreen mode

这个钩子叫做 useTitleActivity，我是这样使用它的:

```
 useTitleActivity(state.isActive); 
```

Enter fullscreen mode Exit fullscreen mode

它的工作原理是首先设置当前标题的本地状态:

```
const [title, setTitle] = useState(document.title); 
```

Enter fullscreen mode Exit fullscreen mode

以下:

```
function getInsertedSequence(sequence) {
    return sequence === '' ? '' : `${sequence} `;
  } 
```

Enter fullscreen mode Exit fullscreen mode

只是一个本地助手，它确保我插入的不是我真正需要的就是一个空字符串。事后看来，这可能有些矫枉过正

继续前进...

```
function cleanTitle(currentTitle) {
    return currentTitle
      .replace(new RegExp(`${getInsertedSequence(sequenceActive)}|${getInsertedSequence(sequenceInactive)}`, 'g'), '');
  } 
```

Enter fullscreen mode Exit fullscreen mode

这个函数的目标是从初始标题中移除任何外来元素，除此之外别无其他。

```
useEffect(() => {
    document.title = title;
  }, [title]); 
```

Enter fullscreen mode Exit fullscreen mode

这种效果将在每次标题改变时用钩子的本地状态中的标题来设置 document.title。

最后:

```
 useEffect(() => {
    let newTitle = cleanTitle(title);
    if (isActive === 1) {
      newTitle = `${getInsertedSequence(sequenceActive)}${newTitle}`;
    } else if (isActive === 0) {
      newTitle = `${getInsertedSequence(sequenceInactive)}${newTitle}`;
    }

    setTitle(newTitle);

    return () => {
      document.title = cleanTitle(title);
    };
  }, [isActive, sequenceActive, sequenceInactive]);
}; 
```

Enter fullscreen mode Exit fullscreen mode

我发现有时候从钩子的末端看会更容易，因为你可以清楚地看到它们的依赖关系。

在这种情况下，每当**是活动的**、**序列活动的**或**序列活动的**发生变化时，需要发生一些事情。

首先我们得到了清白的头衔。

然后，我们根据活动标志，给它加上“活动”或“非活动”表情符号的前缀。

然后，我们将结果标题设置在钩子的状态中，这将确保触发上面的效果，并将本地状态与页面标题同步。

最后，如果用户离开了这个钩子所在的页面，我们会将标题恢复到原来的形式。

就是这样。这是一个小功能，但对最终用户非常有用。随时建议重写/替代，将改善上述代码。

如果你曾经需要一个 pwa 来进行自由简单的时间追踪，那么你已经知道在哪里可以找到一个