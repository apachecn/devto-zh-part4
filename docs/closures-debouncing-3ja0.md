# 闭合:去抖动

> 原文：<https://dev.to/bbarbour/closures-debouncing-3ja0>

在本系列的前一篇文章中，我们研究了闭包的一个实际应用，称为 memoization。在这篇文章中，我们将通过一个去抖函数来简要地看一下闭包。

去抖会将功能的处理延迟一段时间。它对于像表单、按钮或鼠标事件这样的事情非常有用——只要有大量的用户输入。用游戏术语来说，它可以通过引入“冷却”期来帮助防止 DOM 的快速重新渲染。

```
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Practice
</head>
<body>
    <button id="button">Click Me</button>

    <script>
        const debounce = (func, delay) => {
            let inDebounce
            return function () {
                const context = this
                const args = arguments
                clearTimeout(inDebounce)
                inDebounce = setTimeout(() => func.apply(context, args), delay)
            }
        }

        const button = document.getElementById('button')
        button.addEventListener('click', debounce(() => {
            console.log('Hey! It is', new Date().toUTCString())
        }, 3000))
    </script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我在这里的目标不是教你如何写去抖。这里有很多资源(你可以复制粘贴一个通用的去抖函数，就像我在这里做的那样)。更重要的是，我希望你注意到那里的结束，并且有那个“啊哈”的时刻。如果你想要一个指南或者深入了解这个函数，请查看我使用的[源](https://codeburst.io/throttling-and-debouncing-in-javascript-b01cad5c8edf)。

从闭包的角度来看，这里的关键是我们的`inDebounce`变量。作为一个闭包，返回的函数“记住”变量是否超时。如果我们在它还在超时的时候启动它，它会`clearTimeout`，从而重启它。

另一个实际的闭包实例。

你们以前用过去抖吗？有没有不同的变体或用例是非常有用的？