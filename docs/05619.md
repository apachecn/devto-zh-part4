# 开发人员评论和帖子的格式代码

> 原文：<https://dev.to/chrisachard/formatting-code-for-dev-comments-and-posts-2kmp>

你有没有想过如何在 dev.to 上的评论和帖子中获得漂亮的代码格式？

这里有一个例子:

```
const myMethod = (a, b) => {
   return a + b
} 
```

诀窍是所有开发人员的评论和帖子都接受 markdown！因此，我们可以使用 markdown 代码语法突出显示方法，将代码包装在三个反斜杠中，如下所示:

```

```
const myMethod = (a, b) => {
   return a + b
}
```

```

然而，这只能让我们成功一半。将会输出这个:

```
const myMethod = (a, b) => {
   return a + b
} 
```

为了突出语法，我们还需要在反斜线后面指定我们正在编写的语言。在这种情况下，这就是 javascript:

```

```javascript
const myMethod = (a, b) => {
   return a + b
}
```

```

而现在我们有:

```
const myMethod = (a, b) => {
   return a + b
} 
```

万岁！

## 几个值得注意的支持词汇

DEV 使用 [rouge-ruby](https://github.com/rouge-ruby/rouge) 来做语法高亮显示，所以我们可以检查这些文档来获得[支持语言的完整列表](https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers)

列表中有几个有趣的词汇，比如`jsx`(不同于`javascript` ):

```
const myVariable = "abc"
return <p>{myVariable}</p> 
```

和`diff`，如果有一个`-`、`+`或空格作为第一个字符，它将把每一行涂成红、绿或白:

```

```diff
+ const newMethodName = (a, b) => {
- const myMethod = (a, b) => {
    return a + b
 }
```

```

给了我们:

```
+ const newMethodName = (a, b) => { - const myMethod = (a, b) => {
    return a + b
 } 
```

## 我想不明白的事情

有几件我想做的事情我想不出来，所以如果有人知道如何实现这些，我洗耳恭听！

**1。突出显示某些行**

一些 markdown 处理器允许您添加行号来突出显示代码块；但据我所知，胭脂默认没有那个能力。

**2。添加额外样式**

当我不能让线条突出显示工作时，我想我会尝试将我自己的 css 样式添加到 markdown 中来实现这一点——但是我也不知道如何让它工作。我相信 rouge 在渲染之前会从 markdown 中去掉所有的风格和其他属性。

**3。行号**

似乎没有办法让行号显示出来。我能想到的唯一真正的方法是把它们作为一行的第一个字符直接添加到代码中，就像这样:

```

```javascript
1 const myMethod = (a, b) => {
2    return a + b
3 }
```

```

这确实有效:

```
1 const myMethod = (a, b) => {
2    return a + b
3 } 
```

但是它确实增加了一个额外的步骤。

## 我是如何在这篇文章中展示反勾号的

好的，如果三个反勾号自动开始一个代码块，那么我如何在这篇文章中显示它们呢？

```

```javascript
console.log("WAT")
```

```

诀窍是首先将它们包装在一个`<pre>` HTML 标签中——然后这个标签会将里面的内容作为一个块本身呈现出来。看起来是这样的:

```
<pre>

```javascript
console.log("WAT")
```

</pre>
```

现在有一些事情需要思考...我如何让`<pre>`和反勾号都出现在那个块中？这不仅仅是用另一个`<pre>`包起来那么简单...来吧-尝试一下:)

🤯

希望有所帮助！

**喜欢这个帖子？**
你可以通过:

*   在推特上关注我: [@chrisachard](https://twitter.com/chrisachard)
*   加盟快讯:[chrisachard.com](https://chrisachard.com/)

感谢阅读！