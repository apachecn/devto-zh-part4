# 状态和效果挂钩反应简介

> 原文：<https://dev.to/sergiodxa/introduction-to-react-with-the-state-effects-hooks-4cbm>

## 开始一个 React 项目

让我们从创建一个新的 React 应用程序开始，我们可以使用 Create React App ( *CRA* )工具来生成一个没有配置的基本样板文件。

```
$ npx create-react-app my-app 
```

Enter fullscreen mode Exit fullscreen mode

`npx`命令与`npm`捆绑在一起，让我们可以使用 npm 包中的命令，而无需全局安装。

## 运行您的项目

现在我们已经创建了项目，我们可以访问我们的应用程序目录并启动项目，为此运行以下命令。

```
$ cd my-app
$ yarn start 
```

Enter fullscreen mode Exit fullscreen mode

## 用 React 渲染一个元素

您会注意到您的项目附带了一些已经创建的文件，删除`src`文件夹中的所有文件，并使用下面的内容创建一个新的`index.js`文件。

```
// src/index.js
import React from "react";
import { render } from "react-dom";

const $root = document.getElementById("root");
render(<h1>Hello, World!</h1>, $root); 
```

Enter fullscreen mode Exit fullscreen mode

这将使用 React 将`<h1>Hello, World!</h1>`呈现给 DOM，我们刚刚呈现了第一个元素。

## 创建一个组件

现在让我们创建我们的第一个组件。

```
// src/index.js
function HelloWorld() {
  return <h1>Hello, World!</h1>; }

const $root = document.getElementById("root");
render(<HelloWorld />, $root); 
```

Enter fullscreen mode Exit fullscreen mode

组件是一个普通的 JavaScript 函数，但是它的名字应该以大写字母开头，并且必须返回某种 HTML 代码。还有其他有效值，如 arrays 或 null，但您通常希望返回 HTML。

> *注意*:这不是真正的 HTML，是一个叫做 JSX 的东西，在编译时被转换成函数调用，特别是对`React.createElement`函数的调用。

## 运行一个效果

这一次我们将运行一个副作用，在这种情况下，我们将更改页面的标题，即您在浏览器选项卡上阅读的标题。为此，我们需要使用`React.useEffect`。

```
// src/index.js
function HelloWorld() {
  React.useEffect(() => {
    document.title = "Hello, World!";
  });

  return <h1>Hello, World!</h1>; } 
```

Enter fullscreen mode Exit fullscreen mode

这个 useEffect 函数被称为*挂钩*，可以使用这个函数依赖于 reaction 来做不同的事情，在这种情况下，是在组件呈现在屏幕上之后运行一个副作用。

useEffect 钩子接收一个函数，并在每次渲染组件后执行它(如果组件被更新，它将再次运行它)。在我们的函数中，我们将把`document.title`改为字符串`Hello, World!`。

## 处理事件

你需要做的一件事就是监听应用程序中发生的事件并对它们做出反应，比如点击、更改、提交、滚动等事件。在 React 中，我们使用`onEventName`来完成，其中`EventName`是事件的名称，例如`onClick`、`onChange`、`onSubmit`、`onMouseOver`等。

```
// src/index.js
function HelloWorld() {
  React.useEffect(() => {
    document.title = "Hello, World!";
  });

  function handleChange(event) {
    console.log(event.target.value);
  }

  return (
    <main>
      <h1>Hello, World!</h1>
      <input type="text" defaultValue="Hello, World!" onChange={handleChange} />
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在用默认值`Hello, World!`创建一个文本类型的输入，我们将监听 change 事件，当输入改变时，它将调用我们的`handleChange`函数并运行`console.log(event.target.value)`。

## 使用状态

但我们通常不希望只记录值，我们希望保留它并在应用程序的其他地方使用它，为此我们使用 React 的另一个钩子，这个钩子叫做`React.useState`，让我们将值保存在内存中，并在需要时更改它们，当状态改变时，组件将再次呈现新值。

```
// src/index.js
function HelloWorld() {
  const [title, setTitle] = React.useState("HelloWorld");

  React.useEffect(() => {
    document.title = "HelloWorld";
  });

  function handleChange(event) {
    setTitle(event.target.value);
  }

  return (
    <main>
      <h1>HelloWorld</h1>
      <input type="text" value={title} onChange={handleChange} />
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在创建一个新的状态，并在两个元素中析构得到的数组，第一个`title`是状态值，第二个`setTitle`是一个函数，React 为我们提供了更改状态值的方法，我们需要用新的状态值来调用它。

在我们的输入中，我们将`defaultValue`更改为`value`，这迫使输入将我们的`title`状态作为值，这意味着用户是否写了什么并不重要，只要状态不改变，输入值就不会改变。

这里是我们的`handleChange`工作的地方，它将从 change 事件中读取新的假定值，并将其传递给`setTitle`以更新状态，这将触发新的呈现并用新值更新输入。

## 使用状态&一起效果

仅使用状态来跟踪输入的值是可以的，但这不是真正有用的东西，让我们将输入的状态与文档的标题同步。我们可以在我们的`useEffect`钩子中使用我们的`title`状态，并根据用户在输入中写的内容动态地改变文档的标题。

```
// src/index.js
function HelloWorld() {
  const [title, setTitle] = React.useState("HelloWorld");

  React.useEffect(() => {
    document.title = title;
  });

  function handleChange(event) {
    setTitle(event.target.value);
  }

  return (
    <main>
      <h1>{title}</h1>
      <input type="text" value={title} onChange={handleChange} />
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用`<h1>`中的`title`状态的值在用户写的时候更新它。

## 添加第二种状态&效果

现在让我们添加第二个状态和效果，在我们的组件中，我们可以有任意多的状态和效果，唯一的规则是它们不能在条件或循环中。让我们跟踪用户当前是否在写作，就像 Slack 或脸书在他们的聊天中所做的那样。

```
// src/index.js
function HelloWorld() {
  const [title, setTitle] = React.useState("Hello, World!");
  const [isWriting, setIsWriting] = React.useState(false);

  React.useEffect(() => {
    if (!isWriting) {
      document.title = title;
    }
  });

  React.useEffect(() => {
    setTimeout(() => setIsWriting(false), 1000);
  });

  function handleChange(event) {
    setIsWriting(true);
    setTitle(event.target.value);
  }

  return (
    <main>
      <h1>{title}</h1>
      <input type="text" value={title} onChange={handleChange} />
      User is writing: {isWriting.toString()}
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用`React.useState`创建了一个新的状态，并将其值默认为`false`，我们称之为`isWriting`的状态和改变它的函数`setIsWriting`。我们更新了原来的效果，只在用户不写的时候更新文档的标题。

现在我们运行第二个效果，我们正在做一个`setTimeout`来在一秒钟后将`isWriting`状态更新为 false。在`handleChange`函数中，我们改变了两种状态，将`isWriting`改为`true`，将`title`改为用户编写的新内容。

最后，我们添加了一行来显示用户界面，如果用户正在书写，则需要用`.toString()`来显示`true`或`false`作为内容。

## 添加一个效果依赖数组

如果我们运行上面的例子，可以看到在第一秒之前它工作得很好，然后它开始更新状态，而不需要等待用户停止书写。这是因为两种效果都在每次渲染后运行。

我们可以向`useEffect`传递第二个参数，它是一个数组，列出了我们的效果所依赖的效果之外的值。在我们的例子中，第一个效果将使用来自状态的`isWriting`和`title`，这意味着它依赖于那些状态的值，而第二个效果仅依赖于`isWriting`。

这个依赖关系数组的思想是，我们可以限制我们的效果，只在那些依赖关系改变时才运行。如果`isWriting`没有改变，第二个效果将不会运行，如果`title`没有改变，那么甚至第一个效果也不会运行。

```
// src/index.js
function HelloWorld() {
  const [title, setTitle] = React.useState("Hello, World!");
  const [isWriting, setIsWriting] = React.useState(false);

  React.useEffect(() => {
    if (!isWriting) {
      document.title = title;
    }
  }, [isWriting, title]);

  React.useEffect(() => {
    setTimeout(() => setIsWriting(false), 1000);
  }, [isWriting]);

  function handleChange(event) {
    setIsWriting(true);
    setTitle(event.target.value);
  }

  return (
    <main>
      <h1>{title}</h1>
      <input type="text" value={title} onChange={handleChange} />
      User is writing: {isWriting.toString()}
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## 清除一个效果

这稍微好一点了，但是我们仍然看到文档的标题在一秒钟后改变了。我们现在能做的是清除每次调用我们的效果之间的超时。

在一个效果中，可以返回一个在下一次运行该效果之前执行的函数，这让我们可以清除之前运行的效果的结果。在我们的例子中，我们可以用它来运行`clearTimeout`。

```
// src/index.js
function HelloWorld() {
  const [title, setTitle] = React.useState("Hello, World!");
  const [isWriting, setIsWriting] = React.useState(false);

  React.useEffect(() => {
    if (!isWriting) {
      document.title = title;
    }
  }, [isWriting, title]);

  React.useEffect(() => {
    const timer = setTimeout(() => setIsWriting(false), 1000);
    return () => clearTimeout(timer);
  }, [isWriting]);

  function handleChange(event) {
    setIsWriting(true);
    setTitle(event.target.value);
  }

  return (
    <main>
      <h1>{title}</h1>
      <input type="text" value={title} onChange={handleChange} />
      User is writing: {isWriting.toString()}
    </main>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## 解除状态上升

到目前为止，我们创建了一个单一的组件，如果我们继续向该组件添加功能，它将开始增长，直到很难(如果不是不可能的话)维护和添加新功能。

我们可以避免将它分割成不同的组件，然后将它们组合成一个父组件。

```
// src/title.js
import React from "react";

function Title({ value, isWriting }) {
  React.useEffect(() => {
    if (!isWriting) {
      document.title = value;
    }
  }, [isWriting, value]);

  return <h1>{value}</h1>; }

export default Title; 
```

Enter fullscreen mode Exit fullscreen mode

在我们的第一个组件中，我们移动了`<h1>`和效果，以将文档的标题更新到另一个名为`Title`的组件。我们的组件将接收一个对象作为第一个参数，这个对象叫做`props`，我们可以析构它来读取它们的属性，在我们的例子中是`value`和`isWriting`。

```
// src/input.js
import React from "react";

function Input({ value, onWrite }) {
  React.useEffect(() => {
    const timer = setTimeout(() => onWrite(value), 1000);
    return () => clearTimeout(timer);
  }, [value, onWrite]);

  function handleChange(event) {
    onWrite(event.target.value);
  }

  return <input type="text" value={value} onChange={handleChange} />; }

export default Input; 
```

Enter fullscreen mode Exit fullscreen mode

在我们的第二个组件中，我们移动了`<input />`、`handleChange`和效果来设置它是否正在写入另一个名为`Input`的组件。这将在我们的`prop`中接收两个值，即输入的`value`，与我们在`Title`中接收的相同，以及一个名为`onWrite`的函数来改变这个值。

我们将用`event.target.value`调用这个函数来更新它，当用户写一些东西并且在一秒钟后用相同的值在我们的效果中，这个变化将在下一个组件中有意义。

```
// src/hello-world.js
import React from "react";

import Title from "./title";
import Input from "./input";

function HelloWorld() {
  const [title, setTitle] = React.useState("Hello, World!");
  const [isWriting, setIsWriting] = React.useState(false);

  function handleWrite(value) {
    setIsWriting(value !== title);
    setTitle(value);
  }

  return (
    <main>
      
      <Input value={title} onWrite={handleWrite} />
      User is writing: {isWriting.toString()}
    </main>
  );
}

export default HelloWorld; 
```

Enter fullscreen mode Exit fullscreen mode

我们最新的组件是我们的`HelloWorld`，它将导入`Title`和`Input`组件，并在它的返回值发送`value`、`isWriting`和`onWrite`中使用它们作为道具。

该组件还将保持`title`和`isWriting`的状态，这被称为*“提升状态”*，在我们的示例中，这些状态也在我们的其他组件和`HelloWorld`组件中使用，因此我们不能将值直接移动到输入，因为 React 中的数据流是从组件树的顶部到底部的单向流，我们需要保持状态尽可能靠近顶部，以便能够共享值，在我们的示例中是`HelloWorld`。

在`handleWrite`函数中，我们将使用新接收的值更新`title`的值，并将`isWriting`更改为条件`value !== title`的结果，这意味着如果我们接收的值与当前值相同，我们将把`isWriting`设置为假，如果它们不同，我们将把它设置为`true`。

这样我们只需要渲染`HelloWorld`组件。

```
// src/index.js
import React from "react";
import { render } from "react-dom";

import HelloWorld from "./hello-world";

const $root = document.getElementById("root");
render(<HelloWorld />, $root); 
```

Enter fullscreen mode Exit fullscreen mode

[https://codesandbox.io/embed/mz3v0](https://codesandbox.io/embed/mz3v0)