# 超级简单的反应挂接/卸载动画

> 原文：<https://dev.to/michalczaplinski/super-easy-react-mount-unmount-animations-with-hooks-4foj>

**原帖**:[https://czaplinski . io/blog/super-easy-animation-with-react-hooks/](https://czaplinski.io/blog/super-easy-animation-with-react-hooks/)(有更好的格式和语法高亮)

web 上动画的一个主要用例是简单地在页面上添加和删除元素。然而，在 react 中这样做可能很麻烦，因为我们不能直接操作 DOM 元素！由于我们让 react 负责渲染，我们被迫以 react 的方式制作动画。面对这一发现，一些开发人员开始怀念以前的 jQuery 时代，在那里你可以做:

```
$("#my-element").fadeIn("slow"); 
```

Enter fullscreen mode Exit fullscreen mode

如果你想知道困难到底是什么，让我用一个简单的例子来说明

```
/* styles.css */

@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// index.js

const App = ({ show = true }) => (
  show 
  ? <div style={{ animation: `fadeIn 1s` }}>HELLO</div>   : null
) 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们用`fadeIn`制作组件的*安装*的动画所需的全部内容，但是没有办法制作*卸载*的动画，因为一旦`show`属性变为 false，我们就从 DOM 中删除了`<div/>`！组件不见了，根本没有办法让它动起来。我们能做些什么呢？🤔

基本上，我们需要告诉 react to:

1.  当`show`属性改变时，不要立即卸载，而是“计划”卸载。
2.  开始卸载动画。
3.  动画一结束，就卸载组件。

我想向您展示使用纯 CSS 和钩子来实现这一点的最简单的方法。当然，对于更高级的用例，有像 [react-spring](https://www.react-spring.io/) 这样优秀的库。

对于不耐烦的人，这里有代码，分为 3 个文件:

```
// index.js

import React, { useState } from "react";
import ReactDOM from "react-dom";

import "./styles.css";
import Fade from "./Fade";

const App = () => {
  const [show, setShow] = useState(false);

  return (
    <div>
      <button onClick={() => setShow(show => !show)}>
        {show ? "hide" : "show"}
      </button>
      <Fade show={show}>
        <div> HELLO </div>
      </Fade>
    </div>
  );
};

const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

```
// Fade.js

import React, { useEffect, useState } from "react";

const Fade = ({ show, children }) => {
  const [shouldRender, setRender] = useState(show);

  useEffect(() => {
    if (show) setRender(true);
  }, [show]);

  const onAnimationEnd = () => {
    if (!show) setRender(false);
  };

  return (
    shouldRender && (
      <div
        style={{ animation: `${show ? "fadeIn" : "fadeOut"} 1s` }}
        onAnimationEnd={onAnimationEnd}
      >
        {children}
      </div>
    )
  );
};

export default Fade; 
```

Enter fullscreen mode Exit fullscreen mode

```
/* styles.css */

@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}

@keyframes fadeOut {
  0% {
    opacity: 1;
  }
  100% {
    opacity: 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们从第一个文件开始，分析一下这里发生了什么。有趣的是:

```
// index.js

const App = () => {
  const [show, setShow] = useState(false);

  return (
    <div>
      <button onClick={() => setShow(show => !show)}>
        {show ? "hide" : "show"}
      </button>
      <Fade show={show}>
        <div> HELLO </div>
      </Fade>
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们简单地传递一个`show`属性来控制是否显示`<Fade />`组件的子组件。该组件中的其余代码只是使用 [useState](https://reactjs.org/docs/hooks-state.html) 钩子来管理隐藏/显示。

`<Fade/>`组件收到 2 个道具:`show`和`children`。我们使用`show`属性的值来初始化`<Fade />`组件的`shouldRender`状态:

```
// Fade.js

const Fade = ({ show, children }) => {
  const [shouldRender, setRender] = useState(show);
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这提供了一种将*动画*与*安装/卸载*分开的方法。

`show`道具控制我们是应用`fadeIn`还是`fadeOut`动画，`shouldRender`状态控制挂载/卸载:

```
// ...
return (
    shouldRender && (
      <div
        style={{ animation: `${show ? "fadeIn" : "fadeOut"} 1s` }}
        onAnimationEnd={onAnimationEnd}
      >
        {children}
      </div>
    )
  );
// ... 
```

Enter fullscreen mode Exit fullscreen mode

您可以回想一下，我们之前的主要问题是 react 会在我们尝试应用动画的同时卸载组件，这会导致组件立即消失。但是现在我们把那两步分开了！

我们只需要一种方法来告诉反应*序列*`fadeOut`动画和卸载，我们就完成了！💪

为此，我们可以使用[on animation 和](https://reactjs.org/docs/events.html#animation-events)事件。当动画结束运行*和*时，组件应该被隐藏(`show === false`)，然后将`shouldRender`设置为假！

```
const onAnimationEnd = () => {
    if (!show) setRender(false);
  }; 
```

Enter fullscreen mode Exit fullscreen mode

整个例子也在 [Codesandbox](https://codesandbox.io/s/react-easy-animation-b658i) 上，你可以在那里玩它！

### 嘿！👋在你走之前！🏃‍♂️

如果你喜欢这篇文章，你可以在 [twitter](https://twitter.com/C_Z_A_P_L_A) 上关注我，了解更多编程内容，或者给我发一封[电子邮件](//mailto:mmczaplinski@gmail.com)🙂

我绝对喜欢评论和反馈！！！✌️