# ASLR 和卡斯尔简介

> 原文：<https://dev.to/satorutakeuchi/a-brief-description-of-aslr-and-kaslr-2bbp>

# 前言

本文解释了 Linux 中的两个特性，地址空间位置随机化(ASLR)和内核 ASLR (KASLR)。如果你对`memory address`和`pointer.`有所了解，你可以很容易地阅读这篇文章

# ASLR

黑客有很多方法攻击计算机系统，例如执行未经授权的代码和访问未经授权的代码。许多这类攻击都需要目标代码和目标数据的地址。

如果没有 ASLR，程序的代码和数据在执行时加载到固定地址(*1)。因此，如果你有这些程序的执行文件，攻击者可以很容易地获得目标地址。

*1:作为例外，动态共享库被加载到任意地址。

ASLR 是减轻这类攻击的功能之一。如果程序启用了 ASLR，它们的代码和数据会随机加载。所以攻击者很难得到这些地址(*2)。

*2:有很多方法可以绕过 ASLR 和卡斯尔。然而，我在这里省略了解释如何做。

ASLR 不是通过硬件实现的，而是通过软件(Linux 内核)实现的。当支持 ASLR 的程序被加载到虚拟内存地址空间时，Linux 随机定位它们的代码/数据段。

如果代码/数据不是直接访问的(例如`0x10000000`)，而是间接访问的(例如`base address + 0x100000000`)，那么很容易访问这些。这是因为 Linux 内核只是改变了`base address`。堆和栈就是这样的数据，它们在加载的时候就被分配了，并且本质上是用基址来访问的。

另一方面，在执行时直接访问的代码/数据呢？这些被称为`position-dependent code/data`, Linux 不会在随机的地址定位它们。

这里有一个例子。

```
$  sleep 10000 &
[1] 7951 $  sleep 10000 &
[2] 7952 $  cat /proc/7951/maps       # sleep(pid=7951)'s memory map
00400000-00407000 r-xp 00000000 00:16 207201                             /bin/sleep #  code
00606000-00607000 r--p 00006000 00:16 207201                             /bin/sleep #  read-only data
00607000-00608000 rw-p 00007000 00:16 207201                             /bin/sleep #  readable and writable data
009aa000-009cb000 rw-p 00000000 00:00 0                                  [heap]     #  heap
... 7ffc60378000-7ffc60399000 rw-p 00000000 00:00 0                          [stack]    #  stack
$  cat /proc/7952/maps       # sleep(pid=7952)'s memory map
00400000-00407000 r-xp 00000000 00:16 207201                             /bin/sleep
00606000-00607000 r--p 00006000 00:16 207201                             /bin/sleep
00607000-00608000 rw-p 00007000 00:16 207201                             /bin/sleep
00cf1000-00d12000 rw-p 00000000 00:00 0                                  [heap] ... 7ffd1a406000-7ffd1a427000 rw-p 00000000 00:00 0                          [stack] ... 
```

Enter fullscreen mode Exit fullscreen mode

这里堆栈和堆的地址是随机的，但是代码和数据的地址不是随机的。

为了随机化代码/数据段，程序应该被编译成与位置无关的。这里与位置无关的代码通常称为 PIC，所有代码/数据都与位置无关的程序称为位置无关可执行程序(PIE)。

要通过`gcc`构建饼图，使用`-fpic`选项。使用此选项构建的程序，所有数据/代码都位于随机地址。

下面的例子是窥探两个 ssh 的地址映射信息，那是一个饼图。

```
$  cat /proc/7981/maps
5626d3d9c000-5626d3e45000 r-xp 00000000 00:16 867533                     /usr/bin/ssh
5626d4045000-5626d4048000 r--p 000a9000 00:16 867533                     /usr/bin/ssh
5626d4048000-5626d4049000 rw-p 000ac000 00:16 867533                     /usr/bin/ssh
5626d4049000-5626d404c000 rw-p 00000000 00:00 0
5626d5505000-5626d5544000 rw-p 00000000 00:00 0                          [heap] ... 7ffe2bd73000-7ffe2bd94000 rw-p 00000000 00:00 0                          [stack] ... $  cat /proc/7985/maps
5616589f2000-561658a9b000 r-xp 00000000 00:16 867533                     /usr/bin/ssh
561658c9b000-561658c9e000 r--p 000a9000 00:16 867533                     /usr/bin/ssh
561658c9e000-561658c9f000 rw-p 000ac000 00:16 867533                     /usr/bin/ssh
561658c9f000-561658ca2000 rw-p 00000000 00:00 0
561658d81000-561658dc0000 rw-p 00000000 00:00 0                          [heap] ... 7ffc75060000-7ffc75081000 rw-p 00000000 00:00 0                          [stack] ... 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到所有的内存段都是随机放置的。

PIE 在过去没有使用过，因为它比位置相关的可执行文件慢，因为需要额外的工作来寻址内存。然而，最近主要的 Linux 发行版倾向于使所有或大部分二进制文件可重定位。原因之一是安全的健壮性比性能的细微提升更重要。

比如 [Ubuntu 的二进制默认是 PIE。](https://wiki.ubuntu.com/SecurityTeam/PIE)

您可以使用`file`命令来确认一个程序是否是 PIE。这里有一个例子。

```
$  file /usr/bin/ssh
/usr/bin/ssh: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=ecf7433a7d26461fc1bc7\ a6b6a4eba868e685839, stripped                #  `...share object` means PIE
$  file /bin/bash
/bin/bash: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=04eca96c5bf3e9a300952a29ef3\218f00487d37b, stripped            #  `... executable` means position-dependent executable. 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Linux 启用 ASLR。要禁用 ASLR，将`0`设置为`kernel.randomize_va_space` sysctl 参数。这里的`2`表示启用该功能，这是默认配置。我省略解释`1`关于这个参数的含义。该参数影响系统中的所有二进制文件。

# KASLR

KASLR 可以被认为是 ASLR 的内核版本。它的概念和 ASLR 一样。通过启用 KASLR，Linux 内核在每次引导时都会在随机地址中定位其代码/数据。当然，Linux 内核应该像馅饼一样构建。

KASLR 的效果可以通过内核中符号的地址来确认。所有符号及其地址都写在所谓的`System.map`文件中。在 Ubuntu 上，它位于`/boot`目录下，它们的名字是`System.map-<kernel version>`。此外，运行时符号信息可以通过`/proc/kallsyms`获得。因此，如果`/proc/kallsyms`中的一个符号的地址不同于`System.map`中的地址，KASLR 会被运行系统的内核启用。

这里有一个例子。

```
$ sudo grep "T do_page_fault" /boot/System.map-$(uname -r)
ffffffff8106b780 T do_page_fault
$ sudo grep "T do_page_fault" /proc/kallsyms
ffffffffb626b780 T do_page_fault               # It's different from the address in System.map 
```

Enter fullscreen mode Exit fullscreen mode

通常`System.map`只能被 root 读取，`/proc/kalsyms`的地址信息用零填充，非 root 用户无法获得内核符号的真实地址。

KASLR 是在 Linux 内核 3.14 中引入的。要启用 KASLR，需要使用 enabling `CONFIG_RANDOMIZE_BASE`配置选项来构建 Linux 内核。

虽然 KASLR 在 Linux 内核的上游已经被默认禁用，[它在 Linux 内核 4.12 版被默认启用。](https://www.phoronix.com/scan.php?page=news_item&px=KASLR-Default-Linux-4.12)

如果要显式禁用 KASLR，应该设置`nokaslr`内核引导参数。

4.7 或更早版本的旧 Linux 内核中有一个缺陷。这些内核不能与休眠功能共存。如果`CONFIG_HIBERNATION`被使能，KASLR 被禁用。另一方面，如果您通过设置`kaslr`参数来引导这个内核，KASLR 被启用，休眠被禁用。

更糟糕的是，默认情况下，KASLR 不会在内核日志中打印任何消息。下面是 Linux 内核 4.4 版的对应代码(arch/x86/boot/compressed/aslr . c)。

```
...
unsigned char *choose_kernel_location(struct boot_params *boot_params,                                                                                                                                                             unsigned char *input,                                                                                                                                                                        unsigned long input_size,                                                                                                                                                                    unsigned char *output,                                                                                                                                                                       unsigned long output_size)                                                                                                                             {                                                                                                                                                                                                    unsigned long choice = (unsigned long)output;                                                                                                                                                unsigned long random;                                                                                                                                                                
#ifdef CONFIG_HIBERNATION
        if (!cmdline_find_option_bool("kaslr")) {
                debug_putstr("KASLR disabled by default...\n");
                goto out;
        }
#else
        if (cmdline_find_option_bool("nokaslr")) {
                debug_putstr("KASLR disabled by cmdline...\n");
                goto out;
        }
#endif ... 
```

Enter fullscreen mode Exit fullscreen mode

你会觉得“4.7 或更老的版本太老了，可能我不用它”。然而，Ubuntu 16.04 的内核版本是 v4.4，这个发行版仍然在许多地方使用。如果你长期使用所谓的长期支持发行版，情况是一样的。如果你期望 KASLR 被启用，我建议你确认你的内核版本。此外，如果你的内核足够旧，请确认 KASLR 是否通过内核日志工作，设置内核日志级别以显示上述调试消息。

## 结论

您从这篇文章中获得了以下信息。

*   Linux 中有一些特性叫做 ASLR 和 KASLR。
*   这些特性是为了减轻某种攻击。
*   ASLR 是用户程序，KASLR 是内核。
*   旧的 Linux 内核会悄悄地禁用 KASLR