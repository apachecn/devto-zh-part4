# 您可以在 35 LOC 中创建 React 样式的组件

> 原文：<https://dev.to/dutiyesh/you-can-create-react-styled-components-in-35-loc-22df>

你有没有想过 styled component 在 hood 下是如何工作的？让我们通过建造一个来找出答案。

## 了解风格化组件 API 🕵️‍

```
import styled from 'styled-components'

const Heading = styled.h1`
    color: palevioletred;
`;

const App = () => {
    return <Heading>styled components</Heading>
} 
```

Enter fullscreen mode Exit fullscreen mode

基于样式化组件语法，我们可以说样式化组件返回一个带有名为 methods 的 HTML 标签的`styled`对象，并使用带标签的模板文字。

标记模板就像调用一个函数。

```
greeting('Bruce');
// same as
greeting`Bruce`; 
```

Enter fullscreen mode Exit fullscreen mode

唯一的区别是 Tagged Template 如何处理它的参数，其中第一个参数包含一个字符串值数组。

```
// logging function arguments

logArguments('Bruce');
// -> Bruce

logArguments`Bruce`;
// -> ["Bruce"] 
```

Enter fullscreen mode Exit fullscreen mode

## 样式化组件阶段🌗

我们将样式组件分为两个阶段:

### 阶段 1:创建阶段

在创建阶段，我们调用一个名为 method like - `h1`的样式化组件的标签，它返回一个功能性的 React 组件。

```
// App.js
const Heading = styled.h1`
    color: palevioletred;
`; // ❇️ Creation Phase

// styled-components.js
function h1(styleLiteral) {
    return () => { // ❇️ Function component
        return <h1></h1>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 第二阶段:渲染阶段

在渲染阶段，我们渲染在阶段 1 中创建的功能组件。

```
const Heading = styled.h1`
    color: palevioletred;
`;

const App = () => {
    return <Heading /> // ❇️ Rendering Phase
} 
```

Enter fullscreen mode Exit fullscreen mode

## 向被造型构件的“造型”部分靠近💄

在创建阶段，我们将样式传递给了`h1`函数，但是我们如何在不内联它的情况下将它应用到我们的组件呢？🤔

我们将使用一个类选择器，并分配一个随机的名称。

```
const className = `sc-${Math.random().toString(16).substr(2, 6)}`;
// Generate class names like - sc-79a268, sc-56d898 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将创建一个函数来将样式应用到我们的类中，并通过创建一个新的`style`标签(如果不存在的话)将它添加到我们的页面中。

为了从其他`style`标签中唯一地识别它，我们将分配一个`'sc-style'`的`id`，这样我们就可以使用同一个标签为其他样式化的组件添加样式。

```
function appendStyle(className, style) {
    let styleTag = document.getElementById('sc-style');

    if (!styleTag) {
        styleTag = document.createElement('style')
        styleTag.setAttribute('id', 'sc-style');
        document.getElementsByTagName('head')[0].appendChild(styleTag);
    }

    styleTag.appendChild(document.createTextNode(`.${className} { ${style} }`))
} 
```

Enter fullscreen mode Exit fullscreen mode

结合以上两步，我们得到:

```
function h1(styleLiterals) {
    return () => {
        const className = `sc-${Math.random().toString(16).substr(2, 6)}`;
        appendStyle(className, styleLiterals[0]); // pass first item at index 0

        return <h1 className={className}></h1>
    }
}

function appendStyle(className, style) {
    let styleTag = document.getElementById('sc-style');

    if (!styleTag) {
        styleTag = document.createElement('style')
        styleTag.setAttribute('id', 'sc-style');
        document.getElementsByTagName('head')[0].appendChild(styleTag);
    }

    styleTag.appendChild(document.createTextNode(`.${className} { ${style} }`))
} 
```

Enter fullscreen mode Exit fullscreen mode

## 传递文本以显示我们的样式组件⚛️内部

在渲染阶段，我们可以将数据作为孩子传递给组件，并使用`props.children`在组件内部进行渲染。

```
// App.js
const App = () => {
    return <Heading>styled components</Heading> // Rendering phase
}

// styled-components.js
function h1(styleLiterals) {
    return (props) => { // ❇️ props from parent component
        return <h1>{props.children}</h1>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 我们创建了样式化组件💅

```
// App.js
import styled from 'styled-components';

const Heading = styled.h1`
    color: palevioletred;
`;

const App = () => {
    return <Heading>styled components</Heading>
}

// styled-components.js
function h1(styleLiterals) {
    return (props) => {
        const className = `sc-${Math.random().toString(16).substr(2, 6)}`;
        appendStyle(className, styleLiterals[0]);

        return <h1 className={className}>{props.children}</h1>
    }
}

function appendStyle(className, style) {
    let styleTag = document.getElementById('sc-style');

    if (!styleTag) {
        styleTag = document.createElement('style')
        styleTag.setAttribute('id', 'sc-style');
        document.getElementsByTagName('head')[0].appendChild(styleTag);
    }

    styleTag.appendChild(document.createTextNode(`.${className} { ${style} }`))
}

const styled = {
    h1
}

export default styled; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 用道具定制风格化组件🎨

让我们通过传递一个`color`属性来定制我们的组件，以不同的颜色呈现文本。

```
const Heading = styled.h1`
    color: ${(props) => ${props.color}}; // Apply color from props
`;

const App = () => {
    return <Heading color="palevioletred">styled components</Heading>
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你注意到上面的话，我们在模板中有一个插值。

那么当我们传递带有插值的模板文字时，函数会发生什么呢？

```
const username = 'Bruce';

greeting`Hello ${username}!`;
// -> ["Hello ", "!"] "Bruce" 
```

Enter fullscreen mode Exit fullscreen mode

函数将在这里接收 2 个参数，第一个仍然是一个数组。
第二个参数将是插值内容`'Bruce'`。

## 更新样式化组件以接收插值内容📑

```
function h1(styleLiterals, propInterpolation) {
    return () => {
        return <h1></h1>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

由于插值参数的数量不定，我们将使用 rest 参数将它们表示为一个数组。

我们的函数现在变成:

```
function h1(styleLiterals, ...propsInterpolations) { // ❇️ with rest parameter
    return () => {
        return <h1></h1>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 用插值生成样式👩‍🎨

我们的函数现在接收两个参数- `stringLiterals`和`propsInterpolations`，我们必须合并它们来生成样式。

为此，我们将创建一个函数，遍历两个数组中的每一项，并将它们一个接一个地连接起来。

```
function getStyle(styleLiterals, propsInterpolations, props) {
    return styleLiterals.reduce((style, currentStyleLiteral, index) => {
        let interpolation = propsInterpolations[index] || '';

        if (typeof interpolation === 'function') { // execute functional prop
            interpolation = interpolation(props);
        }

        return `${style}${currentStyleLiteral}${interpolation}`;
    }, '');
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的样式组件中使用`getStyle`函数:

```
function h1(styleLiterals, ...propsInterpolations) {
    return (props) => {
        const className = `sc-${Math.random().toString(16).substr(2, 6)}`;
        const style = getStyle(styleLiterals, propsInterpolations, props); // pass required parameters to generate style
        appendStyle(className, style);

        return <h1 className={className}>{props.children}</h1>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 优化时间⚡️

你有没有注意到当我们用相同的样式渲染两个样式的组件时会发生什么？

```
const Heading = styled.h1`
    color: palevioletred;
`;

const App = () => {
    return (
        <React.Fragment>
            <Heading>styled components</Heading>
            <Heading>styled components</Heading>
        </React.Fragment>
    )
} 
```

Enter fullscreen mode Exit fullscreen mode

2 个类被生成，尽管它们的风格是相同的。
为了减少重复的代码，我们将使用 JavaScript 的`Map`对象来保存我们的样式及其键值对中的类名。

```
function h1(styleLiterals, ...propsInterpolations) {
    const styleMap = new Map(); // maintain a map of `style-className` pairs

    return (props) => {
        let className = '';
        const style = getStyle(styleLiterals, propsInterpolations, props);

        if (!styleMap.has(style)) { // check whether style is already present
            className = `sc-${Math.random().toString(16).substr(2, 6)}`;
            appendStyle(className, style);

            styleMap.set(style, className); // store class for a style in Map
        } else {
            className = styleMap.get(style); // reuse class for a style
        }

        return <h1 className={className}>{props.children}</h1>
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 最终结果✨✨

```
function h1(styleLiterals, ...propsInterpolations) {
    const styleMap = new Map(); // maintain a map of `style-className` pairs

    return (props) => {
        let className = '';
        const style = getStyle(styleLiterals, propsInterpolations, props);

        if (!styleMap.has(style)) { // check whether style is already present
            className = `sc-${Math.random().toString(16).substr(2, 6)}`;
            appendStyle(className, style);

            styleMap.set(style, className); // store class for a style in Map
        } else {
            className = styleMap.get(style); // reuse class for a style
        }

        return <h1 className={className}>{props.children}</h1>
    }
}

function getStyle(styleLiterals, propsInterpolations, props) {
    return styleLiterals.reduce((style, currentStyleLiteral, index) => {
        let interpolation = propsInterpolations[index] || '';

        if (typeof interpolation === 'function') { // execute functional prop
            interpolation = interpolation(props);
        }

        return `${style}${currentStyleLiteral}${interpolation}`;
    }, '');
}

function appendStyle(className, style) {
    let styleTag = document.getElementById('sc-style');

    if (!styleTag) {
        styleTag = document.createElement('style')
        styleTag.setAttribute('id', 'sc-style');
        document.getElementsByTagName('head')[0].appendChild(styleTag);
    }

    styleTag.appendChild(document.createTextNode(`.${className} { ${style} }`))
}

const styled = {
    h1
}

export default styled; 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/k7119](https://codesandbox.io/embed/k7119)