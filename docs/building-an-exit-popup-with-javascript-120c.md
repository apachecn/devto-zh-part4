# 用 JavaScript 构建一个退出弹出窗口

> 原文：<https://dev.to/deammer/building-an-exit-popup-with-javascript-120c>

我最近构建了一个系统，当用户要退出一个站点时，它会向用户显示一个弹出窗口。我想我会分享我的方法和我在这个过程中学到的一些技巧。我将坚持使用普通的 JavaScript 来尽可能地让它变得平易近人。

> 免责声明:退出弹出窗口会惹恼用户，所以我建议适度使用它们！

# 标记和样式

让我们为弹出窗口写一些标记:

```
<div id="popup" class="popup__wrapper">
  <div class="popup__container">
    <h1 class="popup__title">Thank you for visiting!</h1>
    <p>Have a wonderful day 💁</p>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

`popup__wrapper`是覆盖页面的透明覆盖图。`popup__container`将包装我们想要显示的内容。

下面是我用的 CSS:

```
* {
  box-sizing: border-box;
}

#popup {
  display: none;
}

.popup__wrapper {
  background: rgba(0, 0, 0, .75);
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
  z-index: 10;
}

.popup__container {
  background: #fff;
  font-family: Helvetica, Arial, sans-serif;
  font-size: 18px;
  margin: 100px auto;
  max-width: 100%;
  padding: 20px;
  width: 500px;
}

.popup__title {
  font-size: 26px;
  margin-bottom: 15px;
} 
```

Enter fullscreen mode Exit fullscreen mode

最重要的一点是`#popup`选择器上的`display: none`:它隐藏内容，直到我们想要显示它。

## 添加功能

现在让我们写几行 JavaScript 来显示鼠标离开窗口时的弹出窗口:

```
function onMouseOut(event) {
  // Remove this event listener
  document.removeEventListener("mouseout", onMouseOut);

  // Show the popup
  document.getElementById("popup").style.display = "block";
}

document.addEventListener("mouseout", onMouseOut); 
```

Enter fullscreen mode Exit fullscreen mode

当你的鼠标离开窗口时，弹出窗口应该会显示出来！

这里有一个 CodePen 供参考:

[https://codepen.io/deammer/embed/GaVqRM?height=600&default-tab=result&embed-version=2](https://codepen.io/deammer/embed/GaVqRM?height=600&default-tab=result&embed-version=2)

# 提炼系统

当用户想离开一个网站时，他们通常会将鼠标移动到浏览器顶部的地址栏或后退按钮。为了让弹出窗口不那么急切，我们可以利用这一点:

```
function onMouseOut(event) {
  // If the mouse is near the top of the window, show the popup
  if (event.clientY < 50) {
    // Remove this event listener
    document.removeEventListener("mouseout", onMouseOut);

    // Show the popup
    document.getElementById("popup").style.display = "block";
  }
}

document.addEventListener("mouseout", onMouseOut); 
```

Enter fullscreen mode Exit fullscreen mode

# 修复火狐漏洞

我注意到当点击或悬停在 Firefox 中的`<select>`元素上时，弹出窗口就会出现。

你可以通过在 DOM 中添加一个下拉菜单并点击它来测试这一点

```
<div id="popup" class="popup__wrapper">
  <div class="popup__container">
    <h1 class="popup__title">Thank you for visiting!</h1>
    <p>Have a wonderful day 💁</p>
  </div>
</div>

<select>
  <option>Oh</option>
  <option>No</option>
</select> 
```

Enter fullscreen mode Exit fullscreen mode

这很讨厌！

经过几个小时的迭代，我得出了一个解决方案:

```
function onMouseOut(event) {
  // If the mouse is near the top of the window, show the popup
  // Also, do NOT trigger when hovering or clicking on selects
  if (
    event.clientY < 50 &&
    event.relatedTarget == null &&
    event.target.nodeName.toLowerCase() !== 'select') {
    // Remove this event listener
    document.removeEventListener("mouseout", onMouseOut);

    // Show the popup
    document.getElementById("popup").style.display = "block";
  }
}

document.addEventListener("mouseout", onMouseOut); 
```

Enter fullscreen mode Exit fullscreen mode

Firefox 中的弹出窗口现在可以正常工作了！这是另一个改进的代码笔:

[https://codepen.io/deammer/embed/EzqyEg?height=600&default-tab=result&embed-version=2](https://codepen.io/deammer/embed/EzqyEg?height=600&default-tab=result&embed-version=2)

* * *

我希望这篇文章是有益的！请在下面的评论里告诉我你的想法，祝你编码愉快！