# 命令式代码和声明式代码的区别

> 原文：<https://dev.to/abidemit/difference-between-imperative-and-declarative-code-226o>

## 定义和代码示例:

*   **命令性** -实现一个在呈现代码功能时要遵循的过程。一个很好的基本示例:假设在我们的 html 文件中有一个 id 为“btn”的按钮，我们希望向其添加一个事件侦听器。在更改正文背景时，我们的命令性代码将类似于:

```
 <button id="btn">Change bgColor</button>

     let btn = document.querySelector('#btn');
     let parent = document.querySelector('body');
     btn.addEventListener('click', () => {
        parent.style.backgroundColor = "orange";
     }); 
```

Enter fullscreen mode Exit fullscreen mode

```
The above code snippet is an imperative way of solving code issue. Vanilla Javascript style. 
```

Enter fullscreen mode Exit fullscreen mode

*   **声明性的**——依靠方法或包来处理代码功能的执行过程。同样，以声明的方式解决上面的代码问题看起来会像这样。

```
 <button onclick="changeBgColor()">Change bgColor</button>

    <script>
        function changeBgColor() {
            let parent = document.querySelector('body');
            parent.style.backgroundColor = "orange";
        }
    </script> 
```

Enter fullscreen mode Exit fullscreen mode

```
frameworks like React and Vue uses Declarative code and Vanilla old style of adding eventlisteners. 
```

Enter fullscreen mode Exit fullscreen mode

#### 待续

```
Give more intermediate examples. 
```

Enter fullscreen mode Exit fullscreen mode