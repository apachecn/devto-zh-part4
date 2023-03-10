# 使用样式化组件重用 CSS

> 原文：<https://dev.to/stephencweiss/reusing-css-with-styled-components-3okn>

样式化组件在划分样式并使其接近使用它的组件方面非常好。

当我们只有一个主 CSS 文件时，我不在“美好的旧时光”中，但我可以想象这将是多么具有挑战性。

这就是为什么当我遇到一个例子，我想以一种面向 sudo 对象的方式在不同的组件之间共享样式时，我觉得它很有趣。

在这个例子中，我有两个组件。它们都是输入，但是它们建立在不同的基础上(为了这个例子，我们认为这是理所当然的)。然而，在第一个建筑的基础上，第二个建筑的造型几乎完全相同。

当我忘记更新一个样式化组件时，我如何重用样式化组件而不重复代码和潜在的分歧？

下面是一个简单的例子，演示了我如何在`styled-components`中使用 css helper 函数来毫不费力地重用我的 css *，而没有*复制代码。 <sup>1</sup>

我的原始样式输入:

```
import styled from ‘styled-components’;

export const DefaultInput = styled.input`
  border: 1px solid ${({error})=>( error ? `red` : `grey` )};
  border-radius: 4px;
  outline: none;
  padding: 0.5em;
`; 
```

因为我的第二个组件实际上*不是*将会是`input`(它将会是`button`)，不幸的是我不能只做:

```
import styled from ‘styled-components’;

export const SecondComponent = styled(DefaultInput)`
  /* make changes as needed*/
`; 
```

这将是理想的，因为我可以简单地扩展我最初定义的 CSS。

从样式组件进入 CSS 帮助函数！

```
import styled, { css } from ‘styled-components’;

const baseInputStyles = css`
  border: 1px solid ${({error})=>( error ? `red` : `grey` )};
  border-radius: 4px;
  outline: none;
  padding: 0.5em;
`;

export const DefaultInput = styled.input` ${baseInputStyles} `;

export const SecondComponent = styled.button` ${baseInputStyles} /* make changes as needed*/
`; 
```

## 资源

*   <sup>1</sup> [api: css |样式化组件](https://www.styled-components.com/docs/api#css)