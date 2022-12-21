# 在 React 中将参数从子组件移动到父组件

> 原文：<https://dev.to/spukas/moving-arguments-from-child-to-parent-component-in-react-25lp>

为了更容易理解代码，React 实现了单向数据流，在将数据从父组件传递到子组件时，也称为“单向数据绑定”。然而，我们经常需要将一些数据从子组件传递回父组件，例如，当用户的输入影响父组件时，想想表单。对于初来乍到的人来说，有时很难掌握如何将数据发送回给孩子的模式。这篇文章解释了如何用简单的方法做到这一点。

## 用例

假设我们有一个父元素，它呈现三个子元素。每个子元素都有一个按钮，每次用户按下按钮时，父元素都应该显示按钮中被选中的颜色。

```
function Parent() {
  return (
    <>
      <h1>Selected color: </h1> // show selected color
      {['green','red','blue'].map((color) => (
        <Child color={color} ... />
      ))}
    </>
  )
}

function Child({ color }) {
  return (
    <button value={color}>{color} button</button>
  )
} 
```

通过 props，自上而下传递参数很容易，但发回数据可能会很棘手。

## 回调救援

让我们从下到上进行逆向工程:

1.  为了捕获按钮点击事件，我们需要添加一个处理程序

```
function Child({ color }) {
  function handleClick(event) {
    // do something meaningful
  }
  return (
    <button name={color} onClick={handleClick}>{color}</button>
  )
} 
```

1.  处理程序内部是调用另一个函数的最佳位置，一个回调函数，由 props - `onChildClick`从父组件传递。请注意，我们还没有创建或传递它，但以后会这样做。回调可以接收任何参数，父组件可以访问它们。在这种情况下，我们将从按钮传递一个参数`name`。

```
function Child({ color,  onChildClick }) {
  function handleClick(event) {
    onChildClick(event.target.name); // pass any argument to the callback
  }
  return (
    <button name={color} onClick={handleClick}>{color}</button>
  )
} 
```

1.  最后一步是从回调中读取参数，并将它们保存到父组件状态以备后用。
    *   创建回调函数`handleChildClick`并通过 prop `onChildClick`将其传递给子组件。
    *   添加`useState`钩子，分配状态变量`color`和一个函数`setColor`来更新它。
    *   从`handleChildClick`函数中读取一个从子组件传递来的参数，并调用`setColor`函数用新值更新状态。

```
function Parent() {
  const [color, setColor] = useState('');
  function handleChildClick(color) {
    setColor(color);
  }
  return (
    <>
      <h1>selected color: {color}</h1>
      {['green','red','blue'].map((color) => (
        <Child color={color} onChildClick={handleChildClick} ... />
      ))}
    </>
  )
} 
```

差不多就是这样，在每一次按钮点击时我们调用事件处理程序，在它里面我们从`props`调用一个回调函数，在回调函数里面(在这个例子中是`handleChildClick`)设置父组件的状态。

## 你可能奋斗的地方

*   直接从事件调用回调并传递参数。它将立即为所有呈现的按钮元素调用函数，并且不会起作用。

```
<button onClick={onChildClick(color)} ... /> 
```

*   将参数传递给事件处理程序也会立即调用所有呈现的按钮元素的函数，并且不会起作用。

```
<button onClick={handleClick(color)} ... /> 
```

*   使用内联箭头函数并在其中调用回调函数将在每次呈现按钮时创建一个新的箭头函数，而且，如果不显式地将事件对象传递给回调函数，将会丢失事件对象。可能，但效率不高。

```
<button onClick={(event) => onChildClick(color, event)} ... /> 
```

*   如果使用类组件和方法作为事件处理程序，不要忘记绑定上下文。使用`bind`,所有进一步的参数，如`event`,都将被转发。

```
<button onClick={this.handleClick.bind(this, color)} .../> 
```

## 总结

从子对象向父对象传递参数并不令人困惑，只是要找出调用回调的正确位置可能有点棘手。我希望这篇文章能澄清一些困惑。