# D 中的注释

> 原文：<https://dev.to/jessekphillips/comments-in-d-2636>

在我继续尝试用 Python 写作的过程中，我希望注释掉一些代码。我尝试了各种风格，包括:但是我仍然不得不寻找，因为我已经忘记了#

d 跟在 C++注释后面。

```
// single line
/*
  Multi-line
*/ 
```

Enter fullscreen mode Exit fullscreen mode

它在嵌套注释方面更进一步。

```
/*
/*
  Multi-line
*/
This is not a comment 
*/

/+
/+
  Multi-line
+/
We are still a comment
+/ 
```

Enter fullscreen mode Exit fullscreen mode

如果您在注释开始处添加了一个字符，那么这些注释就有资格生成文档。

```
/// Document comment
/** doc comment */
/++ comment for docs +/ 
```

Enter fullscreen mode Exit fullscreen mode

Well D 并没有就此止步，它提供了一种不同的方法来阻止代码编译。

```
version (none) {
    auto var = 85;
} 
```

Enter fullscreen mode Exit fullscreen mode

它仍然通过解析器运行，但是不需要成功编译。