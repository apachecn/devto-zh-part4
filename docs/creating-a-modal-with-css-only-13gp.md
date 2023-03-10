# 仅使用 CSS 创建模式

> 原文：<https://dev.to/cadienvan/creating-a-modal-with-css-only-13gp>

## 【TL；博士]这是演示:[代码笔](https://codepen.io/Cadienvan/pen/eqYWOQ)

### 如果你还没有...

看一看我的第一篇文章来正确理解这个系列的前提:[只用 CSS 创建一个横向菜单](https://dev.to/cadienvan/creating-a-lateral-menu-with-css-only-3mmp)

### 走吧...

正如我在上一篇文章中所做的那样，为了简单起见，我限制了 UI 和 UX 的工作量，以提供一个简单易用、易于解释和理解的解决方案。

### HTML...

简单的标记，不多解释了:

```
<!DOCTYPE html>
<html>
  <body>
    <!-- This will trigger our modal via CSS selectors -->
    <input type="checkbox" class="checkbox-modal-toggle" id="IDMODAL" />
    <!-- Here we have our centered modal -->
    <div class="modal">Lorem Ipsum Dolor Sit Amet Consectetur</div>
    <!-- Here we have our closing label which is, in fact, the modal overlay -->
    <label for="IDMODAL" class="modal-overlay"></label>
    <!-- Some fake page content -->
    <p>Lorem Ipsum Dolor Sit Amet Consectetur</p>
    <!-- Here we have our modal trigger. I'm using the class just to put some style on it -->
    <label class="modal-trigger" for="IDMODAL">Click Here To Trigger a Modal</label>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### CSS...

正如我在之前的文章中提到的，CSS 非常简单，因为事实上，我们只是使用 CSS 相邻选择器("+")来获得一个工作的 CSS 唯一模态:

```
html, body {
  margin: 0;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

body {
  padding: 8px;
}

.modal {
  position: fixed;
  z-index: 2;
  display: none;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translateX(-50%) translateY(-50%);
  width: 300px;
  height: 300px;
  background-color: white;
  color: black;
  text-align: center;
  padding: 6px;
  transition: opacity 3000ms ease-in-out;
}
.checkbox-modal-toggle:checked + .modal {
  display: block;
}

.modal-overlay, .checkbox-modal-toggle + .modal + label {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1;
  display: none;
  background-color: #000000;
  opacity: 0.4;
  cursor: pointer;
}
.checkbox-modal-toggle:checked + .modal + .modal-overlay,
.checkbox-modal-toggle:checked + .modal + label {
  display: block;
}

.checkbox-modal-toggle {
  margin: -1px;
  padding: 0;
  width: 1px;
  height: 1px;
  overflow: hidden;
  clip: rect(0 0 0 0);
  clip: rect(0, 0, 0, 0);
  position: absolute;
}

.modal-trigger {
  border-radius: 30px;
  padding: 8px;
  background: #0045ff;
  color: white;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以你有它..通过点击 trigger 标签，我们选中了我们的 invisible 复选框，因此，几乎不费吹灰之力就允许我们的 modal 出现在视图中。

## 提高一点..

一个非常简单的模态可以按照你想要的方式来制作动画和样式。我们可以让它变得不透明，让它从四面八方掉下来，或者让它做我们想做的任何事情。我不会更进一步，让你用它做实验！

### JS...

这是我的第二个帖子，也是你第二次在这个上面摔倒。没有 JS！

### 结论...

正如前一个，这是一个很好的实验，我喜欢实验和尝试新事物。正如我已经说过的，JS 中一个简单的类切换可以使我们的生活变得更容易，但是以不同的方式和从不同的角度思考 CSS 可以帮助你构建一个更好的 web。

我希望你喜欢我的帖子...回头见！