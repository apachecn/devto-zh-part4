# 规划道路

> 原文：<https://dev.to/quoll/mapping-the-way-3fn>

这是我尝试整理数据库设计系列文章的第 6 部分。第五部分是[这里的](https://dev.to/quoll/storage-8gp)，而[开始是这里的](https://dev.to/quoll/what-s-with-data-structures-2fk7)。

# 缓冲极限

Java 的大小有一个基本的 2GB 限制。发生这种情况是因为当 Java 及其运行的硬件和操作系统大多是 32 位系统时引入了缓冲区。`int`类型是一个 32 位值，用于缓冲区大小和缓冲区内的地址。2GB 的缓冲区占用了 32 位系统上 Java 进程的整个可寻址内存，所以即使计算机的 RAM 比这个多，它也不能供单个进程使用。(32 位可以寻址 4GB，但是`int`类型是有符号的，正数只能寻址到 2GB。这个限制是有益的，因为操作系统需要映射到地址空间，加载的库、Java 运行时、堆栈和任何现有的堆也是如此。

当 64 位系统在 21 世纪中期开始成为标准时，Java 进程能够使用大得多的缓冲区，但是`buffer` API 已经固定在 32 位寻址。也就是说，现在可以构建 2GB 的缓冲区，而不用担心耗尽一个进程的所有可寻址空间。事实上，如果您有 RAM 和/或交换空间，您可以在您的进程中创建一个 2GB 的缓冲区数组。尽管能够分配超过 2GB 的内存，但除非您需要处理内存中的大量信息(例如高分辨率图像或视频),否则您不会希望一次分配这么多。

然而，这为内存映射文件开辟了新的可能性。

# 内存映射文件

早在 2001 年，JDK 1.4 测试版就发布了“新 I/O”包。这为 Java 引入了一个以前只存在于主机操作系统中的特性。

我将对读/写操作和内存映射文件做一个简单的描述。我是在午夜后写的这篇文章，所以如果我有什么不对的地方，请见谅。几十年来，有些事情发生了变化，但基本原则仍然存在。

在像 C 这样的二进制系统中，正常的文件 I/O 是通过在一块内存上调用 C 库函数`write()`来完成的。C 库发出一个*系统调用*将内存写到磁盘*。实现写操作的内核代码将这个缓冲区的内容复制到属于操作系统的内存(内核内存)中，并排队进入磁盘控制器。然后内核代码返回状态(希望是“成功”值)。

###### *多年前，我一直想知道做一个*系统调用*到底是什么。它包括将调用的适当参数放入 CPU 寄存器(有时是数字，但通常是数据的内存地址)，在其中一个寄存器中设置适当调用的编号，并保存当前指令指针和堆栈指针。然后，它发出一条指令，告诉 CPU 切换到特权模式并执行内核代码。从前，这是通过发出软件*中断*来完成的。然后在 i586 中使用了`sysenter/sysexit`操作。此后不久，`syscall`被引入以取代`sysenter`。但都是让 CPU 改变模式，在固定位置执行代码。一旦这种新模式启动，CPU 将只运行属于操作系统内核的代码，它可以直接访问硬件。完成后，它将重新加载保存的地址，并从发出呼叫的地方继续。需要注意的是，像这样的模式切换是有代价的。自从我上次在这个级别工作以来，事情已经发生了变化，所以我很高兴我查阅了它。我知道这些原则，但有趣的是看到近年来发生的变化！

执行`read`类似。内存被留出，地址在调用 C 库中的`read()`函数时提供。库发出系统调用，告诉磁盘控制器要获取什么数据。这返回到内核内存中，然后内核必须将它复制到调用程序提供的缓冲区中。

幸运的是，开发人员通常不需要考虑这些细节。但是这里发生了一些相关的事情。当读写时，内核必须在进程的地址空间和内核的地址空间之间复制内存。这需要时间。

当内核需要执行读或写操作时，它通常会使用与普通读/写不同的机制。相反，内核会将文件映射到内存中。这是通过与交换空间做同样事情的代码来完成的。当一个文件被映射时，一个地址范围被用来表示该文件的内容。从给定的地址读取，实际上是从该位置的文件中读取。写到一个地址，你就写到文件了。

当从一个地址读取时，CPU 将检查该地址是否与当前活动的内存相匹配。如果是，那么它将直接从适当的内存中读取(有硬件支持来访问相关的内存)。如果没有，那么内核将找到文件的适当部分，找到空闲的内存，或者腾出一些没有使用的内存，然后将文件的一部分加载到相关的内存地址。这些部分被称为*页面*，在大多数桌面上是 4kb。因此，如果数据已经在内存中，您可以立即获得它，而不需要像正常的`read`操作中那样进行复制。如果它不在内存中，你必须像往常一样从磁盘中读取，但是你有它被读入的地址，同样，没有复制。

写法也差不多。当您写入被映射的内存时，该页面被标记为*脏*，并被安排写入磁盘。如果您再次写入它，那么它仍然是*脏的*并且仍然被调度，您只是修改了一些内存。它最终将被写入磁盘:如果经过了足够的时间；如果应用程序明确请求一个操作来强制它到磁盘；或者操作系统是否需要空间将另一个页面读入内存。

总之，读/写操作:

*   大多数情况下是同步完成的(尽管写操作可能会被操作系统延迟，而读操作可能来自缓存)。
*   总是需要系统调用。
*   通常涉及磁盘访问。
*   总是需要内存副本。

访问映射文件中的数据:

*   不需要系统调用。
*   不需要内存副本。
*   经常从缓存数据中读取。
*   总是写入缓存。

...其中“缓存”是要读取或写入的页面。

内存映射文件的主要问题是:

*   它们占用地址空间(这是 32 位系统的一个问题)。
*   他们固定文件的大小。

如果你想写很多东西，最后一个是很棒的。但是，文件是由*区域*映射的，所以映射文件中你需要的部分并继续追加到文件中是没有问题的。然后，您可以在需要时映射一些新区域。

# 映射字节缓冲区

Java 的 NIO 使用缓冲区来表示内存映射文件。这对我们来说很方便，因为我们已经在缓冲区上分层了我们的数据结构！

那么这看起来会怎样呢？代替显式的`read`和`write`操作，我们将缓冲区设置为构造函数中的文件映射。为了提供一点灵活性，我们可以设置一些新规则:

*   如果文件比请求的数量`Elements`大，还是要映射整个文件(这样我们可以得到比我们请求的更多的元素)。
*   如果文件小于`Elements`的请求号，则将文件扩展到足够大。下一个可用的元素将是超出文件停止位置的元素。
*   关闭文件时，将其截断为所使用的内容。这就是`nextAvailable`位置所在的地方。

```
public BufferList(String filename, int length) throws IOException {
  long fileLength = ELEMENT_SIZE * length;
  File ioFile = new File(filename);
  boolean fileExists = ioFile.exists();
  file = new RandomAccessFile(filename, "rw");
  if (fileExists) {
    nextAvailable = (int)(file.length() / ELEMENT_SIZE);
    // check if the file is longer than what was requested, or shorter
    if (file.length() > fileLength) {
      fileLength = file.length();
    } else if (fileLength > file.length()) {
      file.setLength(fileLength);
    }
  } else {
    // brand new file
    nextAvailable = 0;
  }
  fileChannel = file.getChannel();
  buffer = fileChannel.map(FileChannel.MapMode.READ_WRITE, 0, fileLength);
}

public void close() throws IOException {
  fileChannel.truncate(nextAvailable * ELEMENT_SIZE);
  fileChannel.force(true);
  file.close();
} 
```

Enter fullscreen mode Exit fullscreen mode

否则，`read`和`write`操作被删除，其他都不变！

# 使用新的缓冲列表

我们如何使用与上一个例子相同的元素创建 BufferList？几乎是同样的方式:

```
public static void main(String[] args) throws IOException {
  BufferList bufferList = new BufferList("buffer.bin", 20);
  BufferList.Element list1, list2, list3;
  list1 = bufferList.addToHead(34);
  // ... list construction goes here ...
  list3 = bufferList.addToHead(list3, 3);
  System.out.println(list1);
  System.out.println(list2);
  System.out.println(list3);
  writeLists(list1, list2, list3);
  bufferList.close();
} 
```

Enter fullscreen mode Exit fullscreen mode

唯一的变化是对构造函数的调用，现在我们正在关闭`BufferList`。

它是如何运行的？与上一版本相同:

```
$ rm -f buffer.bin
$ java mapped.MultiExample
1, 1, 2, 3, 5, 8, 13, 21, 34
5, 6, 7, 8, 13, 21, 34
3, 1, 4, 1, 5, 9, 2, 6 
```

Enter fullscreen mode Exit fullscreen mode

在没有删除`buffer.bin`文件之前，不要运行它。我们告诉它不要扩展，我们把它限制在 20 个元素内，所以如果它得到一个已经满了的文件并试图分配新的`Elements`，它会抱怨。

再次读取数据也是一样:

```
public static void main(String[] args) throws IOException {
  BufferList bufferList = new BufferList("buffer.bin", 20);
  for (BufferList.Element list: readLists(bufferList)) {
    System.out.println(list);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

运行它会显示预期的数据:

```
$ java mapped.MultiExample2
1, 1, 2, 3, 5, 8, 13, 21, 34
5, 6, 7, 8, 13, 21, 34
3, 1, 4, 1, 5, 9, 2, 6 
```

Enter fullscreen mode Exit fullscreen mode

由于格式相同，我们可以将之前缓冲版本创建的文件进行内存映射，并成功打印:

```
$ java buffer.MultiExample    # Creates file using write()
1, 1, 2, 3, 5, 8, 13, 21, 34
5, 6, 7, 8, 13, 21, 34
3, 1, 4, 1, 5, 9, 2, 6
$ java mapped.MultiExample2   # Reads file using memory mapping
1, 1, 2, 3, 5, 8, 13, 21, 34
5, 6, 7, 8, 13, 21, 34
3, 1, 4, 1, 5, 9, 2, 6 
```

Enter fullscreen mode Exit fullscreen mode

完整的源代码可以在 Github 上找到[。](https://gist.github.com/quoll/a57a3c85ce5f5b45dd658a6b04b6f550)

# 告诫

虽然这种技术适用于最大 2GB 的文件，但这是一个缓冲区所能处理的最大容量。与*分配的*缓冲区不同，内存映射文件可以使用非常大的缓冲区，因为操作系统只为你当前正在使用的文件部分分配内存。因此，大于 2GB 的文件可以使用数组`MappedByteBuffers`进行映射。获取元素是由构造函数管理的，它为元素索引找到正确的映射，并从中获取一部分。实现的其余部分保持不变。

# 重述

我们采用了现有的缓冲区支持的数据结构，并将缓冲区内存映射到文件。这是更快的，并保持文件跟踪实时数据，因为它在程序中的变化。最终的程序几乎是相同的，但是根本没有对缓冲区使用读/写操作。

(尽管如此，我们仍然对元数据使用读/写。如果我们需要，元数据也可以被映射)。

接下来，我们将看看另一种不同的数据结构:二叉树。