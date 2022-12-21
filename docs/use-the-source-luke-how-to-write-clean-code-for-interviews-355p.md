# 使用源卢克！如何为面试写干净的代码

> 原文：<https://dev.to/tonymet/use-the-source-luke-how-to-write-clean-code-for-interviews-355p>

我在帮一个朋友准备面试，有很多资源可以练习算法&数据结构。

最终只剩下你和董事会，你必须写干净的代码。

通常你很了解算法，但是在棋盘上迭代指针时你会出错——一个小细节会削弱你的自信。

我的问题是:“在做二分搜索法的时候，你是把支点设在中点的天花板还是地板上？”

我的回答，一如既往:“用源！”

这是来自 [linux src](https://github.com/torvalds/linux/blob/master/lib/bsearch.c) 的`bsearch`。(我最先推荐的是 glibc——不过这个更好读——我不做评判)

```
// SPDX-License-Identifier: GPL-2.0-only
/*
 * A generic implementation of binary search for the Linux kernel
 *
 * Copyright (C) 2008-2009 Ksplice, Inc.
 * Author: Tim Abbott <tabbott@ksplice.com>
 */

#include <linux/export.h>
#include <linux/bsearch.h>
#include <linux/kprobes.h>

/*
 * bsearch - binary search an array of elements
 * @key: pointer to item being searched for
 * @base: pointer to first element to search
 * @num: number of elements
 * @size: size of each element
 * @cmp: pointer to comparison function
 *
 * This function does a binary search on the given array.  The
 * contents of the array should already be in ascending sorted order
 * under the provided comparison function.
 *
 * Note that the key need not have the same type as the elements in
 * the array, e.g. key could be a string and the comparison function
 * could compare the string with the struct's name field.  However, if
 * the key and elements in the array are of the same type, you can use
 * the same comparison function for both sort() and bsearch().
 */
void *bsearch(const void *key, const void *base, size_t num, size_t size,
          int (*cmp)(const void *key, const void *elt))
{
    const char *pivot;
    int result;

    while (num > 0) {
        pivot = base + (num >> 1) * size;
        result = cmp(key, pivot);

        if (result == 0)
            return (void *)pivot;

        if (result > 0) {
            base = pivot + size;
            num--;
        }
        num >>= 1;
    }

    return NULL;
}
EXPORT_SYMBOL(bsearch);
NOKPROBE_SYMBOL(bsearch); 
```

`num>=1` -一个相当于 floor(1/2 * n)的比特移位-更快更干净！

当寻找这些小细节的答案时，使用来源:数百万人使用的稳定成熟的回购协议。

*   [linux 源代码](https://github.com/torvalds/linux/blob/master/lib/bsearch.c)
*   [glibc](https://code.woboq.org/userspace/glibc/bits/stdlib-bsearch.h.html)
*   [boost c++](https://github.com/boostorg/core)
*   您的平台的标准库，例如 [python](https://github.com/python/cpython/tree/master/Modules) ，nodejs 等

其中的每一个都是编写代码时数以千计技巧的金矿。在本地克隆它们，然后在谷歌上搜索。