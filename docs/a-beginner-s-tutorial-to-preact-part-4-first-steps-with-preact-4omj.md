# Preact 初学者教程-第 4 部分:Preact 的第一步

> 原文：<https://dev.to/solarliner/a-beginner-s-tutorial-to-preact-part-4-first-steps-with-preact-4omj>

现在，我们已经回顾了在推出 Preact 之前需要了解的所有内容。

# 什么是 Preact？

Preact 是一个 3kb 的库，它允许开发人员使用我们前面看到的相同模式编写应用程序。Preact 提供了自己的`h`函数实现，该函数输出虚拟 DOM(我们前面已经讨论过的“DOM 节点的纯 JavaScript 表示”)，并使用一种高效的呈现算法来尝试对网页进行尽可能少的更改。它还拥有许多我们稍后将会看到的特性。

# 规范的“Hello World”应用程序

## 依赖关系

让我们从创建一个项目开始。我将使用 CodeSandbox(正如我已经为示例所做的那样)，因为它允许我将项目直接嵌入到帖子中，供您运行和调整。所以，要么开始一个新的“香草(+ TS)”项目，要么开始一个新的 npm 项目(`npm init`)。

第二件要做的事，当然是安装 Preact。我们将使用版本 10，在撰写本文时它仍处于发布候选阶段，但它完全重写了 Preact 本身，编写一个具有众所周知的到期日期的教程并不那么有用。如果是测试版，请使用`preact@next`,或者如果你是在版本 10 稳定发布后才阅读这篇文章，请使用`preact`。

接下来，如果您没有使用 CodeSandbox，安装 package 作为开发依赖项:`npm i --save-dev parcel`，并可选地安装 TypeScript ( `npm i --save-dev typescript`)。这是为了复制 CodeSandbox 环境

## index.html 文件

接下来，我们需要创建一个基本的索引文件供浏览器加载——它将指向脚本，并有一个标识的 div 元素供我们在其中呈现应用程序。

默认情况下，CodeSandbox 已经提供了这一功能，尽管我们需要将脚本扩展从`js`改为`jsx`(或者将`ts`改为`tsx`)。

这里是`index.html`的内容

```
<html>
  <head>
    Parcel Sandbox
    <meta charset="UTF-8" />
  </head>

  <body>
    <div id="app"></div>

    <script src="src/index.tsx"></script>
  </body>
</html> 
```

## 应用程序的 JavaScript 入口点

接下来，我们需要创建脚本元素中引用的文件——code sandbox 用户可以在`src`重命名现有文件，而本地用户只需在`src/index.jsx`或`src/index.tsx`创建即可。

无论如何，如果有的话，删除所有的内容，让我们从必要的导入开始:

```
import {h, render} from "preact"; 
```

这里，我们导入 Preact 的`h`函数的实现，以及一个`render`函数——我们稍后会用到它。

### 我们的第一个组件

接下来，我们需要为 Preact 编写一些东西来呈现。为此我们需要编写一个**组件**。我们将在下一部分中更多地讨论组件，但简而言之，组件是可重用的代码片段，可以传递给道具，并具有状态——它们是可重用的元素，是现代 JavaScript 框架应用程序中可重用性的基础。

组件有两种变体——基于功能的，或者基于类的。越来越多的项目使用基于功能的组件，尤其是现在钩子变得越来越流行(后面会详细介绍)。我们将使用一个基于功能的组件，并将其命名为`Application`。让我们把它添加到我们的`index`文件:

```
function Application() {
  return <p>Hello world!</p>;
} 
```

就是这样。这个函数不接受任何道具(因为它们是通过参数给出的)，只返回一个段落元素(实际上，它是虚拟 DOM 的等价物)。

我们需要在某个地方使用这个新组件，这就是为什么我们还引入了`render`函数。

### 渲染我们的分量

因为 Preact 通过它的`h`函数处理虚拟 DOM，所以我们需要一种方法让虚拟 DOM 变成真实的。这就是`render`函数所做的——它输入一个虚拟 DOM 节点和一个 DOM 元素来插入生成的 DOM。在我们的`index.html`中已经有了一个 div 标签，用于托管我们的应用程序——所以让我们使用它。然后，我们需要一个虚拟 DOM 节点传入渲染，这个虚拟 DOM 将来自我们的组件。记住 Preact 的`h`函数*返回*虚拟 DOM，但你可能不知道(但可能已经猜到)的是，该函数也可以将组件而不是标签名作为第一个参数。这就是我们在 Preact 中重用组件的方式。而且，在我们可以使用`h`的地方，我们也可以写 JSX——因此这就是我们如何将应用程序组件传递给渲染函数。

无论如何，让我们将一些生成的 DOM 放入我们的 web 页面。我们将获得对 DOM 中的`<div id="app">`元素的引用，并将其传递给`render`函数:

```
const root = document.getElementById("app");
render(<Application/>, root); 
```

我们的组件显示“Hello World！”放到网页上！🔥

[https://codesandbox.io/embed/2w5s9](https://codesandbox.io/embed/2w5s9)

### 嵌套组件

让我们创建更多的组件，好吗？

组件有几个好处——但最基本的是，它们允许创建可重用的元素；例如，我们接下来要编写的按钮组件。

将实现留到下一部分，让我们在应用程序中使用这个假设的按钮组件。我们希望这个组件在一定程度上是可定制的，这可以通过属性来实现。我们可以通过这些道具传递任何值，包括函数(因为[函数是值](https://www.oreilly.com/library/view/javascript-the-definitive/9781449393854/ch08s04.html))。这将有助于保持我们的按钮组件的通用性和可重用性——我们将公开一个“onClick”prop，它需要一个回调函数，当用户单击组件时，该函数将被调用。

让我们将这个规范用于这个按钮组件的公认属性(通常缩写为 props):

```
interface ButtonProps {
  /** Any CSS color string, from names to hex codes to HSL, HSV or RGB(A) functions */
  color: string;
  /** Changes the button elevation by altering the shadow property of the button */
  elevated?: boolean;
  /** Callback function which gets called when the user clicks on the button */
  onClick?: (ev: MouseEvent) => void;
} 
```

*注意:这是我在源代码中用来定义组件属性的有效类型脚本，参见 CodeSandbox 示例*

这样，我们可以创建一个带有两个按钮的应用程序，来展示如何分别定制它们。在下面的代码中，每个按钮都被分配了一种随机颜色；第一个按钮有一个回调函数，在点击时显示一个警告框，而第二个按钮没有任何回调函数；此外，只有第一个按钮是提升的(默认情况下)，第二个按钮的提升是关闭的。

下面是代码及其附带的 CodeSandbox 示例:

```
function Application() {
  const color1 = colors[Math.round(Math.random() * colors.length)];
  const color2 = colors[Math.round(Math.random() * colors.length)];
  return (
    <div>
      <p>Hello world!</p>
      <Button color={color1} onClick={() => alert("First button was clicked!")}>
        Learn more
      </Button>
      <Button color={color2} elevated={true}>
        Another button
      </Button>
    </div>
  );
} 
```

[https://codesandbox.io/embed/cr5eo](https://codesandbox.io/embed/cr5eo)

# 在下一部分

我们将研究如何实现这个按钮组件(尽管如果您想弄清楚的话，可以自己查看 CodeSandbox 源代码)。我们将介绍编写组件的两种方法，以及几种有用的技术。