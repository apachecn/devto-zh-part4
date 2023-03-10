# 病毒 101:不要怀疑

> 原文：<https://dev.to/cpu/virus-101-don-t-be-suspicious-53o1>

欢迎回来！如果这是你第一次访问 VeXation，你可能想从[开始](https://dev.to/cpu/vexation---lets-write-a-virus-51e0)。

# 继续重述

在[的最后一篇帖子](https://dev.to/cpu/using-win95-kernel32-dll-exports-like-a-virus-elg)结束的时候，我已经完成了 [`apisafejector`](https://github.com/cpu/vexation/tree/master/apisafejector) ，这是一个用于 Windows 95 的自我复制的位置独立 PE infector 病毒，它避免了硬编码的 Win32 API 地址。虽然`apisafejector`相对于 WIP 病毒的早期版本和[迭代](https://github.com/cpu/vexation/tree/master/pijector)来说是一个真正的改进，但它仍然有一个很大的缺陷:被感染的程序不再正常工作！

今天我将描述我如何修复这个缺陷并更新病毒，以便在传播感染后执行原始程序代码。

# 手头的问题

当被感染的程序运行时，`apisafejector`为了获得要执行的附加病毒代码而采取的步骤之一是[改变被感染的可执行文件的 PE 头中的`AddressOfEntryPoint`](https://github.com/cpu/vexation/blob/480676904b8fcc39ebeed19846b28491f4e55fa2/apisafejector/apisafejector.asm#L422-L439) 。

```
; eax -> SecHdrVirtualAddress of .ireloc section, a RVA
mov (IMAGE_NT_HEADERS [ecx]).OptionalHeader.AddressOfEntryPoint, eax 
```

[“PE 内部对等”](https://docs.microsoft.com/en-us/previous-versions/ms809762(v=msdn.10)#the-pe-header)将该字段描述为:

> DWORD AddressOfEntryPoint
> 
> 加载程序将开始执行的地址。这是一个 RVA，通常可以在。文本部分。

在`apisafejector`的情况下，病毒践踏被感染程序的真正的`AddressOfEntryPoint` RVA，用一个指向被注入的病毒代码所在的`.ireloc`段的替换一个指向`.text`段的。重要的是，注入的病毒代码永远不会调用程序真正的可执行代码，这些代码潜伏在`.text`部分。

从用户体验的角度来看，这意味着被感染的程序看起来是被破坏的——它们在运行时不做任何事情(*当然除了悄悄地感染其他可执行文件*)。这种副作用具有不必要的破坏性，肯定会过早地引起人们对病毒的关注。

## 确认

为了让自己重回正轨，我用`tdump`、`td32`和一个被感染的`calc.exe`验证了`apisafejector`的应用程序破坏行为。

简而言之，我的过程是:

*   使用`make`在没有调试符号的情况下构建`apisafejector`。
*   通过运行`td32`中的`apisafejector.exe`直到完成，使用`make run`来感染`calc.exe`的副本。
*   使用`tdump`验证`calc.exe`是否被感染。
*   将受感染的`calc.exe`复制到未受感染的`cdplayer.exe`旁边的临时目录中
*   使用`tdump`验证干净的`cdplayer.exe`尚未被感染。
*   运行被感染的`calc.exe`。似乎什么都没发生。没有计算器出现。
*   再次使用`tdump`表示`cdplayer.exe`已经被感染。
*   运行`cdplayer.exe`以显示它似乎也被打破了。没有光盘播放器界面出现。

[https://www.youtube.com/embed/xBaSQWOEN5w](https://www.youtube.com/embed/xBaSQWOEN5w)

# 直接解决

可疑地破坏受感染的应用程序是急需修复的病毒代码中的一个 bug。我采用了一种直接的解决方案来保留被感染程序的原始功能:

1.  当可执行文件被感染到注入被感染程序的病毒代码中时，保存原始的`AddressOfEntryPoint`值。
2.  当被感染的程序完成传播感染时，将执行返回到保存在病毒代码中的`AddressOfEntryPoint`。

# 保存 RVA 的原始分录

如果你记得以前的 [delta offets](https://dev.to/cpu/a-virus-writers-best-friend-the-delta-offset-1hle) 帖子，那么你已经知道病毒*代码*和病毒*变量*都在同一个 PE 段。这使得一切都是独立的，并且更容易注入到新的可执行文件中。

这种方法的另一个令人高兴的副作用是，一代病毒很容易为下一代病毒“预先填充”变量。当正在执行的病毒代码将其自身复制到目标可执行文件的`.ireloc`部分时，它将复制其变量及其当前值。

重要的是，这意味着如果病毒在注入自身并踩踏入口点之前保存了受害者程序的原始`AddressOfEntryPoint`值，那么它将在以后被注入的病毒代码访问。

# 跳转到原词条 RVA

由于被感染程序的真实入口 RVA 是可访问的，所以病毒代码可以通过`jmp`调用它来将执行重定向回原始程序。

实际上，这个计划还有一个额外的小问题:最初的条目 RVA 仅仅是一个相对的地址。这不是一个病毒代码可以进入的绝对地址。相反，它是相对于操作系统的[加载程序](https://en.wikipedia.org/wiki/Loader_(computing))加载可执行文件的地址的偏移量。

为了计算出`jmp`到病毒代码的绝对地址，需要能够找出操作系统加载可执行文件的地址(通常称为*基址*)。我决定用`kernel32.dll`导出的 win32 [`GetModuleHandleA`](https://docs.microsoft.com/en-us/windows/win32/api/libloaderapi/nf-libloaderapi-getmodulehandlea) 函数来找这个。通过为`lpModuleName`自变量`GetModuleHandleA`提供`NULL`值，返回执行`.exe`的基址。

[最后的烦恼帖](https://dev.to/cpu/using-win95-kernel32-dll-exports-like-a-virus-elg)为从病毒代码中可靠地调用导出的`kernel32.dll`函数打下了基础，这使得使用`GetModuleHandleA`找到可执行文件的基址变得非常简单。通过将基址与保存的原始条目 RVA 相结合，病毒代码拥有了一个指向`jmp`的绝对地址，因此被感染的程序可以开始正常运行。

# 完成组装

为了着手解决入口点问题，我使用了 [`apisafejector`项目代码](https://github.com/cpu/vexation/tree/master/apisafejector)作为基础，将其复制到一个名为 [`epjector`](https://github.com/cpu/vexation/tree/master/epjector) (我猜是*“入口点注入器”*的缩写)的新目录中？)并相应地重命名文件/includes。我的大部分改动都在 [`epjector.asm`](https://github.com/cpu/vexation/tree/master/epjector/epjector.asm) 。

首先，我在病毒代码末尾的[标签上添加了两个新变量来保存入口点 RVA。`DD`是“定义双字”并产生一个 4 字节的变量，与`DWORD AddressOfEntryPoint` PE 头字段的大小相匹配。](https://github.com/cpu/vexation/blob/ef1c6bc24bcd4951db7bc784c4c672f6cd788981/epjector/epjector.asm#L605-L661) 

```
; Original entry-point of to-be infected .exe (or null in generation 0)
originalEntryPoint  DD 0

; Address we return control flow to after infecting (copy of original entry
; point)
savedEntryPoint     DD 0 
```

接下来，我更新了 [`@@analyzepe`代码](https://github.com/cpu/vexation/blob/ef1c6bc24bcd4951db7bc784c4c672f6cd788981/epjector/epjector.asm#L322-L327)，以在被覆盖之前将被考虑感染的 PE 文件的`AddressOfEntryPoint`值复制到`originalEntryPoint`变量中(当然，考虑到`ebp`中的增量偏移)。

```
; At this point we've decided we have found a valid i386 PE and we can
; analyze it for infection.
@@analyzepe:
  ; Copy the original entrypoint address somewhere safe
  mov ecx, (IMAGE_NT_HEADERS [eax]).OptionalHeader.AddressOfEntryPoint
  mov [ebp + originalEntryPoint], ecx 
```

为了填充`savedEntryPoint`，我在计算了增量偏移量之后，立即在病毒代码的[开头添加了一些新的逻辑:](https://github.com/cpu/vexation/blob/ef1c6bc24bcd4951db7bc784c4c672f6cd788981/epjector/epjector.asm#L47-L53) 

```
; If this is not generation 0 then the originalEntryPoint variable will have
; been set when the currently executing PE was infected. We need to stash that
; somewhere we can JMP to later. We'll be writing over originalEntryPoint when
; we find a target to infect and propagate another generation.
@@saveoep:
    mov eax, [ebp + originalEntryPoint]
    mov [ebp + savedEntryPoint], eax 
```

最后一项任务是重构标记为 [`findfirst`](https://github.com/cpu/vexation/blob/ef1c6bc24bcd4951db7bc784c4c672f6cd788981/epjector/epjector.asm#L192-L209) 和 [`findnext`](https://github.com/cpu/vexation/blob/ef1c6bc24bcd4951db7bc784c4c672f6cd788981/epjector/epjector.asm#L224-L240) 的代码，找到`jmp`的正确绝对地址，以便在没有更多`.exe`要感染时使用`savedEntryPoint` RVA。

以前，如果`FindFirstFileA`返回`INVALID_HANDLE_VALUE` ( `-1`)或者如果`FindNextFileA`返回 0，那么病毒代码将`jmp`到`error`标签以退出进程。我重构了这个逻辑的`epjector`版本，将`jmp`替换为调用`CALL_OEP`的`@@nofirst`或`@@nonext`标签。例如:

```
@@nofirst:
  CALL_OEP 
```

第 0 代人的情况与随后几代人略有不同。第 0 代没有在感染后返回执行的“原始”功能。对于所有其他代,`savedEntryPoint`变量保存 RVA，如果程序没有被感染，操作系统加载程序会使用它。

我选择实现查找保存的入口点 RVA 的绝对地址，并跳转到一个名为`CALL_OEP`、`macros.inc` 中定义的[的宏。](https://github.com/cpu/vexation/blob/ef1c6bc24bcd4951db7bc784c4c672f6cd788981/epjector/macros.inc#L82-L106)

宏检查存储在`ebp`中的增量偏移量，以决定当前执行的病毒代码是否是第 0 代。如果是第 0 代，那么宏评估为与旧的`apisafejector`行为相同，标签为`error`的`jmp`。对于第 1 代以上，宏评估代码，该代码将使用`savedEntryPoint`来`jmp`到正确的绝对地址。

```
; CALL_OEP is a macro for calling the savedEntryPoint of the
; infected EXE. If called in generation 0 it is equivalent to
; a jmp to the error label because there is no saved entry
; point. When called in generation 1 the GetModuleHandleA API
; function from kernel32.dll is used to find the absolute
; address with the savedEntryPoint RVA.
CALL_OEP MACRO
    LOCAL @@notgenzero
    LOCAL @@genzero
    ; Use EBP to decide if this is gen > 0
    cmp ebp, 0h
    je @@genzero
@@notgenzero:
    ; When it isn't gen0 we need to jmp to OEP
    ; First calculate the base address of the infected PE
    CALL_RUNTIME_API GetModuleHandleA, <0h>
    ; Then add the saved OEP
    add eax, [ebp + savedEntryPoint]
    ; Bye bye! Give control to the non-viral code.
    jmp eax
@@genzero:
    ; When it is gen0 we don't have an OEP to
    ; jmp to. Instead just jmp to error and ExitProcess.
    jmp error
ENDM 
```

# 一种更隐蔽的病毒

我重复了我使用[确认`apisafejector`行为](http://localhost:8000/entry-points/#confirmation)的相同过程，以验证`epjector`通过保留程序的原始行为成功隐藏了程序被感染的事实。

对于`epjector`,流程是:

*   使用`make`在没有调试符号的情况下构建`epjector`。
*   通过运行`td32`中的`epjector.exe`直到完成，使用`make run`来感染`calc.exe`的副本。
*   使用`tdump`验证`calc.exe`是否被感染。
*   将受感染的`calc.exe`复制到未受感染的`cdplayer.exe`旁边的临时目录中
*   使用`tdump`验证干净的`cdplayer.exe`尚未被感染。
*   运行被感染的`calc.exe`。这次计算器 UI **确实出现了**！
*   再次使用`tdump`表示`cdplayer.exe`已经被感染。
*   运行`cdplayer.exe`。它也能按预期工作，并出现 CD 播放器用户界面。

[https://www.youtube.com/embed/OuiVskD6PSo](https://www.youtube.com/embed/OuiVskD6PSo)

# 结论

已经拿了六个帖子(！)但我终于找到了一个可接受的 PE 文件感染病毒的框架。它肯定不是一种隐蔽或复杂的病毒
,但它:

*   成功地在目录中自我传播。
*   不对任何 win32 API 地址进行硬编码。
*   不会破坏被感染的程序。

对于未来的帖子，我有几个想法:

*   制造有效载荷。除了自我繁殖，病毒还需要做其他事情。
*   改进感染策略。病毒应该会在当前目录之外复发。
*   讨论 AV。我想总结一下当前病毒最明显的“隐形”问题，并分享一些运行反病毒软件的结果。

一如既往，我希望听到关于这个项目的反馈。如果你对上面的某个方向比其他方向更感兴趣，那也是很有用的。请随时在推特( [@cpu](https://twitter.com/cpu) )或电子邮件(【daniel@binaryparadox.net】T2)上给我留言。