# Ruby 紧凑内部结构

> 原文：<https://dev.to/oryanmoshe/ruby-compact-array-of-hashes-19ho>

关于 ruby 的`compact`方法，我遇到了一个奇怪的现象，我想我应该与你分享我的发现。
当我在 monday.com[的“网格”仪表板上工作时，我有一个散列数组，其中一些散列是空的。
我需要摆脱这些。](https://monday.com)

* * *

## 先底线

为了得到我想要的结果，我只是做了`a.reject { |v| v.blank? }`而不是`a.compact`。

* * *

## 期望 VS 现实。

当我在数组上使用`compact`时，我对结果感到有点惊讶。

```
a = [{ key: 'value' }, { key: 'value2' }, {}]

puts a.compact
#[{ key: 'value' }, { key: 'value2' }, {}] 
```

我期望`compact`方法只是遍历数组中的每个元素，检查它是否是`present?`，如果是则返回它。
类似这样的:

```
def compact
  select { |value| value.present? }
end 
```

因此，当我有一个数组，其中的空哈希值不会消失时，我必须找到解释。

* * *

## 幕后实现

像往常一样，我深入研究了实现。

从过去的研究中，我知道 ruby 的 hash 也有一个`compact`方法，我知道它非常简单，对于 Hash 中的每个键，我们检查它是否是`nil`，如果不是，我们返回它。
超级容易。类似于我上面的建议，很容易理解，尽管它不能解决问题(因为我们使用`nil?`而不是`blank?`或`!present?` )*

*active support/lib/active _ support/core _ ext/hash/compact . Rb，第 12 行*T2】

```
def compact
  select { |_, value| !value.nil? }
end 
```

然而，在数组中，实现更加复杂。它完全是用 c 实现的

```
static VALUE
rb_ary_compact(VALUE ary)
{
  ary = rb_ary_dup(ary);
  rb_ary_compact_bang(ary);
  return ary;
} 
```

* * *

## C 是爱情，C 是生命

如果你没有任何 C 语言的背景知识，下一部分可能会让你不太感兴趣。我会尽量让它变得容易理解，但是如果你有时间，我强烈推荐你学习 C 语言！

### 指针背景

如果你不知道，我们访问程序内存(存储变量的地方)的方式是通过使用**指针**。
这些指针基本上只是内存中的一个地址，我们可以在那里找到变量的开始(每个变量占用不同的内存量，我们只是从指针的位置开始得到所需的字节数)

数组的工作方式类似(实际上，数组就是一个指针)，假设我们有一个由 3 个字符组成的数组，每个字符由一个字节组成，所有 3 个字节一个接一个地存储在内存中。我们可以去数组中的第一个地址(或者指向那个数组的指针)，取 1 个字节，那是第一个字符。然后我们取另一个，那是第二个，然后是第三个。

```
char arr[3] = {'m', 'i', 'p'}
/*
Behind the scenes it's stored like so:
... 6d 69 70 ...
    ^ arr[0] == *arr
... 6d 69 70 ...
       ^ arr[1] == *(arr + 1)
... 6d 69 70 ...
          ^ arr[2] == *(arr + 2)
*/ 
```

另一件需要注意的事情是:在 C 中，如果我们有一个指针存储在一个变量中，比如说`ptr`，这个变量将包含值的地址。为了得到值*本身*，我们必须使用**“解引用”操作符**(变量名前的星号)，就像这样`*ptr`。

### 回码

上面我们看到了 ruby 的数组`compact`的 C 实现，我们可以看到一个函数接收一个数组，复制它(使用`rb_ary_dup`函数)，`compact`使用`rb_ary_compact_bang`函数(“砰”与“！”相同)符号，所以它基本上运行`compact!`，然后返回复制的、压缩的数组。

为了找到那个`rb_ary_compact_bang`函数，我查阅了 [ruby 的源代码](https://github.com/ruby/ruby/blob/9d298b9dab831f966ea4bf365c712161118dd631/array.c):

*ruby/array.c，第 5022 行*T2】

```
static VALUE
rb_ary_compact_bang(VALUE ary)
{
  VALUE *p, *t, *end;
  long n;

  rb_ary_modify(ary);
  p = t = (VALUE *)RARRAY_CONST_PTR_TRANSIENT(ary); /* WB: no new reference */
  end = p + RARRAY_LEN(ary);

  while (t < end) {
    if (NIL_P(*t)) t++;
    else *p++ = *t++;
  }
  n = p - RARRAY_CONST_PTR_TRANSIENT(ary);
  if (RARRAY_LEN(ary) == n) {
    return Qnil;
  }
  ary_resize_smaller(ary, n);

  return ary;
} 
```

不要关帖！这段代码对于没有 C 语言经验的人来说并不友好，但是大部分都是内存管理，一些正确分配内存的游戏，调整数组大小以防止内存泄漏等等。如果有人愿意，我可以在另一篇文章中做更深入的探讨。

有趣的部分是这个:

```
while (t < end) {
  if (NIL_P(*t)) t++;
  else *p++ = *t++;
} 
```

### 逐行

正如我在上面解释的，数组只是一组变量，一个接一个地存储在我们程序的内存中，所以我们可以得到一个指向第一个变量的指针，然后每次都加 1 来得到下一个变量(`t++`增加`t`1)

这里我们有一个循环，从数组的开头开始，用我们在`t`变量(指向数组的指针)中的当前位置，我们迭代每个元素，直到我们到达数组的`end`(数组开头的地址+数组的长度)

每次我们都会检查当前值是否为`nil`(使用*include/ruby/ruby . h line 482*中定义的`NIL_P`宏)

如果是`nil`,我们就增加指针，在数组中前进一个点。
如果不是`nil`，我们将原来的`t`(记住，`*`解除了指针的引用)中的当前值放入我们保存在指针`p`中的另一个数组中，然后将指针前移一点。

## 底线

简而言之，这和做`a.reject { |v| v.nil? }`是一样的，但是更有内存意识和效率。

为了显示效率上的差异，我对一个包含 20，000，000 个交替整数和`nil`
[![](img/c6290db6a23bc1e1ee3ef1d5cab95b6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RVCWnXvO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k9o8p26uqlr0mvuq5l4c.png)
元素的数组进行了两种方式的基准测试。正如您所见，使用 C 空指针消除的数组的`compact`方法比`reject`方法快 6.6 倍！

我希望你喜欢学习一些 ruby 的内部机制！

*这是 Hash `compact`的 activesupport 实现，如果你想看 C 实现你可以去*[ruby/Hash . C](https://github.com/ruby/ruby/blob/4daff3a603d1a8b2656e82108e2f7d0abf8103c9/hash.c)line 4110*，但是很相似！