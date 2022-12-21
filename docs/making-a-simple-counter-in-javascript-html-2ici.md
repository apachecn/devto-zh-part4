# javascript 计数器用 JavaScript 和 HTML 制作一个简单的计数器

> 原文：<https://dev.to/stewyearth/making-a-simple-counter-in-javascript-html-2ici>

# 用 javascript 制作一个简单的计数器& HTML

这是我在这里的第一个帖子！如果有任何困惑，请随时给我写评论，我会尽量更新我的帖子，使其更加清晰！

## 步骤 1 - HTML 设置

从一些简单的 HTML 开始，显示当前计数的 h1 和加减按钮

```
<h1 class="counter-display">(..)</h1>
<button class="counter-minus">-</button>
<button class="counter-plus">+</button> 
```

Enter fullscreen mode Exit fullscreen mode

如果你愿意，你可以为柜台展示另一个标签。我只是在这个例子中使用了一个 h1。

* * *

## 第二步-添加 Javascript

### 选择我们的 DOM 元素

首先，我们将使用`document.queryselector`选择各种元素，并将结果放入一些变量中。

```
let counterDisplayElem = document.querySelector('.counter-display');
let counterMinusElem = document.querySelector('.counter-minus');
let counterPlusElem = document.querySelector('.counter-plus'); 
```

Enter fullscreen mode Exit fullscreen mode

### 计数变量

然后我们将添加一个计数器变量，这样我们就有了一个可以引用的对象。我决定把这个叫做`count`。

```
let count = 0; 
```

Enter fullscreen mode Exit fullscreen mode

这将是我们要加减的变量。

### 添加事件监听器

我们需要将 eventlisteners 添加到我们的元素中，这样我们就可以决定当我们单击 add 或 subtract 按钮时会发生什么。

```
counterPlusElem.addEventListener("click",()=>{

}); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们向 counterPlusElem 添加了一个 eventlistener，它在单击后监听，然后执行一个匿名函数。这对我们的副将也是一样的

### event listeners 的功能

现在，让我们添加添加到我们的计数器的核心功能。

```
counterPlusElem.addEventListener("click",()=>{
    counter++;
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们把计数器变量加 1。您可以通过使用`counter = counter + 1`获得相同的结果

对于我们的负 eventlistener，我们可以使用`counter--`，它与`counter++`相同，但是用减法代替加法。

### 更新我们的显示

为了更新我们的展示，我们有两种可能性。我们可以像这样直接将代码添加到 eventlisteners 中。

```
counterPlusElem.addEventListener("click",()=>{
    counter++;
    counterDisplayElem.innerHTML = count;
}); 
```

Enter fullscreen mode Exit fullscreen mode

选择我们的 counterdisplay 元素，并将其 innerHTML 设置为我们刚刚添加了 1 的 count 变量。

但是因为我们的加号和减号按钮都应该更新我们的显示，我们应该把它变成一个函数来避免冗余。像这样

```
function updateDisplay(){
    counterDisplayElem.innerHTML = count;
}; 
```

Enter fullscreen mode Exit fullscreen mode

我已经决定调用我的函数 updateDisplay，因为它是一个更新我们的显示的函数

现在我们可以调用我们的函数 updateDisplay 了。所以我们的 eventlistener 现在应该是这样的

```
counterPlusElem.addEventListener("click",()=>{
    counter++;
    updateDisplay();
}); 
```

Enter fullscreen mode Exit fullscreen mode

通过使用我们的新函数，我们可以在文档的开头调用它。当我们加载页面时更新它。现在它只会显示`(..)`，直到我们按下两个按钮中的一个。

我们应该在定义了变量和元素之后调用它。像这样。

```
let counterDisplayElem = document.querySelector('.counter-display');
let counterMinusElem = document.querySelector('.counter-minus');
let counterPlusElem = document.querySelector('.counter-plus');

let count = 0;

updateDisplay(); 
```

Enter fullscreen mode Exit fullscreen mode

## 最终代码

如果一切都做得正确，我们最终的代码应该是这样的。

### HTML

```
<h1 class="counter-display">(..)</h1>
<button class="counter-minus">-</button>
<button class="counter-plus">+</button> 
```

Enter fullscreen mode Exit fullscreen mode

### Javascript

```
let counterDisplayElem = document.querySelector('.counter-display');
let counterMinusElem = document.querySelector('.counter-minus');
let counterPlusElem = document.querySelector('.counter-plus');

let count = 0;

updateDisplay();

counterPlusElem.addEventListener("click",()=>{
    count++;
    updateDisplay();
}) ;

counterMinusElem.addEventListener("click",()=>{
    count--;
    updateDisplay();
});

function updateDisplay(){
    counterDisplayElem.innerHTML = count;
}; 
```

Enter fullscreen mode Exit fullscreen mode