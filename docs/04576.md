# 函数的作用是

> 原文：<https://dev.to/dochan/the-list-swap-function-2maf>

列表交换函数在 c++中用于交换两个大小和类型相同的列表的值。

```
std::list<int> a{1, 2, 3, 4, 5};
std::list<int> b{6, 7, 8, 9, 10};
a.swap(b);
//from here the content of a is in b and vice-versa 
```