# 注:Ruby Conf 台湾 2019

> 原文：<https://dev.to/dannypsnl/notes-ruby-conf-taiwan-2019-9hi>

### 开始获取

这篇文章是我从会议中得到的笔记和想法。

我谨提及以下主题:

*   拼音文字类型检查器(陡)
*   GC 压缩

### 对拼音进行类型检查

#### 红宝石 3

基本上，我理解 Matz(Ruby 的创建者)想要的是一个类型推断器生成一个类型定义文件，然后我们可以修改它使之更精确。最后，检查器根据您修改的文件检查程序。这样我们可以避免输入任何额外的信息，但仍然有一个静态类型分析器。

我有几个问题:

*   如果我在修改类型定义文件后重新生成它会怎样？
    *   似乎马上就要被取代了
*   它如何避免辛德雷·米尔纳式的系统限制？

    例如，保存未指定类型的值列表的可变单元格。
    p.s .我们能做的就是[价值限制](http://users.cs.fiu.edu/~smithg/cop4555/valrestr.html)这里。无论如何，我们需要对类型系统进行一些扩展。

#### [陡峭](https://github.com/soutaro/steep)

[视频](https://youtu.be/KU1JM4NSKe8)

然后，我们继续前进，因为我有更多的陡峭的创造者(松本刚太郎)XD 交谈。

我不会在这里谈论太多的类型推断，但我会就松本刚太郎的问题谈一点。

他的问题是:

```
class Array<T>
  def `[]=`:
    # for arr[1] = T.new
    (Integer, T) -> T
    # for arr[0, 1] = T.new
    | (Integer, Integer, T) -> T
    # for arr[0, 1] = [T.new]
    | (Integer, Integer, Array<T>) -> Array<T>
    # for arr[0..1] = T.new
    | (Range<Integer>, T) -> T
    # for arr[0..1] = [T.new]
    | (Range<Integer>, Array<T>) -> Array<T>
end 
```

现在我们有一个打错的例子:

```
arr = [1, 2, 3] # Array<Integer>
arr[0] = "foo" 
```

从人类的角度来看，我们预计会出现类似`expected: Integer but got: String`的错误。然而，对于重载，我们必须尝试其他方法，因为我们不能确定它是否匹配。所以检查器继续进行，并在最终定义和报告`expected: Array<Integer> but got: String`中失败，这有点令人困惑。

让我们考虑更一般的重载，这里一般意味着如果重载是一个普通的方法而不是一个操作符，那么语义的限制就更少。

```
class C<T>
  def foo:
    (Integer, T) -> T
    | (Integer, Integer, T) -> T
end 
```

我认为，在这种情况下，我们只能说找不到 blabla 的方法。例如:

```
c = C<String>.new
c.foo(1, 2) 
```

我们不知道用户试图通过遗漏一个参数来匹配`(Integer, Integer, T) -> T`，或者试图键入`c.foo(1, "2")`但输入错误。

但是，在我的理解中，我们不能靠问题给 Ruby 增加更多的运算符。

所以操作符实际上不同于普通的方法。例如:

```
arr = [1, 2, 3]
arr[0] = 2
arr[0, 2] = 9 
```

如果`arr[0, 2] =`没有遵循右手边的表达式，那么它实际上代表了与`arr[0] = 2`相同的类型，这显然是错误的。所以我们可以依靠这个额外的语义，将`[]=`操作符的类型重新定义为:

```
class Array<T>
  def `[]=`:
    (Integer) -> (T) -> T
    | (Integer, Integer) -> (T) -> T
    | (Integer, Integer) -> (Array<T>) -> Array<T>
    | (Range<Integer>) -> (T) -> T
    | (Range<Integer>) -> (Array<T>) -> Array<T>
end 
```

但是因为我们实际上不希望赋值遗漏了右边，所以我们必须为这个特殊的函数有另一个特殊的符号，所以它可能是:

```
class Array<T>
  def `[]=`:
    (Integer) => (T) -> T
    | (Integer, Integer) => (T) -> T
    | (Integer, Integer) => (Array<T>) -> Array<T>
    | (Range<Integer>) => (T) -> T
    | (Range<Integer>) => (Array<T>) -> Array<T>
end 
```

`=>`切语义步骤。`->`后跟 finally 返回类型。

其中代表:

```
class Array<T>
  def `[]=`:
    [Integer] = T -> T
    | [Integer, Integer] = T -> T
    | [Integer, Integer] = Array<T> -> Array<T>
    | [Range<Integer>] = T -> T
    | [Range<Integer>] = Array<T> -> Array<T>
end 
```

在我和松本刚太郎提到这个想法后，他指出新的语法可能不是一个好主意，这在上下文中也是有意义的。既然打破目前的程序不会是任何人想要的。但是 in 类型检查器可以使用抽象来做更好的推断。

### GC 压缩

[视频](https://youtu.be/0ypPiULlKfQ)

为什么我们需要压实？如果没有压缩，我们可能会由于碎片堆而导致内存不足。什么是碎片堆？

这是:

[![](img/af55ab917c85476399fd74a302c24a8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_X-dje8h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dannypsnl.github.io/assets/img/ruby_conf_2019/dirty_room.jpg)

压实后:

[![](img/01292e9fb40c7b1015070ca66af57190.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7W-0m92Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dannypsnl.github.io/assets/img/ruby_conf_2019/clean_room.jpeg)

如果不压缩，我们会发现仍然有足够的空间，但不是连续的空间，所以不能分配对象。

#### 两指紧握

Lisp 使用算法。这个想法是使用两个指针，一个从左向右移动(理想情况下)称为 free，一个从右向左移动称为 scan，free 在获得一个空块时停止，scan 在获得一个非空块时停止，当两者都停止时，交换块，结束这个过程，直到它们指向同一个块，然后我们更新所有引用。

缺点:只使用固定大小的堆->如何改进？

#### 钉住位

当 C 中的一个指针指向 Ruby 中的一个引用，而 GC 移动了这个对象或者删除了它，会发生什么？我们可以分成不同的情况:

*   移动，在这种情况下，空引用导致退出
*   删除，同上
*   移动并移入另一个对象，在这种情况下，会发生 UB，因为你可能把一个数组对象当作一个字符串对象，这种行为是随机的，这是最疯狂的情况

为了避免它们，我们必须告诉 GC C 指针持有的是什么，所以在 C 代码中不仅仅是分配和释放内存，还必须写类似于`gc_c_mark(pointer_to_ruby_object)`的东西，这样 GC 就知道这些对象必须被锁定，它不能移动它们。

#### 允许在 C 中运动

以下内容与如何在 C #中允许移动有关

*   压缩回调

    压缩完成后，GC 调用 C 回调函数，让 C 有机会更新它的引用。

*   无 Pin 标记

    对引用对象不使用 pin 函数，因此 GC 可以管理该对象。

*   新定位功能

    `rb_gc_location`将返回对象的新位置。

#### 已知问题

```
Ruby Object            C Object
    |                      |
    |                      |
    \---> Third Object <---/ 
```

Ruby 自动标记一些对象(gc_mark_no_pin)，而不是从 c 中标记。

所以当压缩程序运行时，GC 会移动第三个对象，但是 C 仍然引用旧的位置，所以程序会爆炸。

#### 调试 GC

1.  最大混乱:双倍堆
2.  僵尸对象:让一些插槽不能被 GC，总是用一个僵尸对象填充它
3.  地址消毒剂:[https://github.com/google/sanitizers/wiki/AddressSanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizer)

#### 更多信息

*   [https://bugs.ruby-lang.org/issues/15626](https://bugs.ruby-lang.org/issues/15626)
*   [https://www . ruby-forum . com/t/r b-GC-register-address-or-r b-GC-mark/219828/2](https://www.ruby-forum.com/t/rb-gc-register-address-or-rb-gc-mark/219828/2)
*   [https://ruby-hacking-guide.github.io/gc.html](https://ruby-hacking-guide.github.io/gc.html)

### 附加部分

剩下的部分，我没有足够的知识来整理，或者我没有看，或者我很懒。以下是视频链接。

*   [红宝石自动区分](https://youtu.be/Drxa_DiLV3s)
*   [虚拟机:它们的共同之处以及它们的特殊之处](https://youtu.be/x6FrRQMF5tg)
*   [百万之旅](https://youtu.be/Dtn9Uudw4Mo)

### 结论

是的，另一个我参加的 Ruby 会议，虽然我不知道 Ruby:)。