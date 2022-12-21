# 有趣的哈希树实现

> 原文：<https://dev.to/johnfound/interesting-hash-tree-implementation-pmc>

当我们需要在一大串字符串中搜索一个字符串时，哈希表非常有用。

他们以这种方式提供 O(1)复杂度，使得我们的算法非常快。

不幸的是，哈希表有一个很大的缺点，尤其是当我们谈论大量的字符串时。

因为保持尽可能低的哈希冲突很重要，所以哈希表的大小必须至少是我们需要放入的字符串数量的两倍。

这样，在一个非常大的字符串集合上，内存浪费会非常大。如果我们填充更多的表，搜索的速度会很快下降到 O(n)的简单线性搜索。

有不同的方法来解决这些问题。我在这里为其中的一个写了这篇文章。

算法的作者是[托马兹·格里斯塔](https://www.youtube.com/channel/UCs_OWSjmFntZqjzSpgJoBhA),[flat assembler](https://flatassembler.net)的作者。

FlatAssembler 使用这种算法是为了在编译期间处理大量的标签名。

该算法比经典哈希表慢，但仍将复杂度保持为 O(log k ),其中 k 是哈希的大小，而不是集合的大小。

这样，不管集合中的字符串计数如何，搜索时间都将保持不变，并且可以假设为 O(1)。

考虑到冲突的数量少得多，在大的集合上，它比具有较小散列大小的传统散列表更快。

## 给树添加字符串。

首先，我们需要计算字符串的哈希值。为了最大限度地减少冲突，最好使用相对较大的散列值，例如 32 位。

我通常使用的是 [FNV-1b](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function#FNV-1_hash) 哈希，但是哈希算法对于算法的理解并不是很重要。

然后，我们需要一个线性内存区域，在这里，以下类型的元素将被连续分配:

```
struct THashTreeNode
  .bit0 dd ?
  .bit1 dd ?
ends 
```

然后，从第一个数组元素开始，一次将散列向右移动一位，我们在`.bit0`或`.bit1`字段中(取决于移动的位的值)放入到数组的下一个元素的偏移量，在那里继续扫描。

下一个元素被分配在数组的末尾，就在最后一个使用的元素之后。(像在队列或线性列表中)。

当我们到达散列的第 32 位时，最后一个索引将指向具有有效载荷的元素(或者如果您愿意，也可以指向树的叶子)——它可以占用随机空间，但是如果叶子结构的大小是节点结构的大小的倍数，则更方便。

在我的实现中，它占用了数组的两个连续元素:

```
struct THashTreeLeaf
  .hString dd ?         ; a handle of the string stored.
  .next    dd ?         ; the next element in the cases of collisions.
  .lparam  dd ?         ; user defined parameter.
  .wparam  dd ?         ; user defined parameter.
ends 
```

字段`.hString`包含一个指向我们放入树中的字符串的指针或句柄。(在我使用的库中，字符串是动态的，由句柄表示，而不是指针。但区别并不重要)

字段`.next`包含一个到下一个`THashTreeLeaf`的偏移量，在极少数情况下，如果我们有两个具有相同散列的不同字符串(冲突)。

## 在集合中搜索字符串。

搜索算法与我们向集合中添加字符串时几乎相同。

从数组的开头开始，我们一次将搜索到的字符串散列移动一位，并跳转到从`.bit0`和`.bit1`字段的偏移量。如果某一步的偏移量为 0，这意味着字符串不在树中。

如果在 32 个步骤之后，我们找到了一个叶元素，则需要与存储的字符串进行直接的字符串比较，以解决可能的冲突。

然后，如果字符串不匹配，我们需要跟踪来自`THashTreeLeaf.next`字段的偏移量，直到 0(字符串不在树中)或字符串匹配-字符串在树中。

您可以看到，在后一种情况下，搜索退化为简单而缓慢的链表搜索。幸运的是，当散列值为 32 位时，这种冲突很少发生。

## 优点

该算法是内存友好的。

整个树位于一个连续的内存区域中，这有利于 CPU 缓存，并额外提高了性能。

该算法相对简单，至少对于汇编语言实现是如此。

我不确定它将如何在高级语言中实现，但至少对于 C/C++/Pascal 应该没有问题，除了一些可能的代码可读性问题...

请注意，相同的算法可以实现为一个普通的树，具有单独分配的节点和叶子。但是这样的实现将会比较慢，因为大量小块内存的堆分配和缓存问题。

## 智力竞赛

为了使事情更加清楚，下面是这个过程的完整代码，它可以向树中添加字符串，也可以在不添加字符串的情况下在树中搜索字符串。

因为搜索和添加算法非常相似，所以可以在一个过程中提供这两种功能。

作为对读者的一个小测验，我不会对代码进行详细的逐行解释。它以与产品库中相同的形式发布。

我将在文章的最后对使用的外部过程给出一点参考。

所以，就这些了。这是代码。试着分析理解一下！:)

如果有不清楚的地方，请在评论中提问。

我会详细解释，但让我们给解谜者一些有趣的时间。

```
; Searches the hash tree for the given string.
; if [.fAdd] is TRUE and if the string is not in the tree it will be added.
;
; Arguments:
;   .pHashTree - pointer to TArray with elements size sizeof.THashTreeNode (8 bytes)
;   .hString - handle/pointer to the string to be searched/added. Notice, that exactly this
;              string will be added to the THashTreeLeaf element. The caller is responsible
;              to not free this string until the hash tree is needed.
;   .fAdd - flag indicating whether to add the string to the tree if is not included.
;
; Returns:
;   edx - pointer to the array. Can be reallocated!
;   eax - index in the TArray of the THashTreeLeaf of the string.
;   CF = 1: the string is already in the tree.
;   CF = 0: the string is not in the tree.
;     [.fAdd] <> 0: eax contains a pointer to the new added leaf.
;                   eax == 0 means there is an out of memory error on
;                   the attempt to add the string.
;     [.fAdd] = 0: eax == 0

proc SearchHashTree, .pHashTree, .hString, .fAdd
.hash dd ?
begin
        pushad

        mov     edx, [.pHashTree]

        stdcall StrHash, [.hString]
        mov     [.hash], eax

        mov     ecx, 32              
        mov     ebx, TArray.array

        cmp     [edx+TArray.count], 0
        jne     .treeseek

        stdcall AddArrayItems, edx, 1
        and     dword [eax+THashTreeNode.bit0], 0
        and     dword [eax+THashTreeNode.bit1], 0

.treeseek:
        xor     edi, edi
        ror     [.hash], 1
        adc     edi, edi
        lea     edi, [ebx+4*edi]

        cmp     dword [edx + edi], 0 ; edi is offset from the beginning
                                     ; of the array.
        je      .notfound

        mov     ebx, [edx + edi]
        loop    .treeseek

; The hash exists in the hash tree. Process the possible collisions.
; here ebx is the offset to the THashTreeLeaf !

.cmp_loop:
        stdcall StrCompCase, [.hString], [edx+ebx+THashTreeLeaf.hString]
        jc      .finish      ; the string is already in the hash tree!!!

        lea     edi, [ebx + THashTreeLeaf.next]

        cmp     dword [edx + edi], 0
        je      .add_in_list

        mov     ebx, [edx + edi]
        jmp     .cmp_loop

.add_in_list:
        cmp     [.fAdd], 0
        je      .finish_zero

        stdcall AddArrayItems, edx, 2
        jc      .finish_zero

        sub     eax, edx
        jmp     .do_add

.notfound:
        cmp     [.fAdd], 0
        je      .finish_zero

; edx - pointer to the tree array,  ebx - offset of the last 
;       found element.
; eax - 0 or 1 depending of the last bit checked. 
; ecx - remaining bits count.
; edi - the offset to the [.bitX] field of the last node.

.add_remaining:

        lea     eax, [ecx+1]
        stdcall AddArrayItems, edx, eax  ; add all needed (THashTreeNode) + THashTreeLeaf
        jc      .finish_zero

        sub     eax, edx

        dec     ecx
        jz      .do_add

.addloop:
        mov     [edx + edi], eax
        and     dword [edx + eax + THashTreeNode.bit0], 0
        and     dword [edx + eax + THashTreeNode.bit1], 0

        xor     edi, edi
        ror     [.hash], 1
        adc     edi, edi

        lea     edi, [eax + 4*edi]
        add     eax, sizeof.THashTreeNode
        loop    .addloop

.do_add:
        mov     [edx + edi], eax
        lea     ebx, [edx+eax]

        mov     ecx, [.hString]
        xor     eax, eax

        mov     [ebx+THashTreeLeaf.hString], ecx
        mov     [ebx+THashTreeLeaf.next], eax
        mov     [ebx+THashTreeLeaf.lparam], eax
        mov     [ebx+THashTreeLeaf.wparam], eax

        clc

.finish:
        mov     [esp+4*regEAX], ebx
        mov     [esp+4*regEDX], edx
        popad
        return

.finish_zero:
        xor     ebx, ebx
        clc
        jmp     .finish
endp 
```

**非常小的备忘单:**

`TArray`是一个动态数组的结构，需要时可以调整大小。([出处](https://fresh.flatassembler.net/fossil/repo/fresh/artifact/e92c47efbc623aed))

过程`AddArrayElements`在 TArray 的末尾添加新元素，并在`EAX`中返回指向这些新元素的指针，在`EDX`中返回指向由于调整大小而可以重新分配的数组的指针。

过程`StrHash`计算一个字符串的 32 位散列并在 EAX 返回它

过程`StrCompCase`比较两个字符串，区分大小写，并在 CF 标志中返回布尔结果。如果字符串匹配则为 1，否则为 0。

绝对初学者快速汇编指令参考:[此处](https://www.felixcloutier.com/x86/)

**NB** :以上实现远非最优。它是为了可读性和正确性而写的。稍后将进行优化。错误也修复了。