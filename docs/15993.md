# React 中的非阻塞更新

> 原文：<https://dev.to/aravindballa/non-blocking-updates-in-react-24nc>

有时，一些更新/计算会占用大量时间。它们阻止用户界面的更新，这让它看起来很慢。我说的不是花费时间的异步数据获取。

> TL DR 我们将使用 web workers 和 Hooks 作为解决方案。

以此为例

```
// Codesandbox - https://codesandbox.io/s/admiring-pond-ixp59
import React from 'react';
import ReactDOM from 'react-dom';

const fib = i => (i <= 1 ? i : fib(i - 1) + fib(i - 2));

function App() {
  const [value, setValue] = React.useState('');
  const [length, setLength] = React.useState(0);

  // whenever `value` changes
  React.useEffect(() => {
    // we calculate the fibonnaci of the length of input * 5
    const result = fib(value.length * 5);
    setLength(result);
  }, [value]);

  const handleChange = async e => {
    const { value } = e.target;
    setValue(value);
  };
  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
      <input value={value} onChange={handleChange} />
      <p>{length}</p>
    </div>
  );
}

const rootElement = document.getElementById('root');
ReactDOM.render(<App />, rootElement); 
```

当我们在这里输入内容时，需要时间来更新。并且它等待更新出现，直到结果计算没有完成。大数的斐波那契很贵。如果输入很长，它甚至会冻结你的浏览器标签。

对此我们有解决办法吗？我们能知道如何从主线程中卸载这个计算吗？*(他为什么在 javascript 中谈论线程？)*

## 网络工作者

Web workers 充当由我们的浏览器处理的线程。我们可以将一个工作者作为一个线程来启动，并以一种特定的方式与它进行通信。React 毕竟是 Javascript UI 库，我们在浏览器中运行，何乐而不为呢？

这是工人，它必须被静态地服务。(放入`public`文件夹)

```
// thread.worker.js
const fib = i => (i <= 1 ? i : fib(i - 1) + fib(i - 2));

self.addEventListener('message', ({ data }) => {
  let { type, payload } = data;
  if (type === 'UPDATE') {
    payload = payload > 11 ? 11 : payload; // upper limit we set
    const result = fib(payload * 5);
    self.postMessage({ type: 'UPDATE_SUCCESS', payload: result });
  }
});

self.addEventListener(
  'exit',
  () => {
    process.exit(0);
  },
  false
); 
```

我们使用事件与工人交流。看这里的代码，我们在听👂到`message`事件。我们根据传递的`type`处理数据，并将结果作为消息返回。

如果您猜对了，我们将不得不在我们的组件中监听来自 worker 的这些消息。我们的组件是这样的。

```
// App.js
import React from 'react';
import ReactDOM from 'react-dom';

import './styles.css';

const worker = new Worker('/thread.worker.js');

function App() {
  const [value, setValue] = React.useState('');
  const [length, setLength] = React.useState(0);

  // when mount and unmount
  React.useEffect(() => {
    const listener = ({ data: { type, payload } }) => {
      console.log(type, payload);
      if (type === 'UPDATE_SUCCESS') setLength(payload);
    };
    worker.addEventListener('message', listener);
    return () => worker.removeEventListener('message', listener);
  }, []);

  React.useEffect(() => {
    worker.postMessage({ type: 'UPDATE', payload: value.length });
  }, [value]);

  const handleChange = async e => {
    const { value } = e.target;
    setValue(value);
  };
  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
      <input value={value} onChange={handleChange} />
      <p>{length}</p>
    </div>
  );
}

const rootElement = document.getElementById('root');
ReactDOM.render(<App />, rootElement); 
```

如果你正在使用 Webpack，你可以用`worker-loader!thread.js`把它加载到你的组件中。我们直接使用`Worker()`从`public`目录加载它。

这里是代码沙盒演示-[https://codesandbox.io/s/funny-nightingale-5kxo1](https://codesandbox.io/s/funny-nightingale-5kxo1)

> 下面是[效果挂钩文档](https://reactjs.org/docs/hooks-effect.html)供参考。

我们在**第一个效果**中添加消息的监听器，其中依赖项是`[]`，这意味着当组件被安装和卸载时，它将[运行](https://reactjs.org/docs/hooks-effect.html#tip-optimizing-performance-by-skipping-effects)。

在**第二个效果**中，每当值改变时，我们就向工人发送一条消息。

与第一个演示相比，我们可以看到工作人员的性能有了巨大的提升。负载现在由浏览器承担。

这就是在 React 中使用 web workers 的方式。感谢阅读！

继续黑！✌