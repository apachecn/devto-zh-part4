# 缓冲起来

> 原文：<https://dev.to/quoll/mapping-the-way-15j2>

在通往持久数据结构的曲折旅程中的第 4 部分。第三部是[这里的](https://dev.to/quoll/a-simple-structure-in-binary-6lj)，或者回到[开头的](https://dev.to/quoll/what-s-with-data-structures-2fk7)。

提醒一句:和上一篇文章一样，这篇文章也非常代码化。我详细解释了这一切，所以它可能会感觉像沼泽下来。但这也是许多工作向前发展的基础，所以我认为在每个方面都要明确是很重要的。

# 缓冲区

今天我要换换口味，转战 Java。这有几个原因:

*   Java 是一种比 c 语言更高级的语言。我曾试图在过去的帖子中展示字符串，但我认为我最终会花费太多时间来解释它，而没有任何好处。我只想说，没有人想知道为什么我们要用`strncat`而不是`strcat`，使用更大的缓冲区来临时保存字符串，并且随着字符串大小的增加，可能不得不分配更大的缓冲区。
*   Java 是一种面向对象的语言。这允许对象存储一些上下文，而 C 需要显式地传递上下文。对象也是通过引用来引用的，指针就消失了。
*   Java 有一种叫做`Buffer`的类。这些可以代表大的内存块，就像我们在 c 语言中从`malloc`得到的一样。
*   Java 不能像 C(或 Erlang)那样在缓冲区上分层记录。如果您曾经使用过 Erlang，这是该语言的一个可爱特性)。但是它确实提供了对缓冲区的寻址访问，使用了像`getLong`和`getByte`这样的方法。这需要更多的工作，但我们仍然可以得到我们想要的功能。
*   Java 缓冲区是专门为 I/O 设计的，这意味着它们可以很容易地发送到磁盘。
*   Java 支持内存映射文件，缓冲区是实现这一点的接口。
*   任何能用 Java 完成的事情都可以用运行在 Java 虚拟机(JVM)上的任何其他语言来完成。这包括我的目标语言之一: [Clojure](https://clojure.org/) 。

我的另一个目标系统是 Javascript，所以对于任何对 JVM 不感兴趣的人，请相信我会实现的！最终。

# 获得缓冲

在 C 语言中，我们使用了`malloc`调用。在 Java 中，我们可以通过请求正确大小的缓冲区来做类似的事情。要求的大小是每个元素的大小乘以元素的数量。在 C 语言中，用`sizeof`操作符很容易得到结构的大小，但是在 Java 中没有这样的操作符。这部分是因为 Java 中的对象总是有额外的空间分配给方法和类型信息，所以内存的使用与 c 中存储的数据没有直接关系，这意味着我们需要自己计算大小。

在这种情况下，我们将为元素使用两个整数:元素的`next`指针和`value`。那是`2 * sizeof(int)`，除了我们没有`sizeof`操作。没关系，因为基本类型的尺寸都包括在内:
`int ELEMENT_SIZE = 2 * Integer.BYTES`

然后我们可以在`ByteBuffer` :
上用静态的`allocate`方法分配 10 个元素的`ByteBuffer`

```
ByteBuffer buffer = ByteBuffer.allocate(ELEMENT_SIZE * 10); 
```

Enter fullscreen mode Exit fullscreen mode

Java 是一种面向对象的语言。这意味着我们可以使用一个对象来管理缓冲区并为我们分配元素。我将调用这个对象`BufferList`,因为它将管理一个表示在缓冲区中的列表。这个对象也是存储我们需要的常量值的自然位置，比如数组大小，以及`Element`对象部分的偏移量。

`Element`的第一部分是保存`next`指针的整数。由于这是在对象的开始，它将从对象偏移`0`。第二部分是包含`value`的整数。这将从对象的起点偏移`1`整数的大小。我们可以用字节(`1 * Integer.BYTES`，它是 4 个字节)，或者如果我们将相同的数据表示为整数，那么我们可以将其表示为`1`的偏移量。

这是我们 BufferList 类的开始:

```
 public static int ELEMENT_SIZE = 2 * Integer.BYTES;
  public static int NULL = -1;

  private static int NEXT_OFFSET = 0;
  private static int VALUE_OFFSET = 1;

  private ByteBuffer buffer;
  private int nextAvailable;

  public BufferList() {
    this(10);
  }

  public BufferList(int length) {
    buffer = ByteBuffer.allocate(ELEMENT_SIZE * length);
    nextAvailable = 0;
  } 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们处理的缓冲区(就像 C 中的`malloc`)，以及每个元素的大小和每个元素中字段的位置。我们在 Java 中手动处理它，而 C 有工具为我们做，但是我们仍然得到同样的效果。

# 缓冲管理

`BufferList`对象不仅仅保存缓冲区；它也在管理它。为此，它通过使用`nextAvailable`值来记住它可以分配空间的最新位置。构造函数将其初始化为缓冲区的开始，偏移量为`0`。

在我们的 [Javascript 链表](https://dev.to/quoll/a-simple-structure-2cmh#listConstruction)中，我们通过使用`Element`构造函数创建了一个新元素来添加到列表的前面。为了在 C 中做[同样的事情，我们有一个助手函数来分配所需的内存并设置值，然后这个函数被更新为](https://dev.to/quoll/a-simple-structure-in-binary-6lj#add_element)[一个从整个缓冲区捕获所需内存的函数](https://dev.to/quoll/a-simple-structure-in-binary-6lj#init_element)。

为了在 Java 中做同样的事情，我们可以引入一个`addToHead`函数。这将像 Javascript 代码那样调用元素构造函数，但是它也可以提供分配的缓冲区的上下文和下一个可用空间的位置。Java 的另一个好处是方法重载，因此可以创建一个额外的`addToHead`方法，而不需要提供列表。这只是创建单个元素列表的一个快捷方式，只是用一个`null`值调用该方法的主版本，用于列表的下一部分。

```
 public Element addToHead(int value) {
    return addToHead(null, value);
  }

  public Element addToHead(Element nextElt, int value) {
    if (nextAvailable * ELEMENT_SIZE >= buffer.capacity()) {
      throw new RuntimeException("Out of memory");
    }
    return new Element(nextAvailable++, nextElt, value);
  } 
```

Enter fullscreen mode Exit fullscreen mode

`addToHead`方法做的第一件事是确保缓冲区还没有满。在这段代码中不会出现这种情况，因为有 10 个元素的空间，而我只要求 9 个元素，但是不做检查我会不舒服。(我也应该用 C 代码来做！)

我们可以看到，主要工作是在最后一行完成的。它将当前偏移量用于下一个可以分配的元素。它将它和参数一起传递给`Element`构造函数。此后，递增下一个可用元素索引。这里没有明确提到缓冲区，因为它将在`BufferList`类的上下文中自动传递。我们现在来看看这是如何发生的。

# 元素

由于`Element`对象总是与创建它的缓冲区相关联，因此`Element`对象与缓冲区管理器相关联是有意义的。Java 通过允许`Element`类成为`BufferList`的内部类来支持这一点。这样，它就可以自动访问所有的静态常量和分配的缓冲区。

将一个`Element`与其相关数据相关联的最佳方式是获取引用相关数据的缓冲区的一个*片*。这是通过将缓冲区的`position`设置为切片的开始，将`limit`设置为结束来实现的。这些操作的每个方法都返回修改后的缓冲区，所以这些操作可以被链接:
`buffer.position(offset).limit(offset + ELEMENT_SIZE).slice()`

这些对象的数据将一致地表示整数，因此可以通过在`IntBuffer`中表示它们来将字节重新解释为整数。

在 C 示例中，元素不断地从其索引中导出到缓冲区之外。在 Java 中，`Element`对象可以在构造过程中得到它所需要的一切，这意味着如果我们不保存它，它将会丢失元素的索引，所以它进入了一个`index`字段。

我们还需要一些不同的构造函数。

## 构造函数

第一个构造函数只接受元素的`index`。这允许它捕获缓冲区的适当部分，包装可能已经存在的任何值。这是我们创建新元素的方式，也是我们读取现有元素的方式。

下一个构造函数使用前一个构造函数从主缓冲区捕获适当的片，然后设置`next`和`value`字段。注意，由于这段代码使用了`Element`对象，因此`next`不需要像 C 代码那样作为元素索引值传递。相反，它需要一个实际的元素。

最后一个构造函数只是为了方便起见，它没有一个`next`值，表明它只是列表末尾的元素。它所做的就是调用另一个使用`next`元素的`null`值的构造函数。

```
 Element(int index) {
    this.index = index;
    int offset = index * ELEMENT_SIZE;
    intBuffer = buffer.position(offset).limit(offset + ELEMENT_SIZE).slice().asIntBuffer();
  }

  Element(int index, Element next, int value) {
    this(index);
    setNext(next);
    setValue(value);
  }

  Element(int index, int value) {
    this(index, null, value);
  } 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们有了从缓冲区包装数据的`Element`对象。所以现在我们需要提供访问数据的 *getter* 和 *setter* 方法。

## 吸杂

`getIndex`的方法只是引用创建`Element`对象时提供的索引。`getNext`和`getValue`方法更有趣。

`getValue`从值的偏移量读取整数，定义为`VALUE_OFFSET`。

`getNext`不同，因为它需要返回一个`Element`对象。它在`NEXT_OFFSET`读取整数作为要读取的元素的索引。如果这个数字是`NULL`(定义为`-1`)，那么没有对象被返回。否则，调用第一个构造函数，创建一个从给定位置读取的对象。

```
 public int getIndex() {
    return index;
  }

  public int getValue() {
    return intBuffer.get(VALUE_OFFSET);
  }

  public Element getNext() {
    int nextId = intBuffer.get(NEXT_OFFSET);
    return nextId == NULL ? null : new Element(nextId);
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 设定者

元素的`index`永远不会改变，所以它不需要 *setter* 方法。这意味着只需要定义`setNext`和`setValue`。同样，如果提供的对象是`null`，则`next`值被设置为`NULL` ( `-1`)。注意`this`是从 setters 返回的，这样就可以在一行中调用多个 setters，就像在第一个构造函数中调用 slice 一样。这不是必需的，但这是一个有用的标准。

```
 public Element setValue(int value) {
    intBuffer.put(VALUE_OFFSET, value);
    return this;
  }

  public Element setNext(Element next) {
    intBuffer.put(NEXT_OFFSET, next == null ? NULL : next.getIndex());
    return this;
  } 
```

Enter fullscreen mode Exit fullscreen mode

# 打印

就像我们在针对`listString` 的 [Javascript 代码中所做的一样，我们可以递归地遍历列表。与 Javascript 不同，只有当`+`符号左边的值已经是字符串时，才会使用`+`运算符将数字转换为字符串。这里不是这种情况，所以我们使用`Integer.toString()`方法来完成。除此之外，该函数与 Javascript 函数`listString`非常相似。](https://dev.to/quoll/a-simple-structure-2cmh#listString) 

```
public static String listString(Element element) {
  Element next = element.getNext();
  String valueString = Integer.toString(element.getValue());
  if (next == null) return valueString;
  else return valueString + ", " + listString(next);
} 
```

Enter fullscreen mode Exit fullscreen mode

`listString`函数不引用缓冲区本身，而是使用当前元素来获取它的信息。因此，该方法可以是静态的，这意味着它不需要访问其上下文的任何本地值。其实没什么关系，只是效率稍微高一点。

然后我们可以在元素上定义一个`toString`方法来调用它。

```
public String toString() {
  return listString(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

# 建筑清单

有了`BufferList`中的缓冲区管理和定义的`Element`对象，构建列表的代码与我们用其他语言构建的非常相似。

```
BufferList bufferList = new BufferList(10);
BufferList.Element list = bufferList.addToHead(34);
list = bufferList.addToHead(list, 21);
list = bufferList.addToHead(list, 13);
list = bufferList.addToHead(list, 8);
list = bufferList.addToHead(list, 5);
list = bufferList.addToHead(list, 3);
list = bufferList.addToHead(list, 2);
list = bufferList.addToHead(list, 1);
list = bufferList.addToHead(list, 1);
System.out.println(list); 
```

Enter fullscreen mode Exit fullscreen mode

运行它会给出与我们在之前的迭代中看到的完全相同的结果

```
~/src$ java buffer.ListExample
1, 1, 2, 3, 5, 8, 13, 21, 34 
```

Enter fullscreen mode Exit fullscreen mode

这个程序的完整 java 代码可以在[这里](https://gist.github.com/quoll/98526b593f6f0150060acb9f1e983857)找到。这些都在一个名为`buffer`的包中，所以如果你想编译它，不要忘记把它放在一个同名的目录中。

# 重述

这篇文章展示了在内存缓冲区中存储链表结构的原理是如何被翻译成另一种语言的。可能缺少将内存直接映射到所需记录的工具，但是对象定义可以用来用 getters 和 setters 来填补这一空白。同时，对象提供的上下文封装可能隐藏了管理内存缓冲区的一些手动方面。

# 接下来

敏锐的读者可能已经注意到缓冲区在一个名为`nio`的 Java 包中。这代表“新 I/O ”,指的是在网络和磁盘上高效存储和检索数据的操作库。我们将在下一篇文章中开始研究如何保存和检索我们一直在处理的数据。