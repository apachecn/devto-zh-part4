# 通过改变输入类型进行简单的就地编辑

> 原文：<https://dev.to/codepo8/simple-edit-in-place-by-changing-the-type-of-an-input-55p8>

让某些东西在适当的位置可编辑通常是让人们做出改变的最好方法。没有人想要一步就够的三步。以 CodePen 上的这个例子为例。你可以点击文本，编辑它，当你按下回车键(或手机上的回车键)时，它又变回原来的文本。

[https://codepen.io/codepo8/embed/XvEwaw?height=600&default-tab=result&embed-version=2](https://codepen.io/codepo8/embed/XvEwaw?height=600&default-tab=result&embed-version=2)

实现这一点的诀窍是极其简单的努力。你从一个基本形式开始:

```
<form>
  <input type="submit" value="edit me">
</form> 
```

为了使其可编辑而不停留在表单域，您需要做的就是在提交表单时将输入类型从“提交”切换到“搜索”。搜索作为一种类型，在基于 Chromium 的浏览器上也提供了删除按钮的好处。

```
const f = document.querySelector('form');
f.addEventListener('submit', (ev) => {
  let but = f.querySelector('input');
  but.type = (but.type === 'search') ? 'submit' : 'search';
  ev.preventDefault();
}); 
```

很漂亮，不是吗？

对任何元素进行同样操作的另一种方法是切换 contentEditable 属性，如示例的 DIV 中所示。

```
document.querySelector('div').
  addEventListener('click', (ev) => {
  let field = ev.target;
  field.contentEditable = field.contentEditable === true ? false: true;
}); 
```

然而，编辑这些需要一些知识，因为你需要关注下一个项目来结束编辑。或者您需要编写自己的键盘处理代码。