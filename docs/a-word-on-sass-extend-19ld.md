# 关于 Sass @extend 的一句话

> 原文：<https://dev.to/iangloude/a-word-on-sass-extend-19ld>

是一个强大的 SASS 工具，有助于保持代码整洁，但是如果使用不当，它也会有缺点。在类或 placehodler 上调用`@extend`会将*扩展的*类添加到*扩展的*类的选择器列表中。举这个例子:

```
.message {
  border: 1px solid black;
  color: black;
  padding: 10px 5px;
}

.success {
  @extend .message;
  border-color: green;
}

.warn {
  @extend .message;
  border-color: yellow;
}

.fail {
  @extend .message;
  border-color: red;
} 
```

我们创建了一个`.message`类，并将其扩展到其他几个类中，只对`border-color`做了一点修改。编译时，我们得到:

```
.message,
.success,
.warn,
.fail {
  border: 1px solid black;
  color: black;
  padding: 10px 5px;
}

.success {
  border-color: green;
}

.warn {
  border-color: yellow;
}

.fail {
  border-color: red;
} 
```

太好了！我们得到了非常有效的干货。然而，不恰当地使用`@extend`会留下大量你不需要的选择器。比如:

```
.foo {
  .bar {
    .message {
      color: black;
      border: 1px solid black;
      padding: 10px 5px;
    }
  }
}

.baz {
  .buzz {
    .error {
      @extend .message;
      border-color: red;
    }
  }
} 
```

会导致:

```
.foo .bar .message,
.foo .bar .baz .buzz .error,
.baz .buzz .foo .bar .error {
  color: black;
  border: 1px solid black;
  padding: 10px 5px;
}

.baz .buzz .error {
  border-color: red;
} 
```

呀。值得这么麻烦吗？

真正的答案是...也许吧？如果你遇到了选择器问题，考虑使用 mixin。但是如果你完全理解`@extend`是如何工作的，并且有一个突出的用例，那就太好了！*加油吧！*否则，仅仅为了使用样式表而强迫使用它可能不值得。