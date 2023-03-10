# Google 闭包编译器...对于 C 程序员来说

> 原文：<https://dev.to/bugmagnet/google-closure-compiler-for-c-programmers-hcd>

是的，你没看错，C 程序员。

我在 Exercism 度过了一段愉快的时光，正如我上一篇帖子指出的那样。今天，我做了一个 C 挑战，需要写一个函数来判断一个数字是不是阿姆斯特朗数字。

我写的东西工作正常，但是我开始想知道闭包编译器会对代码做什么，因为 JavaScript 在 C 的系谱树中。

所以我按照我自己的自定义链接到[谷歌闭包编译器](https://closure-compiler.appspot.com/home#code%3D%252F%252F%2520%253D%253DClosureCompiler%253D%253D%250A%252F%252F%2520%2540compilation_level%2520SIMPLE_OPTIMIZATIONS%250A%252F%252F%2520%2540output_file_name%2520default.js%250A%252F%252F%2520%2540formatting%2520pretty_print%250A%252F%252F%2520%2540language%2520ECMASCRIPT3%250A%252F%252F%2520%2540warning_level%2520QUIET%250A%252F%252F%2520%253D%253D%252FClosureCompiler%253D%253D%250A)并开始工作。

我需要做的第一件事是让 C 语言更像 JavaScript。我通过用`var`替换主体中的`int`声明，用`function`替换函数头的`int`来实现这一点。参数的`int`被完全移除。

所以这个

```
int numlen(int n) {
    int len = 0;
    int div = n, mod = div % 10;
    while (mod != 0) {
        len++;
        div = div / 10;
        mod = div % 10;
    }
    return len;
} 
```

Enter fullscreen mode Exit fullscreen mode

变成了这个

```
function numlen(n) {
    var len = 0;
    var div = n, mod = div % 10;
    while (mod != 0) {
        len++;
        div = div / 10;
        mod = div % 10;
    }
    return len;
} 
```

Enter fullscreen mode Exit fullscreen mode

闭包编译器将其归结为

```
function numlen(a) {
  for (var b = 0, c = a % 10; 0 != c;) {
    b++, a /= 10, c = a % 10;
  }
  return b;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我把它转换成 C 语言，最后以
结束

```
int numlen(int n) {
  int div = n;
  int len = 0; 
  for (int mod = div % 10; 0 != mod;) {
    len++;
    div /= 10;
    mod = div % 10;
  }
  return len;
} 
```

Enter fullscreen mode Exit fullscreen mode

有速度提升吗？我不知道。可读性有提高吗？大概不会。事实上，我很同情维护程序员，他们可能没有意识到`for`没有迭代子句，只有初始化和终止。

顺便说一句，这不是完整的解决方案，但足以给出我使用闭包编译器的目的。当我的导师对我的代码竖起大拇指时，我希望完整的解决方案会出现在我的公开资料中。

在随后的一篇文章中，我可能会展示我是如何使用 Closure 编译器来帮助 C#练习的。