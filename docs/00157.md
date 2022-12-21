# 了解 JavaScript 函数

> 原文：<https://dev.to/buzzedison/learn-about-javascript-functions-210o>

# 什么是功能？

基本上是一组可重用的代码块，一旦我们调用函数名，它就告诉你的应用程序执行一个特定的任务。

让我们创建一个:
首先创建一个 HTML 文档来显示我们创建的内容。在 html 文档的头部。

```
 (<head>

    Website
    <script src="script.js" defer></script>
</head>
 Then create a button in the body of the document
"button onclick = "clickMe()"
<body>
 <button onclick="clickMe()"> CLICK HERE</button>
 <div id ="click">

 </div>
</body>) 
```

Enter fullscreen mode Exit fullscreen mode

将此另存为 index.html

然后我们在另一个文件中创建函数。让我们称之为 script.js

//基本功能，允许您单击按钮，然后在我们单击按钮时显示文本

```
 function clickMe(){
    me = document.getElementById('click')
    me.innerHTML = "SEE ME HERE"

<code>} </code> 
```

Enter fullscreen mode Exit fullscreen mode

保存文件。一旦你点击按钮，文本应该出现。