# React ui-kit 与 TS & styled-components:添加主题-第二部分

> 原文：<https://dev.to/canastro/react-ui-kit-with-ts-styled-components-adding-a-theme-part-ii-of-ii-2745>

这是一系列关于如何使用类型脚本、故事书和样式组件创建可重用 ui 组件模块的帖子:

*   [用 TS &风格的组件反应 ui-kit:引导项目](https://dev.to/canastro/react-ui-kit-with-ts-styled-components-storybook-part-i-of-ii-3jbd)
*   [反应 ui-kit 与 TS &风格-组件:添加一个主题](https://dev.to/canastro/react-ui-kit-with-ts-styled-components-adding-a-theme-part-ii-of-ii-2745)

很可能你的组件会有一些共同的 UI 特征，比如颜色、填充、字体等等。有一个主题来规范这一点会很方便。因此，每当设计团队决定要改变原色时，您就不必进行查找和替换...你只需要更新你的主题。🎉

在本教程中，我们只是要为主题创建一个调色板，但我们在一个单独的文件中做它，以促进一些关注点的分离，并试图成为未来的证据。

**src/theme/调色板. ts** :

```
const palette = {
  white: '#fff',
  grey: '#f7f9fa',
  black: '#222',
  primary: '#6d30e7',
  secondary: '#dfdded'
};

export default palette; 
```

**src/theme/index . ts**:T2

```
import palette from './palette';

export default { palette }; 
```

但是如果你的主题是由静态对象组成的，用户将没有机会调整组件来匹配应用程序中的细微变化...让我们稍微改变一下方法。将主题和调色板更改为允许用户提供一些覆盖的功能:

**src/theme/调色板. ts** :

```
export interface Palette {
  white: string;
  grey: string;
  black: string;
  primary: string;
  secondary: string;
};

export type PaletteInput = {
  readonly [K in keyof Palette]+?: Palette[K];
}

const createPalette = (palette: PaletteInput): Palette => {
  const {
    white = '#fff',
    grey = '#f7f9fa',
    black = '#222',
    primary = '#6d30e7',
    secondary = '#dfdded'
  } = palette;

  return {
    white,
    grey,
    black,
    primary,
    secondary
  };
};

export default createPalette; 
```

**Typescript 提示:**我们不希望在创建新主题时强制发送完整的主题对象，因此我们不能使输入类型与输出类型相同，输入类型需要所有键都是可选的。我们可以从一个接口创建一个类型，使用 keyof 迭代所有的键，然后应用一个修饰符使给定的键可选，查看 [Typescript Mapped Types 文档](https://www.typescriptlang.org/docs/handbook/advanced-types.html)

**src/theme/index . ts**:T2

```
import createPalette, { Palette } from './create-palette';
import createTypography, { Typography } from './create-typography';
import spacing, { Spacing } from './spacing';

export interface Theme {
  palette: Palette,
  typography: Typography,
  spacing: Spacing
};

export interface ThemeInput {
  palette?: PaletteInput,
  typography?: TypographyInput
}

const createTheme = (options: ThemeInput): Theme => {
  const {
    palette: paletteInput = {},
    typography: typographyInput = {}
  } = options || {};

  const palette = createPalette(paletteInput)
  const typography = createTypography(palette, typographyInput);

  return {
    palette,    // our color palette
    spacing,    // a spacing unit to be used on paddings / margins / etc.
    typography  // fonts and fontSizes theme
  };
};

export default createTheme; 
```

为了使用这个主题，我们将使用 styled-components[theme provider](https://www.styled-components.com/docs/advanced#theming)，用法如下:

```
const theme = createTheme({
  palette: {
    primary: 'blue',
    secondary: 'red'
  }
});

const MyPage = () => (
  <ThemeProvider theme={theme}>
    <StyledButton>Hello World!</StyledButton>
  </ThemeProvider> ); 
```

我们现在应该更新 StyleButton 来使用我们的主题变量。如果你回到你的 **styled-button.jsx** 你可以改变你的样式组件来使用主题颜色而不是硬编码的颜色。要访问主题，在你的模板文本中添加一个函数，并从道具中获取主题。每个样式组件都可以作为道具使用主题，如果你想在样式组件之外使用主题，你可以[使用`withTheme` HoC](https://www.styled-components.com/docs/advanced#getting-the-theme-without-styled-components) 。

```
const RootStyledButton = styled.button`
  cursor: pointer;
  padding: 0px ${props => props.theme.spacing.unit * 2};
  height: 49px;
  border-radius: 2px;
  border: 2px solid ${props => props.theme.palette.white};
  display: inline-flex;
  background-color: ${props =>
    props.disabled ? props.theme.palette.secondary : props.theme.palette.primary};
`;

const ButtonSpan = styled.span`
  margin: auto;
  font-size: ${props => props.theme.typography.fontSizeButton};
  font-weight: ${props => props.theme.typography.fontWeightBold};
  text-align: center;
  color: ${props => props.theme.palette.white};
  text-transform: uppercase;
`; 
```

现在，我们需要更新我们的故事，也包括主题。故事书有一个叫做`addDecorator`的功能，它允许你定义一个将用于你所有故事的高级组件，导入风格化组件主题提供者和你的主题构建者，并将你的装饰者添加到`.storybook/config.js` :

```
addDecorator(renderStory => (
    <ThemeProvider theme={createTheme()}>
        {renderStory()}
    </ThemeProvider> )); 
```

另外，我们不希望我们的 ThemeProvider 文档污染我们的故事，所以我们需要更新我们的`.storybook/config.js`文件来忽略 propTypes 表的 info-addon 中的 ThemeProvider。(还没想好如何在故事来源上不显示装饰者，有一个关于它的 [github 问题](https://github.com/storybooks/storybook/issues/4801)

将您的 withInfo 配置更新如下:

```
import { ThemeProvider } from 'styled-components';

addDecorator(
  withInfo({
    header: true,
    inline: true,
    propTablesExclude: [ThemeProvider] // do not display propTable for HOC
  })
); 
```

更新你的 **index.ts** 来导出你的主题相关文件:

```
export { ThemeProvider } from 'styled-components';
export { default as createTheme } from './theme';
export { default as StyledButton } from './styled-button/styled-button'; 
```

我们从 styled-components 中公开 ThemeProvider，这样我们就不会为了使用我们的库而使用 install styled-components。(如果客户也使用样式化组件，请确保它正常工作)。

## 更新您的示例应用程序

1.  将我们的 ThemeProvider 添加到示例应用程序的根目录:

```
import React from 'react';
import { ThemeProvider, createTheme } from 'bob-ross-kit';

const theme = createTheme({ 
  palette: { primary: 'blue', secondary: 'red' }  
});

const App = () => (
    <ThemeProvider theme={theme}>
    ...
    </ThemeProvider> ); 
```

1.  现在在提供者中使用您的组件，他们将可以访问主题道具:

```
import React from 'react';
import { StyledButton } from 'bob-ross-kit';

const Comp = () => (
    ...
    <StyledButton onClick={() => console.log('clicked')}>Button</StyledButton>
    <StyledButtton disabled>My Button</StyledButton>
    ...
) 
```

## 结论

到本文结束时，您的 UI 工具包应该已经可以进行主题化并提供默认主题了。

在 [github](https://github.com/canastro/bob-ross-kit/tree/blog-stuff) 上查看 **bob-ross-kit** 的源代码

## 学分

主题可配置的方式很大程度上基于 Material-UI 使用 JSS 处理主题的方式。

如果你发现任何错误，无论是我糟糕的英语还是任何技术细节，请不要害羞，在推特上告诉我。我会努力不断改进这篇博文 :simple_smile: