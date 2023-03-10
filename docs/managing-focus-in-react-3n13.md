# 管理 React 中的焦点

> 原文：<https://dev.to/westbrookc16/managing-focus-in-react-3n13>

有时候，您可能希望将焦点设置在 react 组件中的特定元素上，比如组件第一次挂载时，或者表单提交后。这在 react 中相对容易实现，今天我将向您展示如何实现。

# 参考 api

在 react 中，可以使用 ref 属性轻松访问 dom 元素。考虑下面这个简单的 react 组件。

```
import React, { useRef } from 'react';
const HelloWorld = () => {
    const mainRef = useRef(null);
    return (
        <div>
            <h1 ref={mainRef} tabIndex="-1">
                Hellow World
            </h1>
            Hello World
        </div>
    );
};

export default HelloWorld; 
```

注意，在上面的组件中，我创建了一个名为 mainRef 的变量，尽管它可以被命名为任何名称。它只需用
进行初始化

```
useRef(null); 
```

然后，当该变量被创建时，可以使用 hte ref 属性在组件的返回标记中的任何地方引用它。这里我将它设置在 h1 元素上，但它也可以设置为任何值。表单域、div、按钮等。我在 h1 上包含了一个-1 的 tabIndex，这样元素就可以聚焦了，我们将在下一节中处理这个问题。

# 用参考电流设置焦点

现在我们可以通过使用一个效果将焦点设置到一个 react ref，就像这样:

```
 useEffect(() => {
        mainRef.current.focus();
    }, [mainRef]);
    ```

This effect is pretty self-explanatory. it uses mainRef.current to get the dom element and then calls focus() on it. If it were an input, you could have set the value with mainREf.current.value="blah"; Anything you can do witha dom element you can do with ref.current.
#making things easier with a custom hook
You can make the task of setting focus on initial component mounting even easier with a custom hook. I created a custom hook that sets focus to a given ref as well as set the document title to a given string. It is below:

```jsx
import { useEffect } from 'react';
const useInitialfocus = (ref, title) => {
    useEffect(() => {
        ref.current.focus();
        document.title = title;
    }, [ref, title]);
};
export default useInitialfocus; 
```

下面是使用 useInitialFocus 钩子的 HelloWorld 组件:

```
import React, { useRef } from 'react';
import useInitialFocus from './hooks/useInitialFocus';
const HelloWorld = () => {
    const mainRef = useRef(null);
    useInitialFocus(mainRef, 'Hello World');

    return (
        <div>
            <h1 ref={mainRef} tabIndex="-1">
                Hellow World
            </h1>
            Hello World
        </div>
    );
};

export default HelloWorld; 
```

确保根据您的文件夹结构等将导入更改为 useInitialfocus。我希望这篇文章能给你一些关于如何在 react 中管理焦点的启发。编码快乐！！！