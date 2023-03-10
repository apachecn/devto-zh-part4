# 在没有库的 React 中使用表单

> 原文：<https://dev.to/sergiodxa/working-with-forms-in-react-without-libraries-4iad>

用 JavaScript 处理表单可能是一项艰巨的任务，在本文中我们将学习如何驯服它们。

## 不受控制的输入

首先，我们需要谈谈不受控制的输入，我说的输入也是选择或文本区。这是输入的默认状态，在这种情况下，我们不做任何特殊处理，让浏览器处理它的值。

```
function Form() {
  const [message, setMessage] = React.useState("");

  function handleSubmit(event) {
    event.preventDefault();
    setMessage(event.target.elements.message.value);
    event.target.reset();
  }

  return (
    <>
      <p>{message}</p>
      <form onSubmit={handleSubmit}>
        <input name="message" type="text" />
      </form>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们在上面的例子中看到的，我们用输入的值来更新我们的状态`message`。在用户提交表单后，按下`enter`,为了重置输入值，我们只需使用表单的`reset()`方法来重置整个表单。

这是正常的 DOM 操作来读取值并重置它，没有什么特别的反应。

## 受控输入

现在让我们来谈谈有趣的部分，控制器输入/选择/文本区域是一个元素，其中的值被绑定到状态，我们需要更新状态来更新用户看到的输入值。

```
function Form() {
  const [message, setMessage] = React.useState("");

  function handleSubmit(event) {
    event.preventDefault();
    setMessage("");
  }

  function handleChange(event) {
    setMessage(event.target.value);
  }

  return (
    <>
      <p>{message}</p>
      <form onSubmit={handleSubmit}>
        <input
          name="message"
          type="text"
          onChange={handleChange}
          value={message}
        />
      </form>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的示例将`input`值设置为`message`，并附加了一个我们称为`handleChange`的`onChange`事件监听器，在这个函数中，我们需要`event.target.value`，在那里我们将接收输入的新值，即当前值加上用户键入的内容，我们调用`setMessage`来更新我们的组件状态，这将更新`p`标签的内容和`input`标签的值以匹配新的状态。

如果我们想重置输入，我们可以调用`setMessage("")`，就像我们在`handleSubmit`中做的那样，这将重置状态，这样做输入的值和`p`内容。

## 添加简单验证

现在让我们添加一个简单的验证，复杂的验证类似，但有更多的规则，在这种情况下，如果使用特殊字符`_`，我们将使输入无效。

```
function Form() {
  const [message, setMessage] = React.useState("");
  const [error, setError] = React.useState(null);

  function handleSubmit(event) {
    event.preventDefault();
    setError(null);
    setMessage("");
  }

  function handleChange(event) {
    const value = event.target.value;
    if (value.includes("_")) setError("You cannot use an underscore");
    else setError(null);
    setMessage(value);
  }

  return (
    <>
      <p>{message}</p>
      <form onSubmit={handleSubmit}>
        <input
          id="message"
          name="message"
          type="text"
          onChange={handleChange}
          value={message}
        />
        {error && (
          <label style={{ color: "red" }} htmlFor="message">
            {error}
          </label>
        )}
      </form>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们创建两种状态，一种是输入值，另一种是错误消息。和之前一样，在我们的`handleSubmit`中，我们将把`message`状态重置为空字符串，另外我们将把`error`状态重置为`null`。

在`handleChange`中，我们将读取输入的新值，并查看下划线是否存在。如果我们发现一个下划线，我们将把错误状态更新为消息`"You cannot use an underscore"`，如果没有，我们将把它设置为`null`。验证之后，我们将使用新值更新`message`状态。

在我们返回的 UI 中，我们将检查`error`的存在，并使用红色文本呈现一个`label`,指向输入并显示里面的错误消息。错误在一个标签内，让用户单击它并将焦点移到输入上。

## 控制一个文本区

之前我说过使用`input`和`textarea`是相似的，事实上也是如此，让我们把我们渲染的元素改为`textarea`，我们上面的代码将继续工作，没有任何其他变化，如下图所示。

```
function Form() {
  const [message, setMessage] = React.useState("");
  const [error, setError] = React.useState(null);

  function handleSubmit(event) {
    event.preventDefault();
  }

  function handleChange(event) {
    const value = event.target.value;
    if (value.includes("_")) {
      setError("You cannot use an underscore");
    } else {
      setError(null);
      setMessage(value);
    }
  }

  return (
    <>
      <p>{message}</p>
      <form onSubmit={handleSubmit}>
        <textarea
          id="message"
          name="message"
          onChange={handleChange}
          value={message}
        />
        {error && (
          <label style={{ color: "red" }} htmlFor="message">
            {error}
          </label>
        )}
      </form>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然通常`textarea`是一个内部内容为`<textarea>Content here</textarea>`的元素，但在 React 中，我们使用`value`属性(如 inputs)和`onChange`事件)来更改值，使 input 和 textarea 之间的更改类似。

## 控制一个选择

现在我们来谈谈`select`。与`textarea`一样，你把它当作一个普通的`input`，用选定的值传递一个`value`道具，用`onChange`监听值的变化。传递给`select`的值应该与其中一个选项的值相匹配，以将其中一个选项显示为当前选中的选项。

```
function Form() {
  const [option, setOption] = React.useState(null);
  const [error, setError] = React.useState(null);

  function handleSubmit(event) {
    event.preventDefault();
  }

  function handleChange(event) {
    setOption(event.target.value);
  }

  function handleResetClick() {
    setOption(null);
  }

  function handleHooksClick() {
    setOption("hooks");
  }

  return (
    <>
      <p>{option}</p>
      <form onSubmit={handleSubmit}>
        <select onChange={handleChange} value={option}>
          <option value="classes">Classes</option>
          <option value="flux">Flux</option>
          <option value="redux">Redux</option>
          <option value="hooks">Hooks</option>
        </select>
      </form>
      <button type="button" onClick={handleResetClick}>
        Reset
      </button>
      <button type="button" onClick={handleHooksClick}>
        Hooks!
      </button>
    </>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

## 处理文件输入

现在为了结束我们来谈谈文件输入，这个特殊的输入不能被控制，但是仍然可以得到一些数据并保存在状态中以在其他地方显示。在下面的例子中，我们正在为一个隐藏的文件输入创建一个自定义的用户界面。

```
function Form() {
  const [fileKey, setFileKey] = React.useState(Date.now());
  const [fileName, setFileName] = React.useState("");
  const [fileSize, setFileSize] = React.useState(0);
  const [error, setError] = React.useState(null);

  function resetFile() {
    setFileKey(Date.now());
    setFileName("");
    setFileSize(0);
    setError(null);
  }

  function handleChange(event) {
    const file = event.target.files[0];

    setFileSize(file.size);

    if (file.size > 100000) setError("That file is too big!");
    else setError(null);

    setFileName(file.name);
  }

  return (
    <form>
      <label htmlFor="file">
        Select a single file to upload. (max size: 100kb)
        <br />
        {fileName && (
          <>
            <strong>File:</strong> {fileName} ({fileSize / 1000}kb)
          </>
        )}
        <input id="file" type="file" key={fileKey} onChange={handleChange} style={{ display: "none" }} />
      </label>
      {error && (
        <label style={{ color: "red" }} htmlFor="file">
          {error}
        </label>
      )}
      <button type="button" onClick={resetFile}>
        Reset file
      </button>
    </form>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

我们侦听更改事件，读取文件大小和名称，并验证文件的大小，如果文件太大，我们将`error`状态设置为消息`"That file is too big!"`，如果文件不是很大，我们将错误设置为`null`，这让我们可以删除之前的错误，如果用户之前选择了一个大文件。

我们也有一个按钮来重置输入，因为我们不能控制状态，我们可以使用`key`来强制反应再次渲染输入，并在这个过程中重置它，我们使用当前日期，每次用户单击`Reset file`它将获得当前日期，并将其保存到`fileKey`状态，并重置它的输入。