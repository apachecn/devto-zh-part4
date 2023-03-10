# Javascript 中的同步与异步编程

> 原文：<https://dev.to/adrianmejias/synchronous-vs-asynchronous-programming-in-javascript-20o8>

## 同步和异步编程有什么区别？

同步和异步的区别在于执行的方式。例如，在 javascript 中，代码的执行是按顺序从上到下进行的。每一项都被执行并继续到下一项，但直到前一项完成。

*   叫做一个()..2 秒执行时间
*   叫做二()..4 秒的执行时间(我们必须在调用下一个方法之前等待)
*   叫做三()..2 秒执行时间

**页面数据加载:** 8 秒

但是，您可以运行一段异步代码，而不会出现堆栈阻塞。例如，按钮标签上的 onclick 事件会被认为是异步的，因为它不会阻止其他代码的执行。

*   叫做一个()..2 秒执行
*   叫做二()..2 秒执行时间+ 2 秒等待服务器后台调用(我们不需要等待调用下一个方法)
*   叫做三()..2 秒执行时间
*   异步二()..我们现在能够使用来自两个()的数据

**页面数据加载:** 6 秒(比后台异步调用节省 2 秒)

下面的例子将向你展示一个明显的区别。请记住，javascript 还有其他承诺形式的异步特性。

### HTML 源文件(非高级示例，btw)

```
<!DOCTYPE html>
    <html>
        <head>
            Synchronous vs Asynchronous Example in Javascript
        </head>
    <body>
        <h1>Synchronous vs Asynchronous Example in Javascript</h1>
        <h3>Synchronous</h3>
        <div id="sync-data"></div>
        <h3>Asynchronous</h3>
        <div id="async-data"></div>
        <button id="async-button">Async Click</button>
        <script>
            (() => {
                let runCommand = (elementId, identifier, timeout = -1) => {
                    console.log('runCommand', elementId, identifier, timeout)
                    if (timeout > -1) {
                        console.log('Waiting for data...', identifier, timeout + ' second delay')
                        window.setTimeout(() => {
                            runCommand(elementId, identifier)
                        }, timeout * 1000)
                    } else {
                        let dataNode = document.createElement('div')
                        let textNode = document.createTextNode('Run method ' + identifier + ' was called')
                        dataNode.appendChild(textNode)
                        document.getElementById(elementId).appendChild(dataNode)
                    }
                }
                // @type sync
                // @stack 1, 2, 3, 4
                for (let i = 1; i <= 4; i++) {
                    runCommand('sync-data', i)
                }
                // @type async
                // @stack 1, 2, 4, 3
                runCommand('async-data', 1)
                runCommand('async-data', 2)
                runCommand('async-data', 3, 2) // call after 2 seconds, continue to call next tick
                runCommand('async-data', 4)
                // @type async
                document.getElementById('async-button').addEventListener('click', () => {
                    console.log('async-button clicked')
                    let dataNode = document.createElement('div')
                    let textNode = document.createTextNode('Button was called without interruption of other async threads')
                    dataNode.appendChild(textNode)
                    document.getElementById('async-data').appendChild(dataNode)
                })
            })()
        </script>
    </body>
</html> 
```

有关 javascript 中 async/await 的更多信息，请查看本文中的动画:[在 7 秒钟内理解 Async/Await](https://dev.to/wassimchegham/understanding-async-await-in-7-seconds-3g0f)