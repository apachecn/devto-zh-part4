# 你忘记打包的前 5 个字符串操作函数

> 原文：<https://dev.to/abdurrahmaanj/the-top-5-string-manipulation-functions-you-forgot-to-pack-4060>

Python 提供了很多很酷很方便的字符串操作函数，人们总是会重新发明。Python 功能强大，并且...考虑周到。我们很严格，只选择 5 个，5 个最好的。希望你喜欢它！

### 什么是字符串操作函数？

字符串操作函数是用来操作字符串的辅助函数。如果你让我们说“abc”，一个好的小写函数会把它转换成“ABC”。让我们开始数数。

### 1。str.swapcase()

程序员都很熟悉。上()和。lower()并尝试使用这两个实现案例交换。。swapcase()你已经猜到它会自动转换大小写。

```
 >>> x = 'aaaBBB'
>>> x.swapcase()

'AAAbbb' 
```

**交换酶的使用**

*   [例 1](https://github.com/samtregar/zoomascii/blob/af252c2dbba16dddd2bcd1800bc86f38b2bdb0b2/tests/bench.py#L16)
*   [例 2](https://github.com/MargusHenning/Programmeerimine/blob/5e9928ee9653fd55b2760a5cf4fd4d7313fe49f7/00-Kodut%C3%B6%C3%B631.10.py#L4)
*   [例 3](https://github.com/AndrewDuncan/Doublecoset/blob/e29c9aaa483964ae469a0d4f6b3904d61e800dd2/python/genfold/adj_gen.py#L67)

### 2。str.zfill()

假设您想要构建一个漂亮的表来显示数字为

```
 0000123
0025378
0005234
0012374 
```

如果你不想担心索引等，zfill 是你的好伙伴。

```
 >>> '1234'.zfill(7)

'0001234' 
```

zfill()填充给定宽度的缺失的零。

**使用 zfill**

*   [例 1](https://github.com/coopci/spring-performance/blob/7e30e88d27b687b92fe8617d5e65dc7d5f5a5dd7/gen-class.py#L19)
*   [例 2](https://github.com/SAMMYKH/comp/blob/0e62861717494b92d682c4d439eb60ba33a558be/board/axent/ag14003/applications/mrwa_vsls/overlay/usr/lib/python2.7/site-packages/rtacomms/rtalog.py#L16)
*   [例 3](https://github.com/wildcardcorp/samson/blob/9966d23a1ecebb9eeab8490f799d05413d0ac643/tests/primitives/test_camellia.py#L8)

## 3。str.casefold()

Casefold 用于比较字符串。它试图遵循 unicode 来这样做。

```
 if 'ABC'.casefold() == 'aBc'.casefold():
    pass 
```

问:那和 str.lower()不一样吗？

答:casefold()是一个 unicode 兼容的版本，而 str.lower()不是这样

**使用案例夹**

*   [例 1](https://github.com/pylangstudy/201708/blob/126b1af96a1d1f57522d5a1d435b58597bea2e57/02/01/endswith.py#L7)
*   [例 2](https://github.com/ThatsGobbles/cheffu-old/blob/dfd8121718f82a5f9288d9acdd0544613e1ad7b9/cheffu/helpers.py#L90)
*   [例 3](https://github.com/tiberiuv/Information-Retrieval-Search-Engine/blob/c501e20cd1c2e499148942f587851f7fd56d292c/CourseWork%202/util/UEAlite.py#L161)

## 4。居中()

center()用于文本居中。一个很酷的开箱整理工具。

```
 >>> 'abcd'.center(10)

'   abcd   ' 
```

它还支持一个 fillchar 参数

```
 >>> 'abcd'.center(10, '?')

'???abcd???' 
```

**使用中心**

*   [例 1](https://github.com/epfeff/smartbetas/blob/9262dd21f0800647e4e723470dfb361c1c464282/smartbetas/i_o.py#L21)

## 5 .扩展选项卡()

这在 Python 中很有意义。假设您在读取 python 源文件时替换制表符，或者在构建编辑器时将制表符转换为空格。做就行了。expandtabs()并且它将被转换！

```
 >>> print('''   abcd
        abcd
    abcd'''.expandtabs(4))

    abcd
        abcd
    abcd 
```

**使用扩展标签**

*   [例 1](https://github.com/mcaire/umat_nlv_1d/blob/ef4663a03b41873e0122bc9d2cf3c22434d8f60b/abaqus_umat_nlv_beam.py#L53)
*   [例 2](https://github.com/snebaybay/python/blob/9f22902f1b0ecc13060889b620b0482d58bd7374/django/djangoEnv/lib/python2.7/site-packages/pygraphviz/tests/test_attribute_defaults.py#L16)
*   [例 3](https://github.com/barkow/linux-can-python/blob/94a7189f46b6d7693dfe0b21ca3872ad867103c4/Tools/scripts/pindent.py#L101)

Python 有 coool 字符串操作函数。我们答应了 5 个，现在他们来了。