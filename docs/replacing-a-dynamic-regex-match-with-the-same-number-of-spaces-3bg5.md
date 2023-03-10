# 用相同数量的空格替换动态正则表达式匹配

> 原文：<https://dev.to/raymondcamden/replacing-a-dynamic-regex-match-with-the-same-number-of-spaces-3bg5>

这篇文章 100%感谢我的朋友彼得·库珀。我在网上找不到任何解决方案(或者我可能搜索得不好)，所以我想写下来，以防其他人有同样的问题。假设你有一个 HTML 字符串，你想删除标签。一个简单的解决方案应该是这样的:

```
let s = `
<p>
This is Ray and I'm <i>very</i> cool. I sometimes am <b><i>super</i></b> cool!
</p> 
```

Enter fullscreen mode Exit fullscreen mode

这是糟糕的代码！
和许许多多

```
 <ul>
<li>more</li>
<li>more</li>
</ul>
`.trim();

// replace all html
s = s.replace(/<.*?>/g,'');
console.log(s); 
```

Enter fullscreen mode Exit fullscreen mode

这工作得非常好，但是我的情况有点不同。我需要将结果传递给一个报告拼写错误的工具。当它这样做时，它会报告行号和列。在我最初的解决方案中，字符串不再像以前那样在相同的空格中包含文本。很接近了，但是在一个大文件中，这种差异在接近结束时变得更大。

所以我的问题是——给定一个大小动态的正则表达式(`<.*?>`)，有没有办法用相同长度的空格字符替换？

当我寻找解决方案时，我关注的是某种可能有所帮助的正则表达式。结果答案很简单。正如 Peter 指出的那样， [replace](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace#Specifying_a_function_as_a_parameter) 函数允许您为替换或函数指定一个子串。向该函数传递匹配的字符串(以及其他参数),以便您可以轻松地检查长度并返回正确的空格数。这里有一个例子:

```
s = s.replace(/<.*?>/g,function(match) {
  return " ".repeat(match.length);
}); 
```

Enter fullscreen mode Exit fullscreen mode

彼得的解决方案实际上更简洁一些。我热爱箭头函数，但在教学时，我还是喜欢先展示“老”的方式。我还记得当 arrow 函数的语法把我搞糊涂的时候:

```
s = s.replace(/<.*?>/g, _=> ' '.repeat(_.length) ); 
```

Enter fullscreen mode Exit fullscreen mode

您可以在下面的代码笔中测试这一点。

[https://codepen.io/cfjedimaster/embed/mZGEVo?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/mZGEVo?height=600&default-tab=result&embed-version=2)

反正就是这样，希望这有帮助！这也是我第一百万次提醒我的读者，MDN 网络文档是互联网上最好的资源。

*由[弗洛里安·奥利佛](https://unsplash.com/@rxspawn?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 Unsplash 上拍摄的标题照片*