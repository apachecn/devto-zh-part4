# 储存；储备

> 原文：<https://dev.to/quoll/storage-8gp>

关于如何构建数据库的系列文章的第 5 部分。第四部在这里，[开始在这里](https://dev.to/quoll/what-s-with-data-structures-2fk7)。

# 膨胀

到目前为止，我们已经构建了一个结构来表示链表，并在 Java 的原始缓冲区中表示它们。Javascript 中也存在缓冲区，我确实计划最终在那里演示相同的内容，但是在这个阶段，我更想关注正在构建什么，而不是如何构建它。

前面例子中的链表都是相同的数字序列:`1, 1, 2, 3, 5, 8, 13, 21, 34`。这个列表也是按顺序分配的，这使得数据比大多数应用程序中预期的更有规律。所以让我们尝试一些不同的东西。

首先，让我们创建一个不同的数字序列，它共享第一个列表的最后 4 个数字:`5, 6, 7, 8, 13, 21, 34`。
然后，让我们创建一个不相关的第三个列表:`3, 1, 4, 1, 5, 9, 2, 6`。

因为我们在缓冲区内移动了一点，这表明我在现有的实现中有一个 bug。哎呀。😳

(是的，我可以回到我之前的帖子并修复错误，但这是一个博客，不是一本书！我要认错了)。

`BufferList`最初的定义总是通过设置位置来取一片，然后是限制。当我们只是将*位置*移动到超过所使用的最后一个*限制*的下一个空间时，这是有效的，但是现在它稍微跳跃了一下，这表明我们违反了[缓冲不变量](https://docs.oracle.com/en/java/javase/12/docs/api/java.base/java/nio/Buffer.html)，即*限制*将总是处于或高于*位置*。

要解决这个问题，我们只需要看看新的头寸是高于还是低于当前的限额。如果在上面，那么可以先设置限额，否则可以先设置仓位:

```
Element(int index) {
  this.index = index;
  int offset = index * ELEMENT_SIZE;
  if (offset > buffer.limit()) {
    intBuffer = buffer.limit(offset + ELEMENT_SIZE).position(offset).slice().asIntBuffer();
  } else {
    intBuffer = buffer.position(offset).limit(offset + ELEMENT_SIZE).slice().asIntBuffer();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一句，这段代码只能在 Java 9 和更高版本中运行。在此之前，抽象的`Buffer`类不支持`slice()`，尽管`ByteBuffer`支持。所以在 Java 8 和更早的版本中，需要做如下修改:

```
ByteBuffer bb;
if (offset > buffer.limit()) {
  bb = (ByteBuffer)buffer.limit(offset + ELEMENT_SIZE).position(offset);
} else {
  bb = (ByteBuffer)buffer.position(offset).limit(offset + ELEMENT_SIZE);
}
intBuffer = bb.slice().asIntBuffer(); 
```

Enter fullscreen mode Exit fullscreen mode

如果你尝试了我上一篇关于 Java 8 的文章中的缓冲片，我很抱歉，它们不起作用！

## 建立列表

现在这已经完成了，我们可以建立新的列表了。当然，我们将需要一个更大的缓冲区来存储所有这些，所以我们将它增加到 20。

```
BufferList bufferList = new BufferList(20);
BufferList.Element list1, list2, list3;

list1 = bufferList.addToHead(34);
list1 = bufferList.addToHead(list1, 21);
list1 = bufferList.addToHead(list1, 13);
list2 = bufferList.addToHead(list1, 8);  // note that this is list2
list1 = bufferList.addToHead(list2, 5);
list1 = bufferList.addToHead(list1, 3);
list1 = bufferList.addToHead(list1, 2);
list1 = bufferList.addToHead(list1, 1);
list1 = bufferList.addToHead(list1, 1);

list2 = bufferList.addToHead(list2, 7);
list2 = bufferList.addToHead(list2, 6);
list2 = bufferList.addToHead(list2, 5);

list3 = bufferList.addToHead(6);
list3 = bufferList.addToHead(list3, 2);
list3 = bufferList.addToHead(list3, 9);
list3 = bufferList.addToHead(list3, 5);
list3 = bufferList.addToHead(list3, 1);
list3 = bufferList.addToHead(list3, 4);
list3 = bufferList.addToHead(list3, 1);
list3 = bufferList.addToHead(list3, 3); 
```

Enter fullscreen mode Exit fullscreen mode

当将数字`8`添加到第一个列表中时，我们在列表中保留该点，并在第二个列表中重用它。这意味着两个列表共享一个尾部。如果尾部发生变化，那么两个列表都会看到变化。当我谈到函数式数据结构时，我将回到共享结构的概念。

有了 3 个列表，我们应该能够看到所有 3 个列表的内容:

```
System.out.println(list1);
System.out.println(list2);
System.out.println(list3); 
```

Enter fullscreen mode Exit fullscreen mode

输出为:

```
$ java buffer.MultiExample
1, 1, 2, 3, 5, 8, 13, 21, 34
5, 6, 7, 8, 13, 21, 34
3, 1, 4, 1, 5, 9, 2, 6 
```

Enter fullscreen mode Exit fullscreen mode

# 文件 I/O

此时，可以通过在某个地方存储缓冲区来持久地存储该结构。唯一缺少的是列表的开头，它们与元素`list1`、`list2`和`list3`相关联。这些数据也需要被存储，但是因为我们的缓冲区只保存元素，所以它们将被存储在列表数据结构附近而不是内部。

## 元数据读/写

我们重建列表所需的关于缓冲区的所有元数据都可以在列表开头的索引值中找到。我们可以用一个小方法把它写到一个文件中:

```
static void writeLists(BufferList.Element... lists) throws IOException {
  RandomAccessFile file = new RandomAccessFile("meta.bin", "rw");
  for (BufferList.Element element: lists) {
    file.writeInt(element.getIndex());
  }
  file.close();
} 
```

Enter fullscreen mode Exit fullscreen mode

这将在读/写模式下创建文件，然后遍历提供给它的所有元素，并将它们的索引值写入文件。

读回这个值意味着读取那些索引值，然后请求缓冲区创建相关的元素:

```
static BufferList.Element[] readLists(BufferList bufferList) throws IOException {
  RandomAccessFile file = new RandomAccessFile("meta.bin", "r");
  int length = (int)(file.length() / Integer.BYTES);
  BufferList.Element[] lists = new BufferList.Element[length];
  for (int number = 0; number < length; number++) {
    lists[number] = bufferList.new Element(file.readInt());
  }
  file.close();
  return lists;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将打开文件进行读取，然后根据文件的大小计算出文件中有多少个整数。这给出了要返回的数组的大小，一个循环读取文件中的每个整数，并创建相关的`Elements`来返回。

### 侧注

这两种方法都说明了一些元数据可能是如何存储的，尽管对于现实世界的应用程序，我们可以期待一些更适合实际使用的数据结构的方法。通常，该元数据将被附加到元数据所代表的`Buffer`类，这样读/写它就不会在 API 中作为一个单独的步骤出现。但我认为在这里看到这一切会有所帮助。

## 缓冲区读/写

`BufferList`本身只需要作为单个块写入或读取其缓冲区。

```
public class BufferList {
...
  public void write(String filename) throws IOException {
    RandomAccessFile file = new RandomAccessFile(filename, "rw");
    buffer.position(0).limit(nextAvailable * ELEMENT_SIZE);
    file.getChannel().write(buffer);
    file.close();
  }

  public static BufferList read(String filename) throws IOException {
    RandomAccessFile file = new RandomAccessFile(filename, "r");
    ByteBuffer byteBuffer = ByteBuffer.allocate((int)file.length());
    file.getChannel().read(byteBuffer);
    file.close();
    return new BufferList(byteBuffer);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，缓冲区中唯一被写入的部分是在当前位置和限制之间，这就是为什么这些需要在`write`方法中设置。

## 彼岸

读/写就绪后，我们可以更新主程序来写缓冲区及其元数据:

```
public class MultiExample {
  public static void main(String[] args) throws IOException {
    BufferList bufferList = new BufferList(20);
    BufferList.Element list1, list2, list3;

    // ... populate the lists, as above ...

    System.out.println(list1);
    System.out.println(list2);
    System.out.println(list3);
    bufferList.write("buffer.bin");
    writeLists(list1, list2, list3);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个主程序正在写缓冲区，我们可以稍后重新加载它吗？让我们写第二个程序来试试:

```
public class MultiExample2 {
  public static void main(String[] args) throws IOException {
    BufferList bufferList = BufferList.read("buffer.bin");
    for (BufferList.Element list: readLists(bufferList)) {
      System.out.println(list);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个全新的打开文件的过程，并打印以下内容:

```
$ java buffer.MultiExample2
1, 1, 2, 3, 5, 8, 13, 21, 34
5, 6, 7, 8, 13, 21, 34
3, 1, 4, 1, 5, 9, 2, 6 
```

Enter fullscreen mode Exit fullscreen mode

注意加载所有内容只需要很少的代码？这来自于覆盖缓冲区的`Element`对象的灵活性。

完整的源代码可以在 Github 上找到[。](https://gist.github.com/quoll/192efb8060fcb47bcdca2912f1ca66bc)

## 讨论

如上所述，将元数据添加到类定义中消除了客户端手动处理其他文件的需要，并且还避免了客户端代码直接引用`Element`构造函数。这个博客仍在建立一个基本框架，所以还没有必要清理一切。

Javascript 在[类型数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypedArray)中有一个类似的缓冲对象。然后，这些对象可以像 Java `Buffer`一样四处移动，并且可以被字符串化以便从本地存储中存储/检索，这让我们可以将这种技术应用到那个系统中。

不幸的是，这里编写的代码不能很好地伸缩，因为它需要一次写入或读取整个缓冲区。相反，我们希望有一种机制可以在特定的文件偏移量处读写各个缓冲区。读取可以在`Element`构造函数期间完成，写入可以在元素结束时完成。在现实世界的系统中，我们通常会在元素上放置一个*脏的*标志，并将这些修改后的元素排队，以便在它们更新时写入。对元素的修改很容易被捕获，因为任何修改都已经通过 setters 和 getters 进行了定义，以便修改底层缓冲区。

要在 Javascript 中管理这一点，在本地存储中为每个元素存储一个`TypedArray`将是一个错误，因为会有大量的元素，导致过多的本地存储条目。相反，`TypedArray`对象可以将元素分组，然后提供访问单个元素的 API。例如，当请求具有 1000 个元素的分组的元素索引 1234 时，组号和组内的元素偏移将被计算为:

```
groupSize = 1000;
group = Math.floor(1234 / groupSize);
offset = 1234 % groupSize; 
```

Enter fullscreen mode Exit fullscreen mode

Java 的需求是不同的，因为它可以读/写缓冲区的任何部分，从单个元素到整个缓冲区。然而，一个完整的文件应该用一个 Java 缓冲区来表示，因为一个大文件会导致大量的内存被用来镜像它。

这就把我带到了本地磁盘存储的下一步:[第 6 部分——内存映射](https://dev.to/quoll/mapping-the-way-3fn)。