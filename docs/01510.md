# 熟悉 useEffect:第一部分

> 原文：<https://dev.to/eveporcello/getting-familiar-with-useeffect-part-one-3eo1>

*本文最初发布于[MoonHighway.com](https://moonhighway.com/use-effect-part-one)。*

渲染是 React 应用程序的核心。当某些东西发生变化时(道具、状态)，组件树会重新呈现，在用户界面中反映出这种变化。但是当我们在渲染之后需要做些什么的时候会发生什么呢？正如你可能想象的那样，有一个钩子。

考虑一个简单的组件`Checkbox`。我们使用`useState`来设置一个`checked`值和一个叫做`setChecked`的函数来改变`checked`的值。用户可以选中和取消选中这个框，但是我们如何提醒用户这个框已经被选中了？我们将使用`alert`来尝试，因为这是一个阻塞线程的好方法:

```
function Checkbox() {
  const [checked, setChecked] = useState(false);

  alert(`checked: ${checked.toString()}`);

  return (
    <>
      <input
        type="checkbox"
        value={checked}
        onChange={() => setChecked(checked => !checked)}
      />
      {checked ? "checked" : "not checked"}
    </>
  );
} 
```

我们在渲染之前添加了`alert`来阻止渲染。直到用户单击警告框上的`OK`按钮，组件才会呈现。因为警报被阻止了，所以我们直到点击`OK`才能看到复选框的下一个状态。

这不是我们的目标，所以也许我们应该在返回后发出警报？

```
function Checkbox() {
  const [checked, setChecked] = useState(false);

  return (
    <>
      <input
        type="checkbox"
        value={checked}
        onChange={() => setChecked(checked => !checked)}
      />
      {checked ? "checked" : "not checked"}
    </>
  );

  alert(`checked: ${checked.toString()}`);
} 
```

算了吧。我们不能在渲染后调用`alert`,因为代码永远不会到达。为了确保我们看到预期的`alert`，我们可以使用`useEffect`。将`alert`放在`useEffect`函数中意味着该函数将在渲染后被调用，这是一个副作用:

```
function Checkbox() {
  const [checked, setChecked] = useState(false);

  useEffect(() => {
    alert(`checked: ${checked.toString()}`);
  });

  return (
    <>
      <input
        type="checkbox"
        value={checked}
        onChange={() => setChecked(checked => !checked)}
      />
      {checked ? "checked" : "not checked"}
    </>
  );
} 
```

当渲染需要产生副作用时，我们使用`useEffect`。把副作用想象成函数做的不属于回报的事情。这个功能就是`Checkbox`。`Checkbox`函数返回一个片段。但是我们可能希望组件做的不止这些。除了返回 UI 之外，我们希望组件做的事情叫做效果。

一个`alert`、`console.log`，或者与浏览器或本地 API 的交互都不是渲染的一部分。这不是退货的一部分。但是，在 React 应用程序中，渲染会影响其中一个事件的结果。我们可以使用`useEffect`来等待渲染，然后将值提供给`alert`或`console.log` :

```
useEffect(() => {
  console.log(checked ? "Yes, checked" : "No, not checked");
}); 
```

类似地，我们可以在渲染时使用`checked`的值，然后将其设置为`localStorage` :
中的值

```
useEffect(() => {
  localStorage.setItem("checkbox-value", checked);
}); 
```

我们还可以使用`useEffect`来关注已经添加到 DOM 中的特定文本输入。React 将呈现输出，然后调用`useEffect`聚焦元素:

```
useEffect(() => {
  txtInputRef.current.focus();
}); 
```

在`render`时，`txtInputRef`将有一个值。我们可以访问效果中值来应用焦点。每次我们渲染时，`useEffect`都可以从渲染中获取最新的值:道具、状态、引用等。

酷，但是...为什么？考虑一个渲染。我们呈现一个复选框，其中的`checked`值为假。在渲染时，React 将查看`checked`值并调用`useEffect` :

```
useEffect(() => {
  console.log(checked ? "Yes, checked" : "No, not checked");
}); 
```

React 在后期渲染时调用这个函数:

```
useEffect(() => console.log("No, not checked")); 
```

然后我们将`checked`值更新为`true`。这将导致另一次渲染。在这一点上，渲染将导致`useEffect`被再次调用，但在这一点上函数是不同的:

```
useEffect(() => console.log("Yes, checked")); 
```

每次组件渲染时，我们都能看到`useEffect`中`checked`的值，因为`useEffect`每次都是唯一的函数。把`useEffect`想象成一个发生在`render`之后的函数。当一个`render`开火时，我们可以看看那个渲染的值并在效果中使用它们。然后，一旦我们再次渲染，整个事情重新开始。新值，新渲染，新效果。

`useEffect`是构建 React 应用程序时需要理解的强大工具。在本系列的下一部分中，我们将更仔细地研究依赖数组，它允许我们定义关于为什么以及何时应该进行渲染的更具体的规则。