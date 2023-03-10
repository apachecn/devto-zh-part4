# 实用二元分析–CTF 演练–3、4 级

> 原文：<https://dev.to/denisnutiu/practical-binary-analysis-ctf-walkthrough-level-3-4-1koh>

你好，

在这篇文章中，我将向你展示我对《实用二元分析》一书中第 5 章 CTF 的解决方案。

对于这个二进制，提示是修复四个坏掉的东西。

运行文件给了我们如下的响应:

```
binary@binary-VirtualBox:~/ctf$ file ./lvl3 
./lvl3: ERROR: ELF 64-bit LSB executable, Motorola Coldfire, version 1 (Novell Modesto) error reading (Invalid argument) 
```

readelf 命令给了我们:

```
binary@binary-VirtualBox:~/ctf$ readelf -h ./lvl3 
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 0b 00 00 00 00 00 00 00 00 
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            Novell - Modesto
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Motorola Coldfire
  Version:                           0x1
  Entry point address:               0x4005d0
  Start of program headers:          4022250974 (bytes into file)
  Start of section headers:          4480 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28
readelf: Error: Reading 0x1f8 bytes extends past end of file for program headers 
```

这时，很明显 ELF 头坏了，为了修复它，我打开了[维基百科](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format)和 [elf 规范](https://refspecs.linuxfoundation.org/elf/elf.pdf)。

当我用二进制 Ninj 手动遍历每个字段时。当我在`0x07`检查当前字节的偏移量时，维基百科说:`It is often set to 0 regardless of the target platform.`我已经把它改成了`0x00`。(注意:我认为这个字段可能还可以)

在偏移量 0x12 处，值`Specifies target instruction set architecture`和当前无效。通过谷歌搜索，我找到了一篇标题为`Novell's Next Generation OS Will Natively Support Intel's Future IA-64 Architecture`的文章，所以我将值设置为`0x3E`。

[![binjahex.jpg](img/62c0055de3c70cefc608150e6c764e24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XWtd-ONO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xon0rw627fjqj7blirdt.png)

在偏移量 0x20 处，我们有`e_phoff`和`Points to the start of the program header table. It usually follows the file header immediately, making the offset 0x34 or 0x40 for 32- and 64-bit ELF executables, respectively`。值`de ad be ef`显然是无效的。我把值换成了`40 00 00 00`。

这时，我想我修复了二进制文件并运行了它，它运行了，并给了我一个无效的标志。

如果运行以下命令:

```
binary@binary-VirtualBox:/media/sf_Dropzone$ readelf -S ./lvl3 | grep .text
  [14] .text             NOBITS           0000000000400550  00000550 
```

你会发现。文本部分标记为`0x8 - NOBITS`，应该是`0x1 - PROGBITS`。为了做出改变，我使用二进制忍者作为十六进制编辑器，在原始模式下打开二进制文件。

从 readelf 命令:

```
 Start of section headers:          4480 (bytes into file) 
```

段头的开头是 4480 字节。区段标头的长度为 0x40 个字节。4480 转十六进制-> 0x1180。0x40 * 14 + 0x1180 = 0x1500。

在偏移量`0x1504`处，我们将类型从 SHT_NOBITS 更改为 SHT_PROGBITS。

运行二进制文件后，我们得到了有效的标志:

```
3a5c381e40d2fffd95ba4452a0fb4a40  ./lvl3 
```

* * *

完成第 3 级后，我想睡觉，在睡觉前，我想运行 ltrace，对二进制文件进行 strace，我得到了这个:

```
binary@binary-VirtualBox:~/ctf$ ltrace ./lvl4
__libc_start_main(0x4004a0, 1, 0x7ffd6fb460e8, 0x400650 <unfinished ...>
setenv("FLAG", "656cf8aecb76113a4dece1688c61d0e7"..., 1)             = 0
+++ exited (status 0) +++ 
```

没想到会这样，非常好。

感谢阅读！