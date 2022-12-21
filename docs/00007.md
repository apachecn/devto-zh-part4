# 使用 useReducer 改进代码

> 原文：<https://dev.to/eveporcello/improving-code-with-usereducer-2lda>

*我们第二版[学习反应](http://shop.oreilly.com/product/0636920252894.do)系列文章的下一篇是关于`useReducer`。*

考虑一下`Checkbox`组件。这个组件是一个保存简单状态的组件的完美例子。该框要么选中，要么不选中。`checked`是状态值，`setChecked`是用于改变状态的函数。当组件第一次渲染时，`checked`的值将是`false` :

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
} 
```

这工作得很好，但是这个函数的一个区域可能会引起警报:

```
onChange={() => setChecked(checked => !checked)} 
```

仔细看。乍一看感觉还行，但我们是在这里挑起事端吗？我们正在发送一个函数，它接受当前值`checked`并返回相反的值`!checked`。这可能比它需要的更复杂。开发人员很容易发送错误的信息，破坏整个系统。与其这样处理，为什么不提供一个函数作为切换呢？

让我们添加一个名为`toggle`的函数来做同样的事情:调用`setChecked`并返回与`checked`当前值相反的值:

```
function Checkbox() {
  const [checked, setChecked] = useState(false);

  function toggle() {
    setChecked(checked => !checked);
  }

  return (
    <>
      <input type="checkbox" value={checked} onChange={toggle} />
      {checked ? "checked" : "not checked"}
    </>
  );
} 
```

这样更好。`onChange`被设置为一个可预测的值:`toggle`函数。无论何时何地，我们都知道这个函数要做什么。每次使用 checkbox 组件时，我们仍然可以进一步产生更可预测的结果。还记得我们在`toggle`函数中发送给`setChecked`的函数吗？

```
setChecked(checked => !checked); 
```

我们现在用一个不同的名字来指代这个函数`checked => !checked`:一个**减速器**。reducer 函数最简单的定义是它接受当前状态并返回一个新状态。如果`checked`是`false`，应该返回相反的，`true`。我们可以将逻辑抽象成一个始终产生相同结果的 reducer 函数，而不是将这种行为硬编码成`onChange`事件。我们将使用`useReducer` :
代替组件中的`useState`

```
function Checkbox() {
  const [checked, toggle] = useReducer(checked => !checked, false);

  return (
    <>
      <input type="checkbox" value={checked} onChange={toggle} />
      {checked ? "checked" : "not checked"}
    </>
  );
} 
```

`useReducer`取减速器功能和初始状态，`false`。然后我们将`onChange`函数设置为`toggle`，这将调用减速器函数。

我们早期的减速器`checked => !checked`就是一个很好的例子。如果向一个函数提供了相同的输入，那么应该会得到相同的输出。这个概念源于 JavaScript 中的`Array.reduce`。`reduce`从根本上来说，做的事情和 reducer 一样:它接受一个函数(将所有的值缩减为一个值)和一个初始值，并返回一个值。

`Array.reduce`接受一个缩减函数和一个初始值。对于`numbers`数组中的每个值，reducer 被调用，直到返回一个值。

```
const numbers = [28, 34, 67, 68];

numbers.reduce((number, nextNumber) => number + nextNumber, 0); // 197 
```

发送给`Array.reduce`的 reducer 接受两个参数。您还可以向一个 reducer 函数发送多个参数:

```
function Numbers() {
  const [number, setNumber] = useReducer(
    (number, newNumber) => number + newNumber,
    0
  );

  return <h1 onClick={() => setNumber(30)}>{number}</h1>; } 
```

每次我们点击`h1`，我们每次都会在总数上加 30。

### useReducer 处理复杂状态

随着状态变得更加复杂，可以帮助我们更加可预测地处理状态更新。考虑一个包含用户数据的对象:

```
const firstUser = {
  id: "0391-3233-3201",
  firstName: "Bill",
  lastName: "Wilson",
  city: "Missoula",
  state: "Montana",
  email: "bwilson@mtnwilsons.com",
  admin: false
}; 
```

然后我们有一个名为`User`的组件，它将`firstUser`设置为初始状态，该组件显示适当的数据:

```
function User() {
  const [user, setUser] = useState(firstUser);

  return (
    <div>
      <h1>
        {user.firstName} {user.lastName} - {user.admin ? "Admin" : "User"}
      </h1>
      <p>Email: {user.email}</p>
      <p>
        Location: {user.city}, {user.state}
      </p>
      <button>Make Admin</button>
    </div>
  );
} 
```

管理状态时常见的错误是覆盖状态:

```
<button
  onClick={() => {
    setUser({ admin: true });
  }}
>
  Make Admin
</button> 
```

这样做将覆盖来自`firstUser`的状态，并用我们发送给`setUser`函数的内容替换它:`{admin: true}`。这可以通过扩展来自用户的当前值，然后覆盖`admin`值:
来解决

```
<button
  onClick={() => {
    setUser({ ...user, admin: true });
  }}
>
  Make Admin
</button> 
```

这将采用初始状态并推入新的键/值:`{admin: true}`。我们需要在每个`onClick`中重写这个逻辑，使它容易出错。当我明天回到应用程序时，我可能会忘记这样做。

```
function User() {
  const [user, setUser] = useReducer(
    (user, newDetails) => ({ ...user, ...newDetails }),
    firstUser
  );
  ...
} 
```

然后将新的状态值`newDetails`发送给 reducer，它将被推入对象:

```
<button
  onClick={() => {
    setUser({ admin: true });
  }}
>
  Make Admin
</button> 
```

当状态有多个子值或者下一个状态依赖于前一个状态时，这种模式很有用。这里我们利用了传播的力量。教大家传播，他们会传播一天。教每个人使用安全套，他们会终生受益。