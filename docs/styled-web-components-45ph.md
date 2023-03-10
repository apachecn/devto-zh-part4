# 样式化的 Web 组件

> 原文：<https://dev.to/alfredosalzillo/styled-web-components-45ph>

【React 的风格组件很牛逼。
为什么我们不能将 web 组件设计成？

有了[伪装](https://github.com/alfredosalzillo/masquerades)我们可以做到这一点。

## 优点

使用样式化的 web 组件:

*   我们可以在 js 文件中编写 CSS，使用标记字符串
*   我们可以基于 props 值编写反应式 CSS
*   不需要考虑唯一的类名或 id
*   我们可以设计本地 web 组件和定制 web 组件的样式
*   生成的 styled-web-components 可以在光影根中使用，而不需要手动管理样式表的采用

## 举例

创建一个有风格的原生按钮:

```
import styled from 'masquerades';

// Create the button
const StyledButton = styled.button`
  background: ${({ disabled }) => (disabled ? 'grey' : '#f1c40f')};
  color: #fff;
  border: 3px solid #fff;
  border-radius: 50px;
  padding: 0.8rem 2rem;
  font: 24px "Margarine", sans-serif;
  outline: none;
  cursor: pointer;
  position: relative;
  transition: 0.2s ease-in-out;
  letter-spacing: 2px; ${({ disabled }) => disabled && styled.css`
    border-radius: 15px;
  `} `
// set up observedAttributes
  .observeAttributes(['disabled']);

// Define the styled button as extension of the native button
customElements.define('styled-button', StyledButton, {
  extends: 'button',
}); 
```

Enter fullscreen mode Exit fullscreen mode

并使用它

```
<button is="styled-button">Styled Button</button>
<button is="styled-button" disabled>Styled Button</button> 
```

Enter fullscreen mode Exit fullscreen mode

## 最后

样式化的组件很棒，也很有用，为什么不把它们和 web 组件一起使用呢？