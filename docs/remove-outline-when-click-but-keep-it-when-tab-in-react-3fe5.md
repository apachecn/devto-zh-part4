# 单击时移除轮廓，但当 tab 在 React 中时保留轮廓

> 原文：<https://dev.to/elecweb/remove-outline-when-click-but-keep-it-when-tab-in-react-3fe5>

当我检查 [f8 网站](https://www.f8.com)时，我看到了一些有趣的事情。

当你点击“体验 F8 2019”按钮时，没有我们讨厌的蓝色轮廓(主要是设计师)。但是当我打开网站的时候，我又看到了蓝色的轮廓。

所以我在网上搜索如何做到这一点(也就是谷歌),我发现解释的方法真的很棒。

[https://hacker noon . com/remove-than-ugly-focus-ring-and-keep-it too-6c 8727 fef CD 2](https://hackernoon.com/removing-that-ugly-focus-ring-and-keeping-it-too-6c8727fefcd2)

所以我用它来反应分量。我认为它可能对其他面临这个问题的人有用。

```
import { useEffect } from 'react';

const handleFirstTab = event => {
  if (event.keyCode === 9) {
    document.body.classList.add('user-is-tabbing');
    window.removeEventListener('keydown', handleFirstTab);
  }
};

const RemoveFocusWhenNotTab = () => {
  useEffect(() => {
    window.addEventListener('keydown', handleFirstTab);
    return () => {
      window.removeEventListener('keydown', handleFirstTab);
    };
  });

  return null;
};

export default RemoveFocusWhenNotTab; 
```

你只需要在某个地方渲染它(我更喜欢在 App.js 中渲染)

您需要根据附加到 body 元素的`.user-is-tabbing`类为可聚焦元素(例如，按钮)添加样式(例如，我使用样式化组件)。

```
const GlobalStyled = createGlobalStyle`
    body:not(.user-is-tabbing) button:focus {
        outline: none;
    }
`; 
```

PS。我只是隐藏大纲，直到用户选项卡。之后，当用户点击它，它会再次显示轮廓。有一种方法可以在用户点击时再次隐藏轮廓，但我认为这太过分了。如果你有兴趣这样做，我提到的网站也展示了如何做，你可以很容易地用`RemoveFocusWhenNotTab`组件来适应它