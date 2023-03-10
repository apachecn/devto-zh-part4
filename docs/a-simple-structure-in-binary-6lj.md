# 二进制中的一种简单结构

> 原文：<https://dev.to/quoll/a-simple-structure-in-binary-6lj>

这个故事的第三部分花了太长时间才说到点子上。前一部分可以在这里找到[，或者你可以从](http://bit.ly/2TEwRvA)[开始](https://dev.to/quoll/what-s-with-data-structures-2fk7)。

# 使用 C

C 引入了许多概念，这些概念后来演变成了我们今天在 C++、Java、Javascript、Python 和 Ruby 等语言中看到的概念。

更准确地说，C 是第一种让大多数程序员接触到这些概念的语言。C 语言的历史可以追溯到 B 语言(程序员显然有比给他们的语言命名更好的想法)，这可以追溯到 BCPL 和 ALGOL。但是我只是告诉你这些事情听起来很聪明，因为我从来没有真正使用过这些古老的语言。😊

就像上一篇文章中的 Javascript 示例[一样，C 可以声明一个包含*姓名*和*年龄*的记录，尽管有一些不同。与 Javascript 最重要的区别在于，C 需要指定*名字*是字符串，*年龄*是数字。](http://bit.ly/2TEwRvA#person) 

```
struct person {
  char* name;
  long age;
};
struct person a_person;
a_person.name = "Mary";
a_person.age = 32; 
```

Enter fullscreen mode Exit fullscreen mode

这看起来有点不同，我应该解释一下为什么会这样。

c 采用了一种非常字面的数据视图，按照描述的精确方式在内存中布局。这为程序员提供了内存中所有内容的精确信息。然而，这也给程序员留下了很多余地，使其更容易引入错误。现代语言从开发人员那里拿走了大量工作，使代码更加灵活和健壮，但代价是一些速度和精确地知道数据在做什么。幸运的是，在现代硬件上，速度的代价是微不足道的。

`person`数据结构保存一个指向一个字符串和一个长整数的*指针*。(表示*指针*的语法是`*`字符)。在 C 语言中处理字符串实际上很难，因为它们大小不一，所以我想避免过多地谈论这个问题。目前，这个名字还不在这个结构中。相反，该结构保存一个指向姓名字符串的*指针*和一个包含年龄的整数。

与其他语言不同，我们实际上可以看到这些数据在内存中的确切布局。可以向对象询问它的地址，然后这可以被当作一系列字节(C 中的一个`unsigned char`)来处理，可以用十六进制:
来打印

```
for (int i = 0; i < sizeof(struct person); i++) {
  printf("%02X ", ((unsigned char*)&a_person)[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

从现代编程的角度来看，这是可怕的。数据结构应该被认为是数据结构，而不是其他。这段代码将其视为一系列字节。可能是增长见识，但也是自找麻烦。

但是现在我们可以看到字节了，它们看起来像什么呢？

```
21 30 D9 04 9C 7F 00 00 20 00 00 00 00 00 00 00 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它分成几个部分:

| 字符*名称 | 漫长的岁月 |
| --- | --- |
| `21 30 D9 04 9C 7F 00 00` | `20 00 00 00 00 00 00 00` |

我们在看什么？虽然*名称*字段被称为*指针*，但它实际上只是一个长数字。这是给出*名称*字符串的存储位置的数字(我们知道它保存`"Mary"`)。*年龄*字段包含 32 个数字。在十六进制中，这表示为 20 (2*16 + 0*1)。

## 年龄

一个意想不到的部分是，年龄后面有所有这些零。如果数字是`0000000000000020`可能更有意义，但这似乎是倒退了。这与我们将数字分成单个字节的事实有关。这里的想法是，当你向右移动时，内存地址会增加。低位地址包含数字的较小部分(100、10 和 1)，而高位地址包含较大部分(百万、十亿等)。但是当我们像这样从低到高打印它们时，它们会向后看。

这实际上是英特尔 CPU 的一个怪癖(Windows 和 Mac 通常运行于其上)。他们明确选择用这种方式来表示他们的数字。其他类型的 CPU 选择了相反的方式来表示数字。苹果在 2006 年之前使用的 PowerPC CPUs 就是这种情况，工业中其他地方使用的许多其他处理器也是如此。将小部分数字存储在低位的计算机称为“[小端](http://catb.org/jargon/html/L/little-endian.html)”体系结构，而先存储大部分数字的计算机称为“[大端](http://catb.org/jargon/html/B/big-endian.html)”。这是对黄邦贤·斯威夫特的《格列佛游记》的一个愚蠢的引用，在那里，利力浦特人和布卢斯库人因为[一个煮鸡蛋的哪一头应该打破](https://en.wikipedia.org/wiki/Lilliput_and_Blefuscu#Satirical_interpretations):大头还是小头而开战。

我们也可以看到有很多零。这些数字占用了 8 个字节(64 位)。真的有必要存储大到可以占据所有空间的数字吗？似乎不是，因为 C 允许几个更小的数字:

*   `int`是 4 个字节，即 32 位。
*   `short`是 2 个字节，即 16 位。
*   `char`是 8 位。

这里的一个问题是，现代 64 位 CPU 的 C 编译器确保数据结构总是在 64 位/8 字节边界上对齐。这既加快了速度，又确保了数据总是被设置为由 CPU 正确处理，因为只有当 64 位数字从 8 的倍数开始时，它们才能被加载到 CPU 中。

我**可以**将年龄设为`short`，或者甚至是`char`，但是数据不会再小了，因为这个结构无论如何都需要扩展到下一个 8 字节的边界。额外的字节将不会被使用。事实上，我在早期的迭代中尝试过这种方法，但是那些未使用的字节以随机数字结束，我认为这对于这篇文章来说会更加混乱。

你知道为什么大多数人会转向更现代的语言吗？

## 名称

名字里的数字看起来像是随机的废话，从某种意义上来说确实如此。事实上，如果我再次运行这个程序，我会得到一个不同的数字:
`21 90 E4 05 98 7F 00 00 20 00 00 00 00 00 00 00`

这是为什么呢？它实际上是几种东西的组合。如果一个恶意程序在你的计算机上运行，它可以猜测你的数据将会在哪里，它可以恶意地读取或修改这些信息。为了减轻这一点，在 2000 年代早期，一些随机性被引入到内存位置中(这更多地影响了内核地址，但是，嘿，我是彻底的)。更重要的原因是，大多数操作系统允许多个程序同时运行，一般来说，它们不应该相互交互。这部分意味着他们每个人都有自己的内存视图，操作系统可以决定内存的实际位置，以及它是否可能位于磁盘的某个位置。这是一个叫做虚拟内存管理的复杂话题，不在这里讨论。但是万一有人读到这里感到失望，我将在以后回到这个主题，因为它在处理内存映射文件访问时非常重要。

从这个调查中得到的最重要的事情是，指向保存这个人的*名字*的字符串的指针实际上只是一个数字。当然，这是一个很大的数字，而且这个数字只对计算机有意义，但它只是一个数字，这个数字指定了一个位置。我将很快回到这个想法。

# 一个链表

改编前一篇文章中的 [Javascript 链表](http://bit.ly/2TEwRvA#list)，我们得到这个:

```
struct element {
  struct element* next;
  long value;
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为这个例子是一个数字列表，所以我将值限制为只保存该类型。

`next`指针指向另一个元素结构。它可以用来指向任何内存地址，包括本地元素对象，或者根据需要请求的原始内存块。这些是使用`malloc`库函数创建的，其中分配了所需数据所需的字节数:

```
struct element* list = malloc(sizeof(struct element)); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，访问结构的元素是用类似于`element.value = 5`的语法完成的。然而，这次`list`变量保存的是一个指向元素的指针(一个 64 位的数字)，而不是一个元素。有两种不同的语法来获取元素内部的数据:

*   我们可以使用`*`操作符*解引用*指针，然后正常访问字段:`(*list).value`
*   我们可以使用指针字段访问操作符`->` : `list->value`

使用指针字段访问操作符通常更好。

在 Javascript 中，我们使用了一个`constructor`函数，它接受一个新分配的对象并在其中设置值。类似地，我们将使用一个函数来创建元素并为我们设置值，然后返回它。

```
struct element* add_element(struct element* list, long value) {
  struct element* new_head = malloc(sizeof(struct element));
  new_head->next = list;
  new_head->value = value;
  return new_head;
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以用类似的方式创建列表:

```
 struct element* list = add_element(NULL, 34);
  list = add_element(list, 21);
  list = add_element(list, 13);
  list = add_element(list, 8);
  list = add_element(list, 5);
  list = add_element(list, 3);
  list = add_element(list, 2);
  list = add_element(list, 1);
  list = add_element(list, 1); 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们使用`NULL`作为无效地址。这只是数字 0，CPU 知道这是一个无效的地址。

打印出来就能看到内容:

```
while (list != NULL) {
  printf("%d", list->value);
  list = list->next;
  if (list != NULL) printf(", ");
}
printf("\n"); 
```

Enter fullscreen mode Exit fullscreen mode

为什么我没有用递归把它打印出来？我在 Javascript 中使用递归来构建一个字符串，它随着我们在列表中的前进而慢慢增长，然后打印出最终结果。这次我边走边打印，而不是构建一个字符串。这是因为在 C 语言中构建字符串很笨拙(我确实有构建字符串的代码，但是我认为对于这篇文章来说它太乱了)。可以在一个调用自身的函数中完成随用随印，但是因为不会返回值，所以这不是我们在使用递归时真正想到的。

运行 C 程序给我们列表:
`1, 1, 2, 3, 5, 8, 13, 21, 34`

因此，我们知道我们拥有与 Javascript 中的列表大致相当的代码。

# 现在去哪里了？

仅仅是复制我在 Javascript 中已经有的东西就要做很多工作。何必呢？

正如我们在上面的`person`结构中看到的，我们可以看到该结构是作为数据在内存中布局的。让我们以字节为单位打印列表:

```
while (list != NULL) {
  for (int i = 0; i < sizeof(struct element); i++) {
      printf("%02X ", ((unsigned char*)list)[i]);
  }
  list = list->next;
  printf("\n");
} 
```

Enter fullscreen mode Exit fullscreen mode

| 结构元素*下一个 | 长值 |
| --- | --- |
| `40 D3 64 E1 FF 7F 00 00` | `01 00 00 00 00 00 00 00` |
| `20 D3 64 E1 FF 7F 00 00` | `01 00 00 00 00 00 00 00` |
| `00 D3 64 E1 FF 7F 00 00` | `02 00 00 00 00 00 00 00` |
| `E0 D2 64 E1 FF 7F 00 00` | `03 00 00 00 00 00 00 00` |
| `C0 D2 64 E1 FF 7F 00 00` | `05 00 00 00 00 00 00 00` |
| `A0 D2 64 E1 FF 7F 00 00` | `08 00 00 00 00 00 00 00` |
| `80 D2 64 E1 FF 7F 00 00` | `0D 00 00 00 00 00 00 00` |
| `60 D2 64 E1 FF 7F 00 00` | `15 00 00 00 00 00 00 00` |
| `00 00 00 00 00 00 00 00` | `22 00 00 00 00 00 00 00` |

这里一个有趣的方面是指针是如何工作的。从列表的末尾开始(分配的第一个元素)，我们看到倒数第二个元素(值 21，十六进制`15`)指向最后一个元素，其编号为:
`60 D2 64 E1 FF 7F 00 00`

作为一个数字，我们可以把它挤回去，并把数字按正确的顺序排列:
`00007FFFE164D260`
我们可以遍历所有这些地址，从列表的末尾到前面(即按照分配的顺序):

| 然后 |
| --- |
| `00007FFFE164D260` |
| `00007FFFE164D280` |
| `00007FFFE164D2A0` |
| `00007FFFE164D2C0` |
| `00007FFFE164D2E0` |
| `00007FFFE164D300` |
| `00007FFFE164D320` |
| `00007FFFE164D340` |

它不包括列表头的地址，但它遵循与其他列表相同的模式。

注意每一个都比前一个地址多 0x20。正好是 32，恰好比结构的大小多 2 个`long`值。为什么有 2 个额外的`long`值？嗯，这取决于内存分配器(在我的例子中， [glibc](https://www.gnu.org/software/libc/) )。老实说，我不知道为什么在分配之间会出现这个空格。我承认我偷看了一下(不要告诉任何人:访问你没有分配的内存是非法的)。看起来它使用 2 个 longs 来保存管理数据。我不知道是什么类型，但是一些编译器以这种方式支持运行时类型标识符[RTTI]，所以可能就是这样。

无论 glibc 选择如何分配元素，它都可以被更紧密地打包在一起，或者被更远地分开。唯一的规则是它们必须从一个 8 字节的边界开始。同样值得注意的是，即使元素是按顺序分配的，它们也可能是随机分布的。

# 一种不同的记忆

在前面的例子中，列表中的每个元素都是使用`malloc`分配的。但是内存可以用另一种方式分配。如果我们分配一块内存，然后自己分发分配的内存，会怎么样？

我们将提供地址来代替在`add_element`的开头使用`malloc`。把这个版本叫做`init_element` :
更有意义

```
struct element* init_element(struct element* new_head,
                             struct element* list,
                             long value) {
  new_head->next = list;
  new_head->value = value;
  return new_head;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需要从分配一大块内存开始。在这种情况下，我将请求大小为 10 个元素的东西，然后依次使用这些元素。因为内存引用的是`element`结构，如果我们添加到内存地址，那么编译器知道要添加那么多的`element struct`大小到地址。这让我在指针上加一个数字。

```
struct element* memory = malloc(10 * sizeof(struct element));                                                           struct element* list = init_element(memory, NULL, 34);                                                                  list = init_element(memory + 1, list, 21);                                                                              list = init_element(memory + 2, list, 13);                                                                              list = init_element(memory + 3, list, 8);                                                                               list = init_element(memory + 4, list, 5);                                                                               list = init_element(memory + 5, list, 3);                                                                               list = init_element(memory + 6, list, 2);                                                                               list = init_element(memory + 7, list, 1);                                                                               list = init_element(memory + 8, list, 1);                                                                               print_list(list); 
```

Enter fullscreen mode Exit fullscreen mode

这给出了与上次完全相同的输出。让我们再次打印原始数据:

| 结构元素*下一个 | 长值 |
| --- | --- |
| `D0 12 2B F2 FF 7F 00 00` | `01 00 00 00 00 00 00 00` |
| `C0 12 2B F2 FF 7F 00 00` | `01 00 00 00 00 00 00 00` |
| `B0 12 2B F2 FF 7F 00 00` | `02 00 00 00 00 00 00 00` |
| `A0 12 2B F2 FF 7F 00 00` | `03 00 00 00 00 00 00 00` |
| `90 12 2B F2 FF 7F 00 00` | `05 00 00 00 00 00 00 00` |
| `80 12 2B F2 FF 7F 00 00` | `08 00 00 00 00 00 00 00` |
| `70 12 2B F2 FF 7F 00 00` | `0D 00 00 00 00 00 00 00` |
| `60 12 2B F2 FF 7F 00 00` | `15 00 00 00 00 00 00 00` |
| `00 00 00 00 00 00 00 00` | `22 00 00 00 00 00 00 00` |

与我们在对每个元素使用`malloc`时看到的模式相同，但是看看地址。这次它们正好相差 0x10 (16 字节)，这正好是结构的大小。这正是我们所希望的。

编译器在这里做了一些工作，假设`memory`指针指向`struct element`对象。这使它可以通过将任何增加的数字乘以结构的大小来计算对象的起始位置，等等。并不是每种语言都一定会给我们这个，所以我们可以使用原始指针重新实现它，并自己找出东西在哪里。这里使用的指针是`void*`。这是一种特殊的指针类型，编译器不会对照其他指针类型进行检查，因此传递给`init_element`函数也没问题，即使该函数需要一个指向`struct element`的指针。

```
void* memory = malloc(10 * sizeof(struct element));                                                                     struct element* list = init_element(memory, NULL, 34);                                                                  list = init_element(memory + sizeof(struct element), list, 21);                                                         list = init_element(memory + 2 * sizeof(struct element), list, 13);                                                     list = init_element(memory + 3 * sizeof(struct element), list, 8);                                                      list = init_element(memory + 4 * sizeof(struct element), list, 5);                                                      list = init_element(memory + 5 * sizeof(struct element), list, 3);                                                      list = init_element(memory + 6 * sizeof(struct element), list, 2);                                                      list = init_element(memory + 7 * sizeof(struct element), list, 1);                                                      list = init_element(memory + 8 * sizeof(struct element), list, 1); 
```

Enter fullscreen mode Exit fullscreen mode

很乱，但是一模一样。

# 一个小小的调整

当我们像这样管理自己的数据时，我们实际上并不局限于使用内存位置作为指针。例如，在上面的代码中，只有 9 个对象，它们都在程序控制的内存块中。既然是这种情况，那么我们可以引用内存中的偏移量来代替完整的内存位置。更好的是，因为内存中的所有内容都是 16 字节大小，所以我们可以使用`[0,1,2,3,4,...]`来代替`[0,16,32,48,64,...]`的偏移量，只需将它们乘以对象的大小。需要注意的一点是，0 偏移量是有效的(与指针不同)，所以我们可以使用一个特殊的保护值-1 来代替`NULL`来指示列表的结尾。

到目前为止，生成的代码需要调整每个函数。所以让我们展示整个程序: