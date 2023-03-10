# 如果不确定，请使用按钮

> 原文：<https://dev.to/vhoyer/na-duvida-use-um-botao-32gj>

好吧，我明白你说无障碍是困难的，真的，但我认为，即使是困难的，我们也必须关注它，因为我们正在使信息不能被某些人使用；你为什么不想让这些人习惯你的内容？你有偏见，是吗？我希望不是

所以我们应该总是尽力让我们的网站尽可能便宜。这就是为什么我来这里运用我所有的知识尝试教你如何做一个经济实惠的按钮！

## 我这里有这个按钮，怎么让它无障碍？

```
<style>
.my-button {
  outline: none;
  background: hsl(80deg, 50%, 50%);
  color: white;
  display: inline-block;
  padding: 4px 16px;
}
.my-button:hover {
  background: hsl(80deg, 50%, 30%);
}
.my-button:active {
  background: hsl(80deg, 50%, 60%);
}
</style>
<div class="my-button">
  Click me
</div>
<script>
document.querySelector(".my-button").addEventListener('click', () => alert("poke!"))
</script> 
```

Enter fullscreen mode Exit fullscreen mode

> 首先，我只想给*免责声明*按钮不丑，你不了解它的设计所带来的微妙艺术，艺术是:写代码-踢-数字-看结果-写完-你的

### 首先！

嗯，孩子，看，被认为是**糟糕**的做法`outline: none`。如果不能避免的话，就和负责人商量不要让你把`none`从元素中抽出来，这样这个人就能找到足够好的代替品，有法律对比让你放进去

```
@@ -1,6 +1,5 @@
 <style>
 .my-button {
-  outline: none;
   background: hsl(80deg, 50%, 50%);
   color: white;
   display: inline-block; 
```

Enter fullscreen mode Exit fullscreen mode

### 第一件事

我注意到的第一个问题是:如果用户只使用键盘浏览您的网站—不管是瘫痪、手臂骨折还是用另一只手拿着三明治—该用户将无法按下此按钮，因为它不是“*”焦点*。试着只使用“`Tab`”来选择此按钮。

要解决此问题，可以在 HTML 中明确定义“`.my-button`”以:`tabindex="0"`为焦点。值为 0 的 [tabindex](https://developer.mozilla.org/pt-BR/docs/Web/HTML/Global_attributes/tabindex) 的类型，是用来告诉*浏览器*这个元素可以**【是】**为焦点，而不管使用哪种*标签。* 

```
@@ -11,6 +11,6 @@
   background: hsl(80deg, 50%, 60%);
 }
 </style>
-<div class="my-button"> +<div class="my-button" tabindex="0">
   Click me
 </div> 
```

Enter fullscreen mode Exit fullscreen mode

### 第二件事

当屏幕阅读器阅读此元素(现在它是焦点)时，它会阅读“Click me，generic container(单击我，通用容器)”之类的东西，有时只需“Click me(单击我)”。为什么这个按钮带来的元素是‘t0’，给我们用来绕过这个问题的技巧可能如下:

```
@@ -11,6 +11,6 @@
   background: hsl(80deg, 50%, 60%);
 }
 </style>
-<div class="my-button" tabindex="0"> +<div class="my-button" tabindex="0" role="button">
   Click me
 </div> 
```

Enter fullscreen mode Exit fullscreen mode

现在有了[属性`role`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Roles/button_role) 屏幕阅读器可以知道，这个`div`实际上是在扮演按键角色，然后会读作:“点击我，按键”。

### 第三件事

将`onClick` *处理程序*放入`div`时，当用户单击时会触发，但如果用户不使用鼠标，则等待该按钮的动作(该按钮的焦点是使用`Tab`触发)

*   `Enter`
*   `Espaço`

这些都是必须满足的期望，否则系统可能会变得无法使用。所以为了纠正这一点，我们必须在他的
上按下这个按钮的动作:

```
@@ -17,5 +17,13 @@
   Click me
 </div>
 <script>
-document.querySelector(".my-button").addEventListener(() => alert("poke!")) +const myButtonAction = () => alert("poke!")
+const myButton = document.querySelector(".my-button")
+
+myButton.addEventListener('click', myButtonAction)
+myButton.addEventListener('keydown', (event) => {
+  if (["Enter", " ", "Spacebar" /*ie11*/].includes(event.key)) {
+    event.preventDefault() // para evitar que a página seja scrollada (spacebar)
+    myButtonAction()
+  }
+})
 </script> 
```

Enter fullscreen mode Exit fullscreen mode

它有一个 WAI-ARIA 页面，列出了给定元素的所有行为，不管它是一个*按钮*、*复选框*、*滑块*、*列表*、*工具栏*它们必须符合特定用户的期望，即‘T10’【wcag 1.1 创作实践】，这是一份经过多次修订的 1999 年文件，而且我相信还在继续，WAI-ARIA 已经推出 WCAG 2.0 和 WCAG 2.1，但它们不一定比 1.1 更好

如果你看着这些规格表的文件，并有一段，还有另一个网站叫做[web im:WCAG 2 Checklist](https://webaim.org/standards/wcag/checklist)，它把 wcag 变成了一个更友好的东西，把它的标准呈现为 check list。

即便如此，在我卑微的看来，要找到这些信息，最简单、结构完整、最完整的地方就是‘t0’MDN’，我刚发现这和我现在举的例子完全一样！伙计 MDN 太棒了。

### 最后我们拿着它:

```
<style>
.my-button {
  background: hsl(80deg, 50%, 50%);
  color: white;
  display: inline;
  padding: 4px 16px;
}
.my-button:hover {
  background: hsl(80deg, 50%, 30%);
}
.my-button:active {
  background: hsl(80deg, 50%, 60%);
}
</style>
<div class="my-button" tabindex="0" role="button">
  Click me
</div>
<script>
const myButtonAction = () => alert("poke!")
const myButton = document.querySelector(".my-button")

myButton.addEventListener('click', myButtonAction)
myButton.addEventListener('keydown', (event) => {
  if (["Enter", "  ", "Spacebar" /*ie11*/].includes(event.key)) {
    event.preventDefault() // para evitar que a página seja scrollada (spacebar)
    myButtonAction()
  }
})
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 好吧，但这看起来很复杂！

真的吗？但你知道有别的方法能让上面提到的所有东西都能用得上吗？如:

```
<style>
.my-button {
  background: hsl(80deg, 50%, 50%);
  border: none;
  color: white;
  padding: 4px 16px;
}
.my-button:hover {
  background: hsl(80deg, 50%, 30%);
}
.my-button:active {
  background: hsl(80deg, 50%, 60%);
}
</style>
<button class="my-button">
  Click me
</button>
<script>
document.querySelector(".my-button").addEventListener('click', () => alert("poke!"))
</script> 
```

Enter fullscreen mode Exit fullscreen mode

上面的`div`按钮都有，只是你不用做什么别的，呵呵。而 *diff* 最后变成:

```
@@ -1,21 +1,20 @@
 <style>
 .my-button {
-  outline: none;
   background: hsl(80deg, 50%, 50%);
+  border: none;
   color: white;
-  display: inline-block;
   padding: 4px 16px;
 }
 .my-button:hover {
   background: hsl(80deg, 50%, 30%);
 }
 .my-button:active {
   background: hsl(80deg, 50%, 60%);
 }
 </style>
-<div class="my-button"> +<button class="my-button">
   Click me
-</div> +</button>
 <script>
 document.querySelector(".my-button").addEventListener('click', () => alert("poke!"))
 </script> 
```

Enter fullscreen mode Exit fullscreen mode

## 但是如果我用`<a>`呢？

如果*标签*没有`href`属性，基本上被认为是 *div* 的话，那么你把`<div>`变成按钮所做的一切工作，对于*标签也会是一样的**