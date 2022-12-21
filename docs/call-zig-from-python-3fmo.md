# 从 Python 调用 Zig

> 原文：<https://dev.to/tonetheman/call-zig-from-python-3fmo>

Zig 是什么？看这里:[https://ziglang.org/](https://ziglang.org/)

看起来很有趣！

让我们用 Zig 创建一个共享库，我们可以用 ctypes 调用 Python。

```
export fn add(a : i32, b : i32) i32 {
  return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码是一个名为 add 的函数，它接受两个整型(32 位)参数。该函数也返回一个 32 位整数。

要将它编译成动态库，运行这个

```
zig build-lib mathtest.zig -dynamic 
```

Enter fullscreen mode Exit fullscreen mode

此时，您将在当前目录中拥有一个共享库。

```
-rw-r--r-- 1 tonetheman tonetheman     58 Sep  3 07:28 mathtest.zig
-rw-r--r-- 1 tonetheman tonetheman 546152 Sep  3 07:35 mathtest.o
-rw-r--r-- 1 tonetheman tonetheman    395 Sep  3 07:35 mathtest.h
-rwxr-xr-x 1 tonetheman tonetheman 616728 Sep  3 07:35 libmathtest.so.0.0.0 
```

Enter fullscreen mode Exit fullscreen mode

还要注意，您还可以免费获得一些其他文件，最著名的是头文件 mathtest.h。您可以使用 C/C++编译器来调用 Zig 函数。

启动 Python 解释器，看看能否调用第一个 Zig 函数。

```
>>> import ctypes
>>> a = ctypes.CDLL("./libmathtest.so.0.0.0")
>>> print(a)
<CDLL './libmathtest.so.0.0.0', handle 58bfef25b470 at 78bf0457bf10>
>>> a.add(400,400)
800 
```

Enter fullscreen mode Exit fullscreen mode