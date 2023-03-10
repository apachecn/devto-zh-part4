# 是否要对齐分配？

> 原文：<https://dev.to/redcreator37/to-align-assignments-or-not-5hi6>

如果你看过一些著名的维护 C/C++开源项目的代码，你可能会注意到，每当有多个变量赋值时，它们往往是这样对齐的:

```
first_num      = 1;
second_num     = 2;
third_num      = 3;
fourth_num     = 4;
fifth_num      = 5;
some_other_num = 6; 
```

Enter fullscreen mode Exit fullscreen mode

而不是

```
first_num = 1;
second_num = 2;
third_num = 3;
fourth_num = 4;
fifth_num = 5;
some_other_num = 6; 
```

Enter fullscreen mode Exit fullscreen mode

这有时也可以在 Makefiles 中看到(尤其是自动生成的)。但是说到 Java，默认情况下，大多数 ide 可能会尝试删除多余的空白。

那么你认为呢？这种风格也与 Java 相关吗？
还有，你觉得哪个看起来可读性更强？