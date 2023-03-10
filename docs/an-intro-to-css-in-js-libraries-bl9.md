# CSS-in-JS 库简介

> 原文：<https://dev.to/darcydev/an-intro-to-css-in-js-libraries-bl9>

许多人受到 Christopher Chedeau 关于脸书在 JS 中使用 CSS 的演讲的启发，许多库已经成为实现这种方法的一种方式。事实上，当你选择一个图书馆时，你可能会被众多的选项所淹没。

每个库的核心是将它们用于现代基于组件的应用程序的能力。本文的目的是提供我最常用的四个库的概要和比较:

*   样式组件
*   情绪
*   魅力
*   阿佛洛狄忒

## 总结

| 图书馆 | 模板文字 | 对象文字 | 自动供应商前缀 | 服务器端渲染 | 需要类名 |
| --- | --- | --- | --- | --- | --- |
| 样式组件 | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![cross](img/1781d49a054581f1eb7fb744c8192f47.png) |
| 情绪 | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![cross](img/1781d49a054581f1eb7fb744c8192f47.png) |
| 魅力 | ![cross](img/1781d49a054581f1eb7fb744c8192f47.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) |
| 阿佛洛狄忒 | ![cross](img/1781d49a054581f1eb7fb744c8192f47.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) | ![tick](img/eb4ade13854616a7901e0eb55a206541.png) |

从上表可以清楚地看出，许多关键特性是所有库共享的。事实上，我经常发现自己基于语法原因选择一个特定的 JSS CSS 库。在撰写本文时，这意味着使用 **styled-components** 来转换先前存在的项目，以便进行 React(以便能够使用模板文字),使用 **Aphrodite** 来转换新项目(在我看来，这样可以更清楚地分离 CSS 和 JS 逻辑)。

### 模板文字与对象文字

在编写时，只有**样式化组件**和**情感**允许您将样式作为模板文字编写。当您转换现有的代码库以进行反应时，这是一个显著的优势，因为它迫使您对 CSS 做很少的修改。

#### 模板文字

```
// Using template literal string in Emotion
const base = css`
  color: hotpink;
`

render(
  <div
    css={css` ${base};
      background-color: #eee;
    `}
  >
    This is hotpink.
  </div> ) 
```

#### 对象文字

```
// using Object Literal styles in Emotion

const Button = styled.button(
  {
    color: 'darkorchid'
  },
  props => ({
    fontSize: props.fontSize
  })
)

render(
  <Button fontSize={16}>
    This is a darkorchid button.
  </Button> ) 
```

### 句法差异

在比较两个库的语法差异时，最好复制相同的元素。出于这些目的，我将创建两个不同颜色的按钮。

#### 样式化-组件

```
import React from 'react';
import styled, { css } from 'styled-components';

const Button = styled.button`
  background: transparent;
  border-radius: 3px;
  border: 2px solid #007bff;
  color: #007bff;
  margin: 0.5em 1em;
  padding: 0.25em 1em; ${props => props.primary && css`
    background: #007bff;
    color: white;
  `} `;

const StyledComponentsLib = () => {
  return (
    <div>
      <Button>Normal Button</Button>
      <Button primary>Primary Button</Button>
    </div>
  );
};

export default StyledComponentsLib; 
```

#### 情感

```
import React from 'react';
import styled from '@emotion/styled';
import { css } from '@emotion/core';

const Button = styled.button`
  background: transparent;
  border-radius: 3px;
  border: 2px solid #007bff;
  color: #007bff;
  margin: 0.5em 1em;
  padding: 0.25em 1em; ${props => props.primary && css`
    background: #007bff;
    color: white;
  `} `;

const EmotionLib = () => {
  return (
    <div>
      <Button>Normal Button</Button>
      <Button primary>Primary Button</Button>
    </div>
  );
};

export default EmotionLib; 
```

#### 魅力

```
import React from 'react';
import { css } from 'glamor';

const Button = (props) => {
  const myButton = css({
    background: 'transparent',
    color: '#007bff',
    borderRadius: '3px',
    border: '2px solid #007bff',
    margin: '0.5em 1em',
    padding: '0.25em 1em'
  });

  return (
    <button className={css(myButton, props.css)}>{ props.children }</button>
  );
};

const GlamorLib = () => {
  const primaryButton = css({
    background: '#007bff',
    color: 'white'
  });

  return (
    <div>
      <Button>Normal Button</Button>
      <Button css={primaryButton}>
        Primary Button
     </Button>
    </div>
  );
};

export default GlamorLib; 
```

#### 阿佛洛狄忒

```
import React from 'react';
import { StyleSheet, css } from 'aphrodite';

const styles = StyleSheet.create({
  myButton: {
    background: 'transparent',
    color: '#007bff',
    borderRadius: '3px',
    border: '2px solid #007bff',
    margin: '0.5em 1em',
    padding: '0.25em 1em'
  },
  primary: {
    background: '#007bff',
    color: 'white'
  }
});

const Button = (props) => {
  const primaryButton = props.primary && styles.primary;

  return (
    <button className={css(styles.myButton, primaryButton)}>
      { props.children }
    </button>
  );
};

const AphroditeLib = () => {
  return (
    <div>
      <Button>Normal Button</Button>
      <Button primary>Primary Button</Button>
    </div>
  );
};

export default AphroditeLib; 
```