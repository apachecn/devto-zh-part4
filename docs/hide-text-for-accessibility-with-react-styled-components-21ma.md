# 使用 React +样式的组件隐藏文本以便于访问

> 原文：<https://dev.to/jacobmparis/hide-text-for-accessibility-with-react-styled-components-21ma>

我构建这个小组件是为了隐藏在整个屏幕都可见的情况下不重要的文本，但是对于任何使用屏幕阅读器的人来说，隐藏的内容是非常宝贵的。

```
import styled from 'styled-components';

const Hide = styled.span.attrs({
    "aria-hidden": true
})`
    display: none;
`; 
```

Enter fullscreen mode Exit fullscreen mode

所以如果你有一个`Shop Now`按钮，你可以让它更容易操作。

```
Shop <Hide>our {category.name} collection</Hide> now 
```

Enter fullscreen mode Exit fullscreen mode