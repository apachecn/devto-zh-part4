# 异步流...带反应钩！

> 原文：<https://dev.to/silvestricodes/asynchronous-flows-with-react-hooks-1g0m>

前几天在工作中，我有机会实现一些异步数据流，我很乐意与大家分享我的方法。

## 思想过程

每当我处理加载和显示异步数据时，我喜欢将数据加载工作和数据显示工作分成两个部分。对我来说，这种关注点的分离帮助我专注于一个干净的、容易遵循的逻辑树。

## 设置我们的装载机

下面是我们希望加载组件处理的内容:

*   当组件挂载时，我们应该触发 api 调用来获取数据。
*   当这个 api 调用触发时，我们应该设置某种加载状态。
*   当 api 调用完成时，我们应该将数据设置为 state，并指示加载已经完成。
*   我们应该将这些数据传递给其他组件。

基于这个列表，我们需要两部分:状态加载和数据。我们还需要想出如何钩入我们的组件的安装。让我们从用 [useState 钩子](https://reactjs.org/docs/hooks-state.html)设置我们的状态开始。

```
 import React, { useState } from 'React'
  import Breakfast from './Breakfast' // I utilize breakfast foods as my foo/bar/biz/baz

  const DataLoader = () => {
    const [ isLoading, setIsLoading ] = useState(false)
    const [ data, setData ] = useState([])

    return isLoading ? <div>Loading</div> : <Breakfast data={data} />
  } 
```

好了，状态设置好了！现在我们需要进行 API 调用。我将把它分成一个新的部分，以便于理解。

## 使用效果

[useEffect](https://reactjs.org/docs/hooks-effect.html) 是我们处理装载和更新的方式。这个函数让我们捕捉函数组件中的副作用以供使用。TL；博士的文档可以在这里找到:

```
 useEffect(callback, dependencyArray) 
```

useEffect 可以通过两种方式触发:每当组件挂载时，以及每当 dependencyArray 中的值发生变化时。如果您传递一个空数组作为第二个参数，它将确保 useEffect 只在您的组件挂载时运行。

我们将在 useEffect 中使用异步函数。值得注意的是——我们不能让回调函数异步，因为 useEffect 要么返回一个清理函数，要么什么都不返回。稍后您将看到我使用了 [async/await](https://javascript.info/async-await) 方法进行承诺声明。异步函数隐式地返回一个承诺，所以如果没有一个时间点，你可以解析现在是承诺化的 useEffect，那么一切都完了！但是，在 useEffect 中使用异步函数完全没问题。

```
-  import React, { useState } from 'React' +  import React, { useState, useEffect } from 'React'
   import Breakfast from './Breakfast'

  const DataLoader = () => {
    const [ isLoading, setIsLoading ] = useState(false)
    const [ data, setData ] = useState([])

+   useEffect(() => {
+     async function fetchData() {
+       setIsLoading(true)
+       const fetcher = await window.fetch(/some/endpoint)
+       const response = await fetcher.json()
+       setData(response)
+       setIsLoading(false)     
+     }
+     fetchData()
    }, [])

    return isLoading ? <div>Loading</div> : <Breakfast data={data} />
  } 
```

下面是上述函数的工作原理:

*   如果依赖关系数组为空，此 useEffect 将仅在装载时运行。
*   当组件挂载时，运行 fetchData。
*   触发我们的装载状态。利用[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) (我们做到了！！！)来解决一个让我们得到回应的承诺。
*   使用`.json`函数解析响应来解析承诺。
*   将我们的数据状态设置为这个响应，并将我们的加载状态设置为 false。

在状态变化的每一点，我们将使用适当的 UI 重新呈现。

这就是我们的装载机！就 React 组件而言，接收我们的数据的组件是非常标准的，所以我不会担心示例中的这一部分。

## 改进

### 错误处理

我们还可以用 useEffect 设置做更多的事情。先说错误处理。

`Async/Await`非常适合[尝试/抓住/最终](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/try...catch)阻挡，所以让我们试一试。让我们提取 useEffect 的内部部分，并在其中添加 try/catch/finally。

```
 async function fetchData() {
       setIsLoading(true)
+      try {
        const fetcher = await window.fetch(/some/endpoint)
        const response = await fetcher.json()
        setData(response)
+      } catch (error) {
+        // Do something with error
+      } finally {
+        setIsLoading(false)   
+      }  
     }
     fetchData() 
```

`try`部分将尝试进行我们的 API 调用。如果出现任何错误，我们将陷入 catch 语句。在这两个都完成之后，不管结果如何，我们点击 finally 块并清除我们的加载状态。

### 清理

处理组件卸载的情况是一个好主意，这样我们就不会继续设置状态。`useEffect`支持组件卸载时运行的清理功能。让我们在中添加该功能。

```
 useEffect(() => {
+    let didCancel = false
     async function fetchData() {
+      !didCancel && setIsLoading(true)
       try {
        const fetcher = await window.fetch(/some/endpoint)
        const response = await fetcher.json()
+       !didCancel && setData(response)
       } catch (error) {
           // Do something with error
       } finally {
+       !didCancel && setIsLoading(false)   
       }  
     }
     fetchData()
+    return () => { didCancel = true }
    }, []) 
```

我们添加的返回函数将在组件卸载时运行。这将设置 didCancel 为真，并确保所有状态仅在`didCancel`为假时设置。

## 最后的话

这篇文章中有很多东西需要解开。然而，我想把这个想法写在纸上。我知道其他人已经就这个主题写了更深入的文章，但希望这概括了利用异步的 useEffect 的挑战部分。如果有任何问题，请随时在下面发表评论！