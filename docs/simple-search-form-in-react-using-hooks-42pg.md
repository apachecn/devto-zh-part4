# REACT 中的简单搜索表单使用挂钩🔎。

> 原文：<https://dev.to/asimdahall/simple-search-form-in-react-using-hooks-42pg>

搜索是 web 应用程序中最重要的组件之一。让我们以一个电子商务平台为例，那里有成千上万的商品在出售，但是要找到你正在寻找的特定商品，你需要搜索🔍使用平台提供的搜索组件来搜索该项目。

今天我们将学习构建一个简单的搜索表单，使用 react 从数据列表中进行搜索。

# 设置项目

要设置你的项目，你可以使用`create-react-app`或者你也可以去 [CodeSandBox](http://codesandbox.io) 。
你可以在这里找到一篇关于建立你的 react 项目[的文章。](https://github.com/facebook/create-react-app)

创建项目后，首先，让我们创建一个简单的 UI，它有一个输入字段并显示搜索结果列表。

转到项目根目录下的`index.js`文件，清理里面的所有代码并添加以下代码。

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
  return (
    <div className="App">
      <input
        type="text"
        placeholder="Search"
      />
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的组件中，我们创建了一个简单的输入表单(它目前不做任何事情)和一个将要显示的结果的模拟列表。

现在，我们将双向数据绑定应用于输入字段，它基本上从用户处获取值，并将其保存到状态中。

```
import React from "react";
import ReactDOM from "react-dom";

function App() {
 const [searchTerm, setSearchTerm] = React.useState("");
  const handleChange = event => {
    setSearchTerm(event.target.value);
  };
  return (
    <div className="App">
      <input
        type="text"
        placeholder="Search"
        value={searchTerm}
        onChange={handleChange}
      />
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经创建了一个名为`searchTerm`的状态，它在每次发生`change`事件时保存来自搜索输入的数据。`handleChange`方法将`event`对象作为参数，并使用`React.useState`方法提供的`setSearchTerm`方法将表单的当前值设置为`searchTerm`状态。

现在，我们创建一个模拟数据列表，并根据用户在我们创建的输入框中提供的输入来搜索数据。

```
import React from "react";
import ReactDOM from "react-dom";

const people = [
  "Siri",
  "Alexa",
  "Google",
  "Facebook",
  "Twitter",
  "Linkedin",
  "Sinkedin"
];

function App() {
 const [searchTerm, setSearchTerm] = React.useState("");
 const [searchResults, setSearchResults] = React.useState([]);
 const handleChange = event => {
    setSearchTerm(event.target.value);
  };

  return (
    <div className="App">
      <input
        type="text"
        placeholder="Search"
        value={searchTerm}
        onChange={handleChange}
      />
      <ul>
        <li>Item 1</li>
        <li>Item 2</li>
      </ul>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中，我们创建了一个名为`people`的模拟列表/数组，从中我们可以在组件中显示列表。我们还创建了一个名为`searchResults`的状态，用于设置搜索结果。

现在，我们将搜索功能应用于组件。

```
import React from "react";
import ReactDOM from "react-dom";

const people = [
  "Siri",
  "Alexa",
  "Google",
  "Facebook",
  "Twitter",
  "Linkedin",
  "Sinkedin"
];

function App() {
 const [searchTerm, setSearchTerm] = React.useState("");
 const [searchResults, setSearchResults] = React.useState([]);
 const handleChange = event => {
    setSearchTerm(event.target.value);
  };
 React.useEffect(() => {
    const results = people.filter(person =>
      person.toLowerCase().includes(searchTerm)
    );
    setSearchResults(results);
  }, [searchTerm]);

  return (
    <div className="App">
      <input
        type="text"
        placeholder="Search"
        value={searchTerm}
        onChange={handleChange}
      />
      <ul>
         {searchResults.map(item => (
          <li>{item}</li>
        ))}
      </ul>
    </div>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在上面的代码片段中，使用了`React.useEffect` hook，每当方法的依赖关系改变时，它就会执行。`React.useEffect`钩子接受两个参数。第一个参数是当依赖关系中的数据被修改时要执行的函数，第二个参数是`React.useEffect`钩子所依赖的依赖关系的数组。因此，每当`React.useEffect`钩子中依赖项的值改变时，第一个参数中的函数就会执行。

所以在上面的`React.useEffect`钩子中，依赖关系是`searchTerm`，它随着用户的每次输入而改变，然后执行`React.useEffect`钩子的第一个参数中的函数。执行以下功能

```
() => {
    const results = people.filter(person =>
      person.toLowerCase().includes(searchTerm.toLowerCase())
    );
    setSearchResults(results);
  } 
```

Enter fullscreen mode Exit fullscreen mode

在上面的函数中，`filter`方法被应用于`people`数组，该数组根据每次迭代返回的条件返回一个新数组。条件为`person.toLowerCase().includes(searchTerm.toLowerCase())`，这意味着如果人员列表`includes`中的`person`为`searchTerm`，则返回`true`，否则返回`false`。

使用`React.useState`钩子提供的`setSearchResult`将过滤后的列表设置在`searchResults`状态后。

现在我们已经将搜索结果设置为状态，我们通过在组件中使用`searchResults.map`方法来显示它，该方法迭代所有的`searchResults`并在`ul`中呈现它们。

```
 <ul>
     {searchResults.map(item => (
          <li>{item}</li>
     ))}
  </ul> 
```

Enter fullscreen mode Exit fullscreen mode

最终的结果看起来像这样

[![Result](img/56924c80f2068a9c741a70e73e496ae8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---1JH19SK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zmippbu3o2rm1n8dyoml.gif)

你可以在这里找到完整的代码

谢谢你

你也可以在推特上关注我。