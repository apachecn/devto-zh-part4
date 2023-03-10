# 使用 localStorage 和 CSS 自定义属性(变量)的主题转换器。

> 原文：<https://dev.to/nicklasspendler/theme-changer-using-localstorage-and-css-custom-properties-variables-2c22>

如果你以前从未使用过 localStorage，请查看 Mozilla docs，我们将使用与图中所示略有不同的语法，但它会让你了解 Localstorage 是如何工作的

首先创建一个样式表并链接到你的 HTML

```
<link rel="stylesheet" href="main.css"> 
```

然后创建一个按钮并给它一个类

```
<button class="changeTheme">Change Theme</button> 
```

然后在 HTML 的底部添加一个脚本标签

```
<script src="main.js"></script> 
```

你的 HTML 现在应该看起来像这样

```
<html>
<head>

    <link rel="stylesheet" href="main.css">

</head>

<body>
    <button class="changeTheme">Change Theme</button>

    <script src="main.js"></script>
</body>
</html> 
```

在 CSS 文件中，我们首先创建变量，并将其赋给我们想要改变颜色的对象。在本例中，我创建了一个名为 themeColor 的变量，并将其用于身体的背景色。themeColor 为空的原因是我们通过 javascript 赋予它属性。我们这样做是为了防止页面闪烁。

```
body{
    --themeColor: ;
    background-color: var(--themeColor);
} 
```

javascript 时间到了，首先为按钮创建一个变量。

```
let themeChangerBtnElem = document.querySelector('.changeTheme') 
```

现在我们想创建本地存储，但在创建之前，我们必须检查它是否已经存在。我们这样做是为了防止覆盖它。

```
if(localStorage.currentTheme == null){
    console.log('currentTheme does not exist', )
    localStorage.currentTheme = "blue"
}else{
    console.log('CurrentTheme does exist', )
    console.log('Localstorage: ', localStorage.currentTheme)
} 
```

然后创建一个 updateUI 函数，每次我们想更新页面时都会调用这个函数，所以记得在代码中的某个地方调用这个函数。我们根据本地存储设置了- themeColor 的属性

```
updateUI();
function updateUI(){
    if(localStorage.currentTheme == "blue"){
        document.body.style.setProperty("--themeColor", "blue")
    }else if (localStorage.currentTheme == "grey"){
        document.body.style.setProperty("--themeColor", "grey")
    } 
```

最后我们给我们的按钮一个 eventListener 来改变本地存储。
如果本地存储器是蓝色的，将其更改为灰色，反之亦然。然后我们调用 updateUI 函数。

```
themeChangerBtnElem.addEventListener('click',()=>{
    if(localStorage.currentTheme == "blue"){
        localStorage.currentTheme = "grey"
    }else if (localStorage.currentTheme == "grey"){
        localStorage.currentTheme = "blue"
    }
    updateUI();
}); 
```

这个教程是一个同学要求的，希望对你有帮助