# 反应钩子&封闭地狱 2

> 原文：<https://dev.to/anpos231/react-hooks-the-closure-hell-2-58g9>

# 反应过来的钩子&关闭地狱 2

这是我上一篇文章的延续([React hooks&the closure hell](https://dev.to/anpos231/react-hooks-the-closure-hell-71m))

## 只是快速的 TL；博士；医生

函数组件要求你在每次重新渲染时重新生成所有的回调函数，因为没有地方存储它们(在旧的基于类的组件中，你只需将函数绑定为方法就可以了)。

以前的解决方案要求您要么指定依赖项以便将它们传递给现有函数，要么使用存储当前属性和值的对象。我认为这些解决方案很麻烦，所以我不断修补，创造了更好的解决方案！

## 遇见`useCallbacks`

```
const useCallbacks = (reinit) => {
  const data = useRef({ callbacks: {}, handlers: {} })
  const callbacks = data.current.callbacks
  const handlers = data.current.handlers

  // Generate new callbacks
  reinit(callbacks)

  // Generate new handlers if necessary
  for (let callback in callbacks) {
    if (!handlers[callback]) {
      handlers[callback] = (...args) => callbacks[callback](...args)
    }
  }

  // Return existing handlers
  return handlers
} 
```

## 用法([试试这里](https://codesandbox.io/s/react-hooks-usestate-sj9zo)

```
const App = () => {
  const [value, setValue] = useState(1);

  const handlers = useCallbacks(callbacks => {
    callbacks.handleClick = (event) => {
      setValue(value + 1)
    }
  })

  // Check console, the state has changed so the App function will re-run
  // but memoized ExpensiveComponent won't be called because the actual handling
  // function hasn't changed.
  console.log(value)

  return (
    <div className="app">
      <ExpensiveComponent onClick={handlers.handleClick} />
      <button onClick={handlers.handleClick}>
        I will not trigger expensive re-render
      </button>
    </div>
  );
}; 
```

就是这样！你不必指定任何依赖关系或处理杂乱的对象。
回调被重新生成，但实际的处理函数没有，所以你的纯组件或记忆化组件不会不必要地重新呈现。

一切都如钩子所愿！告诉我你的想法。