# 使用 CFFI 构建 C libgeohash 的 Python 包装器

> 原文：<https://dev.to/aldnav/building-a-python-wrapper-of-c-libgeohash-using-cffi-4727>

DEV 的第一篇文章。到🎊️

我以前不知道什么是 Geohash ,被它的概念迷住了。我找到了一个用 c 写的实现。我一直想试验外国函数，发现 [CFFI](https://cffi.readthedocs.io) 适合我的情况。同一天下午，我用 Python 编写了一个简单的 libgeohash 包装器。

看看这个。[https://aldnav . com/blog/writing-a-geo hash-wrapper-using-cffi/](https://aldnav.com/blog/writing-a-geohash-wrapper-using-cffi/)
那是什么？想直接跳到源代码中吗？好吧！[https://github.com/aldnav/geohash](https://github.com/aldnav/geohash)

这里有一个预览。

```
# geohash_build.py from cffi import FFI
ffi = FFI()
ffi.cdef(
    """
    char* geohash_encode(double lat, double lng, int precision);
    GeoCoord geohash_decode(char* hash);
"""
)
lib = ffi.dlopen("geohash.so")
ffi.set_source("_geohash", None) 
```

Enter fullscreen mode Exit fullscreen mode

```
# Simple testing from _geohash import ffi, lib
print(lib.geohash_encode(35.689487, 139.691706, 6))
#>> xn774c 
```

Enter fullscreen mode Exit fullscreen mode

```
# Use geohash module import geohash
geohash.geohash_encode(48.856614, 2.352222)  # u09tvw for Paris! geohash.geohash_decode("wy7b1h")  # lat: 35.179554, long: 129.075642 
```

Enter fullscreen mode Exit fullscreen mode

查看我的笔记，看看我是如何构建包装器的。加上更多的参考资料，链接到各自作者的更全面的笔记。

让我知道你的想法！