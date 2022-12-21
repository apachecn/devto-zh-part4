# 汇编脚本标准库:字符串#切片

> 原文：<https://dev.to/jtenner/assemblyscript-std-lib-string-slice-3c20>

今天的 std lib 方法是`String#slice`，这是一个非常有用的将字符串内容复制到新的`String`中的方法。这是函数的源代码。

```
class String {

  slice(start: i32, end: i32 = i32.MAX_VALUE): String {
    var len = this.length;
    start = start < 0 ? max(start + len, 0) : min(start, len);
    end   = end   < 0 ? max(end   + len, 0) : min(end,   len);
    len   = end - start;
    if (len <= 0) return changetype<String>("");
    var out = __alloc(len << 1, idof<String>());
    memory.copy(out, changetype<usize>(this) + (<usize>start << 1), <usize>len << 1);
    return changetype<String>(out); // retains
  }

} 
```

`String`类也是`string`类型的后台类。(注意大小写的重要性，但是它们被视为完全相同的类。)现在让我们看一下每一行，并回顾一下复制过程的每一步。

```
slice(start: i32, end: i32 = i32.MAX_VALUE): String { 
```

注意，没有 AssemblyScript 中的第一个参数就不能调用`str.slice()`,因为它是必需的。这两个参数都是从零开始的相对索引。所以`str.slice(3, 8)`会返回从索引`3`(含)到`8`(不含)的一部分字符串。)第二个参数默认为`i32.MAX_VALUE`，即`2,147,483,647`。如果任一参数大于字符串长度或导致计算出的索引小于`0`，值将被箝位。

```
 var len = this.length; 
```

第一条语句缓存`this.length`值，这是一个计算属性。这对后面的计算很有用。

```
 start = start < 0 ? max(start + len, 0) : min(start, len);
    end   = end   < 0 ? max(end   + len, 0) : min(end,   len); 
```

接下来的两条语句对指数`start`和`end`进行了相同的计算。如果`parameter`为负，则从末端偏移量开始计算索引，否则，将其视为有效索引。一旦计算完成，数值被固定在`0`和`string.length`之间。

```
 len   = end - start;
    if (len <= 0) return changetype<String>(""); 
```

计算出结果字符串的最终长度后，如果恰好小于等于`0`，则返回一个空字符串。负长度的字符串不能存在，所以默认为空字符串。`changetype<String>("")`表达式仅用于隐藏 IDE 中报告的内联类型脚本错误。这种表达没有副作用。

```
 var out = __alloc(len << 1, idof<String>()); 
```

这一行分配了一个类型为`String`的`Block`。AssemblyScript 中的字符串在内部以`utf16`格式表示，每个字符通常用两个字节表示。因此，我们需要将字符串的长度加倍，以计算存储字符串数据需要多少字节。`len << 1`表达式左移一位。这实际上使长度变量的值加倍了。`idof<String>()`方法将编译器的`String`数字内部 id 作为一个常量值返回，用于描述我们正在创建的堆分配类型。最后，`__alloc()`函数本身负责在堆上分配内存，返回一个指向堆分配的指针。现在，我们有了一个复制数据的地方。

```
 memory.copy(out, changetype<usize>(this) + (<usize>start << 1), <usize>len << 1); 
```

因为我们使用相同的格式将字符串值从一个地方复制到另一个地方，所以使用一个简单的`memory.copy(destination, source, count)`函数调用将 WebAssembly 内存从一个地方复制到另一个地方。

`destination`是我们刚刚进行的新的堆分配。源是`changetype<usize>(this)`加上用`(<usize>start * 2)`(或更快的等效`start << 1`)计算的起始偏移。使用`len * 2`(或更快的等价物`len << 1`)计算字节数。)

```
 return changetype<String>(out); // retains 
```

一旦内存被复制，我们可以简单地将它转换为引用类型并返回它。

随意问许多关于`AssemblyScript`内部如何工作的问题。我的博客文章中没有记录的一些设计选择有很多小原因，可能会让其他人感到困惑。

如果你想看看它是什么样子，你可以在这里查看优化的 web 汇编输出[。](https://github.com/AssemblyScript/assemblyscript/blob/master/tests/compiler/std/string.optimized.wat#L4532)

希望这很有趣！

祝福，
[@jtenner](https://dev.to/jtenner)