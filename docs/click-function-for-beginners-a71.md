# 初学者的点击功能

> 原文：<https://dev.to/rtsjesp95/click-function-for-beginners-a71>

在本教程中，我将教你点击功能。只是一个免责声明，我只打算过一下使用点击功能的基础，因为这是一个非常广泛的深入的主题。

还有一张小纸条:

您不必给 id、类和变量起和我一样的名字。你可以给它们起任何你想要的名字，我只是选择保持我的名字简单

首先，在代码编辑器(我使用 Visual Studio 代码)中设置一个 HTML 文档，在主体中有 3 个按钮，并给它们一个 ID:

```
<button id="button1">Button 1</button>

<button id="button2">Button 2</button>

<button id="button3">Button 3</button> 
```

**第二步:**

准备一个 JS 文档，为所有按钮创建变量。像这样:

```
document.addEventListener('DOMContentLoaded', ()=>{
    let button1 = document.querySelector('#button1');
    let button2 = document.querySelector('#button2');
    let button3 = document.querySelector('#button3');
}) 
```

DOMContentLoaded 事件在初始 HTML 文档完全加载和解析后触发，无需等待样式表、图像和子框架完成加载。

**第三步:添加点击功能**

现在，您可以使用下面的代码向其中一个按钮添加点击功能:

```
button1.addEventListener('click', ()=>{
    console.log('You clicked Button 1')
}) 
```

使用 console.log 的原因是为了确保您单击了正确的元素。

现在我们可以将它添加到剩下的两个按钮

```
button2.addEventListener('click', ()=>{
    console.log('You clicked Button 2')
})

button3.addEventListener('click', ()=>{
    console.log('You clicked Button 3')
}) 
```

**第四步:让按钮改变背景颜色**

要使其改变背景颜色，请执行以下操作:

```
button2.addEventListener('click', ()=>{
    button2.style.backgroundColor = 'blue'
}) 
```

这将改变按钮 2 的背景颜色为蓝色

现在假设你想把颜色变回原来的颜色。在按钮后创建一个变量，如下所示:

```
let button1 = document.querySelector('#button1');
let button2 = document.querySelector('#button2');
let button3 = document.querySelector('#button3');
let isColored = false; 
```

创建 isColored variabel 的原因是要有一些东西，这样我们就可以检查按钮的颜色是否改变了

现在在 click 函数中添加一个 if 和 else 语句:

```
button2.addEventListener('click', ()=>{
    if(isColored){
        button2.style.backgroundColor = 'buttonface'
        isColored = false;        
    }else{
        button2.style.backgroundColor = 'green'
        isColored = true;
    }
}) 
```

if 语句检查按钮是否改变了颜色。现在它是假的，所以它有按钮的默认颜色。如果你点击这个按钮，它会变成绿色。如果成功了，isColored 变量现在为真，这意味着颜色实际上已经改变了。如果您再次单击按钮，它应该会恢复到默认颜色(“buttonface”是按钮元素的默认颜色值)

**第五步:与 classList.add/remove 合作**

你也可以对 classList.add/remove.做同样的事情，在这种情况下，我想给其中一个按钮添加一些填充和空白，并使文本加粗。将为这个示例创建第四个按钮。还将创建一个变量来跟踪样式，看它是否已经改变。这样就不会和我们的其他代码混淆了。让我们来看看 CSS:

```
.padding-margin-bold{
    padding: 10px;
    border-radius: 15px;
    margin: 10px;
    font-weight: bold;
} 
```

你也可以随意称呼你的班级。

我添加了一些填充、空白、边框半径和字体粗细。现在通过 JavaScript 添加这个类。让我们继续看 JavaScript 文件。确保已经在 HTML:
中创建了一个指向第四个按钮的变量

```
let styleChanged = false;
let button4 = document.querySelector('#button4'); 
```

当改变背景颜色时，我使用了前面例子中的一些相同的代码，我使用的是 classList 而不是 style。下面是例子:

```
button4.addEventListener('click', ()=>{
    if(styleChanged){
        button4.classList.remove("padding-margin-bold")
        styleChanged = false;        
    }else{
        button4.classList.add("padding-margin-bold")
        styleChanged = true;
    }
}) 
```

现在，当您单击按钮时，应该会添加该类，当您再次单击它时，应该会删除该类

感谢您通读我的教程:)

希望它能帮助你更好地理解点击功能

以下是完整示例的 CodePen 链接:

[https://codepen.io/jesp258/pen/VwZMeBR](https://codepen.io/jesp258/pen/VwZMeBR)

小纸条:

在上面的 CodePen 示例中，我为所有按钮设置了唯一的“false”变量，这样它们就不会在代码中相互混淆