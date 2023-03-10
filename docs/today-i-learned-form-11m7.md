# 今天我学到了-形式

> 原文：<https://dev.to/dhkamp/today-i-learned-form-11m7>

# 到-形

*2019 年 7 月 12 日*

我需要创建一个页面，其中有一个表单显示和表单按钮放置在标题内，模板是这样的

```
<body>
  <header>
    Headline

    <!-- SUBMIT button should be displayed here -->

  </header>
  <main>
    <form>
      <label for="someinput">Some Label</label>
      <input type="text" id="someinput" name="someinput" />
    </form>
  </main>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

有几种方法可以解决这个问题:

*   使用 CSS
    *   每个人都喜欢玩位置游戏:绝对的，所有的孩子都喜欢左上… -我知道我喜欢
*   将页眉添加到表单

    *   这可能是一个解决方案，但在我的例子中，我正在构建一个 vue 应用程序，而标题 id 是一个可重用的组件，而表单显示在一个**中**

***   使用一个酷的属性是这篇文章的全部内容

    *   使用内置属性可以解决这个问题(在我的例子中就是这样)**

 **## 欢迎表单

form 属性可以用来将一个元素指向它的父表单，而不用进入它的内部

```
<body>
  <header>
    Headline

    <button type="submit" form="myform">Submit</button>
  </header>
  <main>
    <form id="myform" action="myAction.php">
      <label for="someinput">Some Label</label>
      <input type="text" id="someinput" name="someinput" />
    </form>
  </main>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

您希望将按钮绑定到的表单需要有一个 id——就是这样。在你的控件中，在我的例子中是提交按钮，属性表单应该指向你的表单元素的 id。

form 属性可用于各种元素，不仅限于按钮。

## 来源

*   [https://www.impressivewebs.com/html5-form-attribute/](https://www.impressivewebs.com/html5-form-attribute/)
*   [https://developer.mozilla.org/de/docs/Web/HTML/Attributes](https://developer.mozilla.org/de/docs/Web/HTML/Attributes)**