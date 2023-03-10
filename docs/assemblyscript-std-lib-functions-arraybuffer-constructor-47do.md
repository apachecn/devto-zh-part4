# AssemblyScript 标准库函数:ArrayBuffer #构造函数

> 原文：<https://dev.to/jtenner/assemblyscript-std-lib-functions-arraybuffer-constructor-47do>

今天，我开始发表一系列关于 AssemblyScript std 库函数的文章，讨论这些函数是如何实现的。今天的函数是`ArrayBuffer` `constructor`函数。

## 阵列缓冲器

此类表示包含数据的原始大小的堆分配。这个方法很小，但是包含了很多值得讨论的活动部分。在这里，您可以直接从 std lib 源代码中看到实现。

```
export class ArrayBuffer {

  constructor(length: i32) {
    if (<u32>length > <u32>BLOCK_MAXSIZE) throw new RangeError(E_INVALIDLENGTH);
    var buffer = __alloc(<usize>length, idof<ArrayBuffer>());
    memory.fill(buffer, 0, <usize>length);
    return changetype<ArrayBuffer>(buffer); // retains
  }

} 
```

这里有一个如何使用它的例子。

```
// create a heap allocation of 12 bytes
let buffer = new ArrayBuffer(12); 
```

第一个语句是一个`if`语句，它断言所提供的`ArrayBuffer`的长度没有超过`BLOCK_MAXSIZE`编译时常数。如果是这样，前面的语句抛出一个`RangeError`来表示请求的块太大。

```
if (<u32>length > <u32>BLOCK_MAXSIZE) throw new RangeError(E_INVALIDLENGTH); 
```

下一条语句是对`buffer`的变量声明，它被初始化为内部堆分配*指针*。`__alloc()`函数是一个特殊的内置函数，它在堆上创建一个内存块，该内存块包含一些关于块大小和块类型的元数据以及数据本身。它的参数是堆分配大小和堆分配的运行时“类型”。在这种情况下，`idof<ArrayBuffer>()`内置函数将为`ArrayBuffer`返回正确的运行时 id。最后，`__alloc()`函数将返回一个指向数据起点的指针。

```
var buffer = __alloc(<usize>length, idof<ArrayBuffer>()); // returns `usize` 
```

缓冲区分配后的语句是一个`memory.fill()`批量调用，它将`ArrayBuffer`中的所有字节设置为`0`的字节值。这实际上是将数据“归零”。`memory.fill()`函数有三个参数:指针、填充值(`u8`)和字节数(`usize`)。

```
memory.fill(buffer, 0, <usize>length); 
```

最后，构造函数返回指针，但是它使用了一个特殊的`changetype<T>(value: any)`函数。通常，在 AssemblyScript 中禁止使用`any`，但是这个特殊的函数向 AssemblyScript 发出信号，它需要将指针转换为引用类型。

此时，它将导致运行时引用计数器开始关注`buffer`对象。`// retains`注释提醒人们 AssemblyScript 是使用引用计数进行垃圾收集的，现在将开始关注指针，就像任何其他的`ArrayBuffer`一样。

```
return changetype<ArrayBuffer>(buffer); // retains 
```

如果你对`ArrayBuffer#constructor`在未优化的 web 汇编中编译成什么感兴趣，你可以看看这里。

```
 (func $~lib/arraybuffer/ArrayBuffer#constructor (; 18 ;) (type $FUNCSIG$iii) (param $0 i32) (param $1 i32) (result i32)
  (local $2 i32)
  local.get $1
  i32.const 1073741808
  i32.gt_u
  if
   i32.const 24
   i32.const 72
   i32.const 57
   i32.const 42
   call $~lib/builtins/abort
   unreachable
  end
  local.get $1
  i32.const 0
  call $~lib/rt/tlsf/__alloc
  local.set $2
  local.get $2
  i32.const 0
  local.get $1
  call $~lib/memory/memory.fill
  local.get $2
  call $~lib/rt/pure/__retain
 ) 
```

所有寻找优化版本的尝试都被`binaryen`挫败了，因为它倾向于频繁内联这个特定的函数。

有任何意见或问题吗？请随意将它们留在下面！

感谢阅读，
[@jtenner](https://dev.to/jtenner)