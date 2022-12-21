# 使用 JS 库中的 Emotion CSS 设计 react 应用程序的样式——Emotion 简介

> 原文：<https://dev.to/paramharrison/styling-a-react-app-using-emotion-css-in-js-library-an-introduction-to-emotion-11g4>

> 请将此视为一篇独立的文章。如果你已经知道 react 和 react 钩子的基础知识，你就不需要阅读本系列的所有内容。

这篇博文将是对情感的介绍。我们设计一个 react 应用程序，同时学习设计组件的概念。

到目前为止，我们已经创建了一个 react 应用程序，它可以搜索谷歌图书并将其加载到页面上，还可以为每本书创建一个图书详情页面，

你可以在这里看到它的作用，

[https://codesandbox.io/embed/books-detail-page-by2nj](https://codesandbox.io/embed/books-detail-page-by2nj)

#### 安装情感

有两个包，一个是`core`，另一个是为样式组件`@emotion/styled`提供动力的包。我们将看到两者的例子。

```
yarn add @emotion/core
yarn add @emotion/styled 
```

### 情感中的全局风格

在开始任何站点样式之前，我们总是需要一些重置来使浏览器默认样式正常化。我要用`normalize.css`。让我们安装它，

```
yarn add normalize.css 
```

在`create-react-app`中添加外部样式表有几种方法。我将使用一种不寻常的技术来展示如何利用情感来完成。

Emotion 有一个`Global`组件，它将为你的应用程序创建全局样式。让我们把它添加到我们的搜索页面。

```
/* App.js */

// Adding global CSS
import { Global, css } from "@emotion/core";
// importing the text from normalize.css file
import normalize from "normalize.css";

...

const App = () => {
  return (
    <>
      <Global
        styles={css`
          ${normalize}
          body {
            background-color: #fafafa;
          }
        `}
      />
      ...
    </>
  );
};

... 
```

这里我们为 body 元素添加了规格化 css 和背景色。我们可以在情感中添加任何像这样的通用全局 CSS。但是，当您可以随时提取样式和组件并在需要时重用共享组件时，添加通用 CSS 是一种反模式。

### 使用情感风格 react 组件对标题进行样式化

该标题将包含一个标志在左边和一个按钮在右边的搜索输入。

emotion 中样式化组件语法的示例

```
const Header = styled.header`
  background-color: #ffffff;
`;

<Header></Header>; 
```

你也可以给它传递道具

```
const Header = styled.header`
  background-color: ${props => props.bgColor};
`;

<Header bgColor="#FEFEFE"></Header>; 
```

您还可以扩展一个组件并创建新组件。当我们想要为不同的组件共享组件样式时，这将是有用的。

```
const Header = styled.header`
  background-color: #ffffff;
`;

const PageHeader = styled(Header)`
  color: #444;
`;

<PageHeader></PageHeader>; // this will have both background color and color 
```

让我们用所有这些知识来设计我们的标题。

```
// pages/searchPage.js

...
import styled from "@emotion/styled";

import BookSearchForm from "../components/bookSearchForm";
...

// Its very easy to create a syled component using emotion
const Header = styled.header`
  background-color: #ffffff;
`;

const Container = styled.div`
  max-width: 960px;
  padding: 15px;
  margin: 0 auto;
`;

// We can extend a react styled component to create styles on top of it
const HeaderContainer = styled(Container)`
  display: flex;
  align-items: center;
`;

const LogoText = styled.h3`
  margin: 0;
`;

// The styles written inside follows same syntax as CSS, so there is no special learning curve for styled components
const HeaderSearchForm = styled.div`
  margin-left: auto;
`;

const SearchPage = () => {
  ...

  return (
    <>
      <Header>
        <HeaderContainer>
          <LogoText>Bookie</LogoText>
          <HeaderSearchForm>
            <BookSearchForm
              onSubmitHandler={onSubmitHandler}
              onInputChange={onInputChange}
              searchTerm={searchTerm}
              error={error}
            />
          </HeaderSearchForm>
        </HeaderContainer>
      </Header>
      ...
    </>
  );
}; 
```

在搜索页面上，我们已经创建了徽标和标题表单。现在让我们进入标题表单组件和样式的形式。

在此之前，我们将看到一个例子，如何设计 HTML 元素的不同状态(即，悬停、活动、焦点状态)

```
const Input = styled.input`
  min-width: 280px;
  &:focus,
  &:active {
    border-color: #85b7d9;
  }
`; 
```

如何在我们的样式组件中设置 css 类 HTML 元素的样式？其语法与 SCSS 相同

```
const Element = styled.element`
  outline: 0;

  .css-classname {
    background-color: #ffffff;
  }
`; 
```

现在让我们用同样的例子来设计表单元素的样式

```
// components/bookSearchForm.js

...

const Input = styled.input`
  outline: 0;
  padding: 0.6rem 1rem;
  border: 1px solid rgba(34, 36, 38, 0.15);
  border-radius: 3px;
  min-width: 280px;
  &:focus,
  &:active {
    border-color: #85b7d9;
  }
`;

const Button = styled.button`
  background-color: #2185d0;
  color: #ffffff;
  text-shadow: none;
  background-image: none;
  padding: 0.6rem 1.5rem;
  margin-left: 15px;
  border-radius: 3px;
  cursor: pointer;
`;

const BookSearchForm = ({
  onSubmitHandler,
  searchTerm,
  onInputChange,
  error
}) => {
  return (
    <form onSubmit={onSubmitHandler}>
      <Input
        type="search"
        placeholder="Search for books"
        value={searchTerm}
        onChange={onInputChange}
        required
      />
      <Button type="submit">Search</Button>
      ...
    </form>
  );
}; 
```

### 在 JS 中使用 CSS 样式化加载器组件

有几种方法可以对情感中的组件进行样式化。你也可以在 JS 样式中使用 CSS 来表达情感。让我们使用情感库来设计 JS 中 CSS 的 loader 组件。

情感支撑`css`道具。让我们来看一个例子

```
// We have to import jsx, else it won't work on react component
import { css, jsx } from '@emotion/core';

<div
  css={css`
    color: green;
    text-align: center;
    padding: 20px 0;
  `}
>
  {children}
</div>; 
```

在上面的例子中，我们在 JS 中使用简单的 CSS 样式化了一个 div 元素。

如果我们没有导入 jsx，emotion 就不会编译 CSS，这个道具会被当作另一个 react 道具。

让我们使用相同的原则来设计加载器组件的样式

```
/** @jsx jsx */
import { css, jsx } from '@emotion/core';
import React from 'react';

const Loader: React.FunctionComponent<{}> = ({ loading, children }) => {
  return (
    <>
      {loading && (
        <div
          css={css`
            color: green;
            text-align: center;
            padding: 20px 0;
          `}
        >
          {children}
        </div>
      )}
    </>
  );
};

export default Loader; 
```

如果你仔细看，我们在两行代码中做了一些奇怪的语法，

```
/** @jsx jsx */

const Loader: React.FunctionComponent<{}> = { loading, children }; 
```

原因只是一个黑客，这样 linter 就不会对你大喊，`React`已定义但未使用。对于`jsx`也是一样。

除此之外，组件风格是非常直接的，它与样式化组件相似，但唯一的区别是，我们在 JS 方法中使用了内联 CSS。

### 共享错误成分利用情感而不作出反应

我们可以在不使用 react 的情况下创建一个组件并共享它。我们将创建一个错误组件，并将其共享给多个文件。

```
// errorText.js
import styled from '@emotion/styled';

const ErrorText = styled.div`
  color: red;
  text-align: center;
  padding: 20px 0;
`;

export default ErrorText; 
```

就这么简单，我们不需要创建小的 react 组件，只需要有一个风格化的组件。这样会减少一个真实 app 中大量的功能组件需求。

响应风格再次与 SCSS 相同，我们可以添加媒体查询到任何组件。让我们开始让标题响应。

```
// pages/searchPage.js

const HeaderContainer = styled(Container)`
  display: flex;
  align-items: center;
  @media (max-width: 778px) {
    flex-direction: column;
    align-items: flex-start;
  }
`;

// components/bookSearchForm.js
const Input = styled.input`
  ... @media (max-width: 778px) {
    margin-top: 10px;
  }
`;

const Button = styled.button`
  ...border-radius: 3px;
  cursor: pointer;

  @media (max-width: 778px) {
    margin-left: 0;
    margin-top: 10px;
  }
`; 
```

添加媒体查询就是这么简单。您可以向任何样式的组件添加媒体查询，并使其具有响应性。该语法也适用于 JS 语法中的 CSS。

### 共享共同颜色的主题化

现在如何分享共同的颜色，字体，规模等。有办法吗？

是的，有，情感库提供了一个情感主题包。它允许你创建一个 react `context`并在任何你想要的组件上使用主题。让我们看看我们的头组件。

让我们先安装包

```
yarn add emotion-theming 
```

让我们创建一个简单的主题文件并导出它

```
// theme.js

const theme = {
  colors: {
    success: 'green',
    error: 'red',
    white: 'white',
  },
};

export default theme; 
```

现在，我们可以从`emotion-theming`包
中导入主题并使用它

```
// App.js

...
import { ThemeProvider } from "emotion-theming";

import theme from "./components/theme";
...

const App = () => {
  return (
    <ThemeProvider theme={theme}>
      <Global
        styles={css`
          ${normalize}
          body {
            background-color: #fafafa;
          }
        `}
      />
      <Router>
        ...
      </Router>
    </ThemeProvider>
  );
};

export default App; 
```

正如你看到的，我们用主题提供者封装了整个应用程序，并将主题对象传递给主题道具。

现在我们可以在任何样式的组件上使用主题值，让我们看看 header 组件
中的例子

```
// Props will have a theme object and here you can use it in any react component which is enclosed inside the ThemeProvider
export const Header = styled.header`
  background-color: ${props => props.theme.colors.white};
`; 
```

### 共享样式组件

现在我们已经看到了几乎大部分使用情感库的案例。让我们看看共享样式组件的最后一个技巧。我们已经看到了如何用误差分量来完成。现在，我们将分享我们的标题样式，以便搜索页面和详细信息页面标题看起来一样。

```
// components/shared.js

import styled from '@emotion/styled';

export const Header = styled.header`
  background-color: ${props => props.theme.colors.white};
`;

export const Container = styled.div`
  max-width: 960px;
  padding: 15px;
  margin: 0 auto;
`; 
```

现在您可以将共享的样式组件导入到页面

```
// pages/searchPage.js, pages/bookDetailPage.js

import { Container, Header } from '../components/shared'; 
```

[https://codesandbox.io/embed/styling-with-emotion-gfbni](https://codesandbox.io/embed/styling-with-emotion-gfbni)

> 我没有用情感来设计完整的应用程序，只是设计了它的一部分来教授情感。我将很快更新 codesandbox 和 repo 的完整样式，包括卡片和详细信息页面的样式。

在这里查看这部分[的代码库，在这里](https://github.com/learnwithparam/books-series-react-hooks/pull/1/files)可以查阅整个系列的代码库[。](https://github.com/learnwithparam/books-series-react-hooks)

希望这篇文章能帮助你了解情感库及其功能。它是在 react 应用程序上用 JS 实现风格化组件或 CSS 的最佳包之一😎