# 内存视图:处理字符串

> 原文：<https://dev.to/tuned/memory-views-handling-strings-48h7>

做好准备，这篇文章将会比上一篇不那么简单，因为我们将讨论如何从/向 WebAssembly (Wasm)内存视图读取/写入 UTF-8 字符串！

# Unicode(简历)

*如果你认为你已经熟悉了*，请随意跳到下一段

即使 Rust 编译的 Wasm 和 Python 在许多方面是非常不同的语言，它们可能与最流行的编程语言截然相反，它们都利用 [**Unicode** 格式进行网络交换](https://tools.ietf.org/html/rfc5198)；幸运的是，如今这是一个被广泛接受和支持的标准。

关于 Unicode 的一些基本泛型:

> ...它将一个字符编码为一到四个字节的序列[1]
> 
> ...对于任何给定的码位，只有最短的编码被认为是格式良好的；你不能用四个字节来编码一个三个就能装下的码位[1]

字符串中的每组码位(对于 UTF-8，一个码位可以小到 1 个字节，大到 4 个)编码一个字符(换句话说 UTF-8 是 Unicode 的 8 位变宽编码)；通常会有一个基本代码点和随后的代码点，这些代码点会向基本代码点添加细节。 [Unicode version 12.1](https://en.wikipedia.org/wiki/Unicode#Versions) 允许记录 137，994 个代码点，涵盖了大量语言的所有字符。

> UTF-8 支持任何 Unicode 字符，这实际上意味着任何自然语言(科普特语、僧伽罗语、音素语、切诺基语等)，以及许多非口语语言(音乐符号、数学符号、APL)和表情符号[【来源】](https://superuser.com/a/946614)

更多关于这个美妙的互联网无处不在的工具的信息，请访问 Unicode Consortium。

# 字符串

对于这个实验，我们需要知道的是，我们可以将字符串表示为代表 Unicode(或一组)码位的 8 位整数列表。要使用[*Python-ext-wasmer*](http://wasmer.io)在 Python 和 Wasm 之间传递字符串，我们需要知道:

*   在 Python 中，字符串是不可变的字符序列；打开 Python IDLE 并尝试:

```
# a Unicode string containing non-ASCII chars
>>> st = 'ABW@☀CC☯'

# same string as bytestring encoded in UTF-8 (hex)
>>> bytes(st, 'UTF-8')
b'ABS@\xe2\x98\x80CC\xe2\x98\xaf'

# UTF-8 (decimal) represention of the same bytestring
>>> [b for b in bytes(st, 'UTF-8')]
# elements for Unicode code points as Python integers
[65, 66, 83, 64, 226, 152, 128, 67, 67, 226, 152, 175] 
```

Enter fullscreen mode Exit fullscreen mode

[带射线的黑太阳(U+2600)](https://graphemica.com/%E2%98%80) 码位用 UTF-8 编码表示:3 个十六进制值或等价的 3 个正十进制值:

```
#
# same code point, same encoding (different formats)
#
\xe2\x98\x80 ----> ☀ as UTF-8 (hex)
226 152 128 ----> ☀ as UTF-8 (decimal) 
```

Enter fullscreen mode Exit fullscreen mode

*   任何 Unicode 点序列在 Python 中都可以表示为`List[int]`；在特殊情况下，如果我们想使用 UTF-8 与 Rust 中的代码进行互操作，我们需要在它的一侧使用一个`Vec<u8>`(更多细节[在这里](https://www.reddit.com/r/rust/comments/2b08l5/uft8_and_string_why_vecu8/))；最后，在 Wasm 级别，我们有一个线性存储单元阵列，用于索引其元素:

```
# pseudo-code
array_memory[0] = 226
array_memory[1] = 152
array_memory[2] = 128 
```

Enter fullscreen mode Exit fullscreen mode

现在很明显，我们可以在 Wasm 线性存储器中存储十进制 UTF 8 点。

# Wasmer 内存视图

我们可以把每个 Wasm 实例看作一个迷你沙箱，在内存上有自己的抽象。内存被表示为“视图”，基本上是管理值和它们在线性内存中的位置之间的引用的表。

*细节* : [Wasmer 运行时核心](https://github.com/wasmerio/wasmer/tree/dac86eec330b67cc60c81aa5640c3e4d66e901d2/lib/runtime-core/src)提供了这些非常强大的抽象，允许利用 Wasm 内存；在这种情况下我们要看一看 [**python-ext-wasm** 内存视图](https://github.com/wasmerio/python-ext-wasm/blob/f91ce2c32e6cf3b96cd858fbdaf40ab0e31b1c4c/src/memory/view.rs#L12)；接口方法是从每个基本类型的宏中生成的。

# 传递一串

让我们进入实验的主要部分:**如何利用所有这些强大的工具，让 Python 流利地与 Rust/Wasm** 对话并接收回来？
简述:

*   我们将从 Python 中使用 Wasmer 视图告诉实例的内存写什么
*   我们将调用 Wasm 函数(从 Rust 编译而来),从实例的导出函数中读取数据
*   我们将把结果传回 Python

所有这些过程都是通过[这个函数](https://github.com/Mec-iS/rust-wasm-python-101/blob/50ae7799bb0eeb725a5792dd1e83d258b70e21ae/pywasm/examples/__init__.py#L46) :
来演示的

```
def test_reverse(instance, func, bytestr):

    # return a Wasmer Memory View for 8-bit integers
    mem_view = allocate_bytes(instance)

    # write the UTF-8 (decimal) code points to the view
    mem_view = write_to_memory(bytestr, mem_view, offset=0)

    # call the exported function by reading the offset of the
    # view for the length of the bytes string (b'...')
    result = func(0, len(bytestr))

    # read the result value at a given position in linear memory
    return address_to_utf8(mem_view, result, len(bytestr)) 
```

Enter fullscreen mode Exit fullscreen mode

使用的方法是从这个[小型 Python-to-Wasmer API](https://github.com/Mec-iS/rust-wasm-python-101/blob/50ae7799bb0eeb725a5792dd1e83d258b70e21ae/pywasm/src/utils.py#L1) 加载的。

这个[代码块](https://github.com/Mec-iS/rust-wasm-python-101/blob/50ae7799bb0eeb725a5792dd1e83d258b70e21ae/pywasm/run_wasm_with_python.py#L31) :
很容易显示结果

```
Reversing b'Test sTRing' >>>
b'gniRTs tseT' 
```

Enter fullscreen mode Exit fullscreen mode

感谢您的关注。

来源:
[1]奥瑞利《编程锈》ISBN-10: 9781491927281，pg。392-393
【2】【kunststube.net/encoding T2】