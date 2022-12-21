# 热门 HTML 面试问题及详细答案🍁🔥👌😊🤘第一部分

> 原文：<https://dev.to/theodesp/top-html-interview-questions-with-detailed-answers-part-i-4ib2>

[**阅读原文**](https://codethat.today/tutorial/top-html-interview-questions/part-one/)

你好。我们已经回顾了最有用的 CSS 面试问题，我们可以继续这个系列的 HTML 小冒险和练习。

你可能在想，*嘿，HTML 很简单，对吧？*。不对！HTML 是一个兔子洞，有比 CSS 更难理解的部分。有许多属性、规则和 API 等等。这不仅仅是关于标记，还有管理它们的规则。希望通过这个系列，你可以提前做好准备，揭开 HTML 的一些难点。

不过，我已经做了一些改变。为了您的方便，我将答案放在了每个问题的旁边。然而，你应该能够回答大部分的问题而不作弊，或者至少掌握概念。

一如既往地给你下次技术面试提点建议。试着做自己，不要荒谬。有些事情你可以假装，但大多数你不能。你还想展示你的个性以及你如何适应面试团队，所以要格外小心。

我们开始吧💫

#### 我们在哪里使用了`<video>`元素？

**回答**:`<video>`元素提供了一种无需插件就能在浏览器中播放视频的方法。

#### 一个 HTML5 文档最基本的结构是什么？

**答**:最基本的结构是一个`<!DOCTYPE>`，后面是`html`、`head`和`body`。

#### 下面的 meta 标签是什么意思？

```
<meta charset="utf-8"> 
```

Enter fullscreen mode Exit fullscreen mode

**回答**:这个`charset` meta 标签声明页面的字符集是一个`utf-8`。例如，它将只显示 UTF-8 字符范围。

#### 样式表链接的默认类型是什么？

**回答**:默认是`text/css`。

#### 哪个元素在语义上适合标题、题目或者其他重要细节？

**回答**:那会是`<header>`元素。

#### 我们通常如何使用`<hgroup>`元素？

**回答**:我们将使用一个`<hgroup>`来分组多个 h1-h6 元素，这些元素将形成一个标题。例如:

```
<hgroup>
    <h1>Chapter 1</h1>
    <h4>Once upon a time...</h4>
</hgroup> 
```

Enter fullscreen mode Exit fullscreen mode

#### 我们通常如何使用`<mark>`元素？

**回答**:我们可以使用`<mark>`元素来标记或突出显示一个大文本中的一段文本。例如，当我们在搜索输入中自动完成术语时。

#### 如何在 input 元素中添加占位符文本？

**回答**:通过添加一个`placeholder`属性。

#### 下列元素表示什么？

```
<progress value="25" max="100" min="50"></progress> 
```

Enter fullscreen mode Exit fullscreen mode

**回答**:会显示进度条四分之一填满。min 属性不起作用。

#### 下列元素表示什么？

```
<details>
  <summary>Copyright 2018</summary>
  <p> - by Theo. All Rights Reserved.</p>
  <p>All content and graphics on this web site are the property of Theo.</p>
</details> 
```

Enter fullscreen mode Exit fullscreen mode

**回答**:它会显示一个标题为`Copyright 2018`的可折叠元素，当我们点击标题时，其余的内容就会显示出来。

#### `<canvas>`元素是干什么的？

**回答**:`<canvas>`元素提供了一个无需安装插件就能绘制或操作图像和图形的 API。例如，我们可以创建形状，我们可以定义路径，使用颜色，导出到 png 等。

#### 服务器发送事件和 Websockets 的主要区别是什么？

**答**:主要区别是服务器发送的事件只是从服务器到浏览器的单向，而 Websockets 建立的是双向通信。

#### 如何使 DOM 中的任何元素可编辑？

**回答**:我们可以使用全局`contenteditable="true"`属性来编辑文本上下文。

#### 怎样才能让整张单据的任何一张都可以编辑？

**答**:我们可以使用全局`document.designMode="on"`属性，使整个文档可编辑。

#### 当我们超过了 cookie 存储的最大大小会发生什么？

**回答**:浏览器会开始丢弃旧的 cookiess，为新的 cookie 腾出空间。

#### `Session Storage`和`Local Storage`的主要区别是什么？

**答**:使用`sessionStorage`时，客户端数据只在当前会话期间或每个选项卡中持续存在。当我们打开一个新标签页时，它将重新开始。有了`localStorage`,我们就没有这个限制了。

#### `<output>`元素的主要用途是什么？

**回答**:使用`<output>`元素显示计算结果。它接受类似`form_id`的属性来引用其所属的表单 id。

#### 我们有一个输入字段，我们想添加一个简单的检查来测试规则，例如输入有正确的长度，只包含数字。我们如何用 HTML 做到这一点呢？

**回答**:我们可以用`pattern`属性来指定一个正则表达式。例如:
`pattern="[0-9]{10,16}"`将只允许最少 10 个、最多 16 个数字的数字字符。

#### 我们有一个表单，我们想禁用内置的表单验证效果。我们需要做什么？

**回答**:我们可以在表单上添加一个`novalidate`属性，这样它就可以绕过应用的验证规则。

#### 我们有以下形式:

```
<form action="/submit.php">
  First name: <input type="text" name="fist_name"><br>
  Last name: <input type="text" name="last_name"><br>
  <button type="submit">Submit</button><br>
  <button type="submit">Save for later</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

我们希望能够在单击 Save for later 按钮时提交到不同的端点。我们如何做到这一点？

**回答**:我们可以给按钮添加一个`formaction`属性。例如:

```
<button type="submit" formaction="/save_for_later.php">Save for later</button> 
```

Enter fullscreen mode Exit fullscreen mode

它将发送到该按钮的不同端点。

#### 如何在不使用 Javascript 的情况下，将输入元素的值作为数字获取？

**回答**:在这种情况下，我们需要使用`valueAsNumber`属性，返回一个有效元素的值。

#### [T3`<select>`元素和`<datalist>`元素的主要区别是什么？](#what-is-the-main-difference-between-a-raw-ltselectgt-endraw-element-and-a-raw-ltdatalistgt-endraw-element)

**回答**:一个`<select>`元素提供了一个选项列表，我们需要从中选择，而一个`<datalist>`元素提供了一个选项列表，我们可以选择也可以不选择，因为它们是一个推荐。

#### 你对约束验证 API 了解多少？

**答**:约束验证 API 是一组方法和属性，可以用来检测和修改给定元素的有效性。我们可以用它来应用定制的验证规则或者改变默认的错误消息。

#### `setCustomValidity`法的主要目的是什么？

**答**:我们使用`setCustomValidity`为一个元素的有效性检查设置一个自定义消息。

#### 如何在提交表单时监听无效事件？

**回答**:我们实际上必须使用一个`oninvalid`事件监听器。例如:

```
<input type="text" oninvalid="alert('You must fill your name!');" required> 
```

Enter fullscreen mode Exit fullscreen mode

#### 什么是聚合填充？

**答**:poly fill 是一段代码，模拟浏览器中缺少的一些功能。例如承诺或 es6 方法等。

* * *

### 资源延伸阅读

*   [约束验证 API](https://developer.mozilla.org/en-US/docs/Web/API/Constraint_validation)
*   [HTML5 新标签](https://www.htmlgoodies.com/tutorials/html5/new-tags-in-html5.html)