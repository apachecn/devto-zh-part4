# 拯救树木

> 原文：<https://dev.to/quoll/save-the-trees-4n4p>

第 8 部分，用[第 7 部分回到这里](https://dev.to/quoll/shaking-the-tree-2oi2)，或者回到[开头](https://dev.to/quoll/what-s-with-data-structures-2fk7)。

# 重访映射文件

现在我们已经看到了树节点和链表元素的结构差异，我们需要更新 [BufferList](https://dev.to/quoll/mapping-the-way-3fn#buffer-list) 类来处理树而不是列表。

首先要考虑的是每个树节点在磁盘上的布局:

```
public static int NODE_SIZE = 3 * Integer.BYTES;
private static int VALUE_OFFSET = 0;
private static int LEFT_OFFSET = 1;
private static int RIGHT_OFFSET = 2; 
```

Enter fullscreen mode Exit fullscreen mode

注意节点的大小是以字节表示的，但是偏移量都是以整数偏移量给出的。如果没有数据的`IntBuffer`视图，所有的偏移量都是以字节为单位的，我们的代码必须使用这些偏移量，然后在将它们粘合到一个`Integer`值之前读取适当数量的字节。幸运的是，`IntBuffer`类已经为我们做了所有这些。

对于链表文件，我们需要为文件存储一些元数据，并将其放在一个单独的文件中。这次我们可以做点不同的事情。我们想要存储的元数据是可以分配的下一个节点的偏移量，以及形成树的根的节点的偏移量。这仅仅是 2 个整数，这使得它可以放入通常分配给`Node`的空间中。如果我们在偏移量 0 处保留第一个`Node`的位置，那么我们可以用它来存储这个元数据。在这种情况下，这意味着我们不需要第二个文件，这很方便，尽管对于更复杂的结构，我们可能仍然需要那个元文件。

使用`0`作为无效地址反映了 CPU 和操作系统通常的工作方式。`0`地址表示内存的无效部分，这意味着对它的每个引用都表示无效或未初始化的内存。通常情况下，最初的几个物理地址都表示一个错误，尽管 CPU 会将它们用于自己的目的，例如保存在旧系统上发生硬件中断时代码运行的地址。因此，在`0`偏移量处存储我们的元数据有一个我喜欢的类比。

这种方法的第二个好处是`NULL`参考现在将是`0`而不是`-1`。这很有用，因为 Java 确保所有新的缓冲区都被初始化为`0`，这意味着当一个新的`Node`被分配时，子缓冲区已经被设置为`NULL`。(C 和 C++不会这样做，除非你使用`calloc`来分配内存)。

现在我们知道第一个`Node`空间将保存元数据，我们有一组扩展的常量可以使用:

```
public static int NODE_SIZE = 3 * Integer.BYTES;
public static int META_SIZE = 2 * Integer.BYTES;
public static int NULL = 0;

// META offsets
private static int NEXT_AVAILABLE_OFFSET = 0;
private static int ROOT_OFFSET = 1;

// Node offsets
private static int VALUE_OFFSET = 0;
private static int LEFT_OFFSET = 1;
private static int RIGHT_OFFSET = 2; 
```

Enter fullscreen mode Exit fullscreen mode

## 构造

有了常数，就可以声明树所需的数据了。这包括文件以及通道，以便可以将它映射到缓冲区中。我们希望`ByteBuffer`代表文件中的所有数据，因为这是将`Node`叠加到文件上的基础。最后，我们需要一个缓冲引用来访问元数据和一个表示树根的节点。

```
private final RandomAccessFile file;                                                                                    
private final FileChannel fileChannel;
private ByteBuffer buffer;
private IntBuffer metaBuffer;
private Node root; 
```

Enter fullscreen mode Exit fullscreen mode

构造函数将做类似于它为`BufferList`类所做的工作。文件被打开，如果不够长，它会被扩展，如果太长，它会被截断。

```
long fileLength = NODE_SIZE * length;
File ioFile = new File(filename);
boolean exists = ioFile.exists();
file = new RandomAccessFile(filename, "rw");
if (exists && file.length() > fileLength) {
  fileLength = file.length();
} else if (fileLength > file.length()) {
  file.setLength(fileLength);
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦文件达到预期大小，就可以对其进行映射。然后，映射缓冲区的前几个字节被捕获为元数据的缓冲区。

```
fileChannel = file.getChannel();
buffer = fileChannel.map(FileChannel.MapMode.READ_WRITE, 0, fileLength);
metaBuffer = buffer.limit(META_SIZE).position(0).slice().asIntBuffer(); 
```

Enter fullscreen mode Exit fullscreen mode

最后，可能会发生一些数据初始化。如果该文件以前不存在，那么第一个位置可用来放置一个节点，偏移量为`Node 1`。否则，将从元数据中读取该位置。同样，如果文件是新的，树的根将是`null`，否则一旦从元数据中读取了偏移量，就可以构造根节点:

```
if (!exists) {
  setNextAvailable(1);
}
int rootIndex = metaBuffer.get(ROOT_OFFSET);
root = rootIndex == NULL ? null : new Node(rootIndex); 
```

Enter fullscreen mode Exit fullscreen mode

`setNextAvailable`方法只是更新元数据

```
private void setNextAvailable(int next) {
  metaBuffer.put(NEXT_AVAILABLE_OFFSET, next);
} 
```

Enter fullscreen mode Exit fullscreen mode

在树上调用`close()`将会关闭这些文件，就像使用`BufferList`一样。我们不需要在这里包括它，但是它会在最后的文件中，我会在最后链接它。

## 生长一棵树

添加到树中遵循与前一篇文章中的 [Javascript 非常相似的结构。](https://dev.to/quoll/shaking-the-tree-2oi2#tree) 

```
public Node add(int value) {
  Node node = new Node(getAndIncNextAvailable(), value);
  if (root == null) {
    setRoot(node);
  } else {
    insertNode(root, node);
  }
  return root;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的主要区别是`Node`构造函数，它获取下一个空闲节点的节点 ID。注意，这是用一个获取 ID 并递增它的函数来检索的:

```
private int getAndIncNextAvailable() {
  int next = metaBuffer.get(NEXT_AVAILABLE_OFFSET);
  if ((next + 1) * NODE_SIZE > buffer.capacity()) {
    throw new RuntimeException("Out of capacity");
  }
  metaBuffer.put(NEXT_AVAILABLE_OFFSET, next + 1);
  return next;
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是从元数据中读取下一个可用的值，增加存储的值，并返回读取的值。

和`BufferList`类一样，如果整个缓冲区都满了，那么就会抛出一个异常。在生产中，我们希望通过扩展文件来处理这种情况，但这超出了我们现在所考虑的范围。在考虑生产代码时，也值得考虑多个线程可能同时调用该代码。如果是这种情况，那么这个函数应该用一个`synchronized`关键字来保护，以避免*下一个可用的*值出现可能的竞争情况。

对于 Javascript 树，方法`insertNode`将非常类似于 [`insertNode`方法。](https://dev.to/quoll/shaking-the-tree-2oi2#insert-node) 

```
private void insertNode(Node node, Node newNode) {
  int value = node.getValue();
  if (newNode.getValue() < value) {
    Node left = node.getLeft();
    if (left == null) {
      node.setLeft(newNode);
    } else {
      insertNode(left, newNode);
    }
  } else {
    Node right = node.getRight();
    if (right == null) {
      node.setRight(newNode);
    } else {
      insertNode(right, newNode);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

与原始 Javascript 的唯一区别在于,`this`标识符对于当前对象上的方法是可选的，并且因为*左*和*右*值是通过 *getter* 方法访问的，所以我们只调用该方法一次并保存返回值。

这些对象通常都需要 Getter 方法，因为它们包装了从缓冲区中检索对象的操作，而 Javascript 代码只能跟踪内存引用。我们在访问树根的方法中也看到了这一点，尽管因为这是在初始化时读取的元数据，所以 getter 不需要接触缓冲区:

```
private void setRoot(Node node) {
  root = node;
  metaBuffer.put(ROOT_OFFSET, node.getIndex());
}

public Node getRoot() {
  return root;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 打印

和前面一样，打印是一个递归过程，首先打印左边的分支，然后打印节点值，最后打印右边的分支，使用各种`if`或三元语句来确保添加了适当数量的逗号:

```
public String toString() {
  return root.toString();
}

public static String treeString(Node element) {
  Node left = element.getLeft();
  Node right = element.getRight();
  return (left == null ? "" : treeString(left)) +
         Integer.toString(element.getValue()) +
         (right == null ? "" : treeString(right));
} 
```

Enter fullscreen mode Exit fullscreen mode

## 节点

与`BufferList`元素一样，节点覆盖一个缓冲区。创建节点的第一步是切掉节点所包装的那部分缓冲区，并将其表示为整数值。如果节点仅使用缓冲区引用进行初始化，它可以用来读取已经存在的内容，如果它使用值进行初始化，它会将该值写入缓冲区。

```
private final IntBuffer intBuffer;
private final int index;

Node(int index) {
  this.index = index;
  int offset = index * NODE_SIZE;
  if (offset > buffer.limit()) {
    intBuffer = buffer.limit(offset + NODE_SIZE).position(offset).slice().asIntBuffer();
  } else {
    intBuffer = buffer.position(offset).limit(offset + NODE_SIZE).slice().asIntBuffer();
  }
}

Node(int index, int value) {
  this(index);
  setValue(value);
  setLeft(null);
  setRight(null);
} 
```

Enter fullscreen mode Exit fullscreen mode

`index`是只读的，但是其他值都可以通过*获取器*和*设置器*来访问，它们都包含缓冲区操作:

```
public int getValue() {
  return intBuffer.get(VALUE_OFFSET);
}

public Node getLeft() {
  int leftId = intBuffer.get(LEFT_OFFSET);
  return leftId == NULL ? null : new Node(leftId);
}

public Node getRight() {
  int rightId = intBuffer.get(RIGHT_OFFSET);
  return rightId == NULL ? null : new Node(rightId);
}

public Node setValue(int value) {
  intBuffer.put(VALUE_OFFSET, value);
  return this;
}

public Node setLeft(Node left) {
  intBuffer.put(LEFT_OFFSET, left == null ? NULL : left.getIndex());
  return this;
}

public Node setRight(Node right) {
  intBuffer.put(RIGHT_OFFSET, right == null ? NULL : right.getIndex());
  return this;
} 
```

Enter fullscreen mode Exit fullscreen mode

[`BufferTree`的完整代码在这里](https://gist.github.com/quoll/ec455ae7a66cf4d683768b41dc805628)。

## 使用树

创建这个树并添加π的数字很简单:

```
BufferTree bufferTree = new BufferTree("tree.bin", 25);
bufferTree.add(3);
bufferTree.add(1);
bufferTree.add(4);
bufferTree.add(1);
bufferTree.add(5);
bufferTree.add(9);
bufferTree.add(2);
bufferTree.add(6);
bufferTree.add(5);
bufferTree.add(3); 
```

Enter fullscreen mode Exit fullscreen mode

或者，可以将`add`操作链接起来:
`bufferTree.add(3).add(1).add(4).add(1).add(5).add(9).add(2).add(6).add(5).add(3);`
，但我没有这样做，因为大多数填充树的用例是当数据来自外部源时，而不是像这样硬编码。

打印树显示所有数据已经排序:

```
System.out.println(bufferTree); 
```

Enter fullscreen mode Exit fullscreen mode

```
$ java tree.TreeExample
1, 1, 2, 3, 3, 4, 5, 5, 6, 9 
```

Enter fullscreen mode Exit fullscreen mode

如果再次运行相同的程序，第二次加载数据，会发生什么情况？

```
$ java tree.TreeExample
1, 1, 1, 1, 2, 2, 3, 3, 3, 3, 4, 4, 5, 5, 5, 5, 6, 6, 9, 9 
```

Enter fullscreen mode Exit fullscreen mode

所有的原始数据都是按顺序重复的。

把数据读回来怎么样？那完全一样，没有加载步骤:

```
public class TreeExample2 {
  public static void main(String[] args) throws IOException {
    BufferTree bufferTree = new BufferTree("tree.bin", 25);
    System.out.println(bufferTree);
    bufferTree.close();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

导致:

```
$ java tree.TreeExample2
1, 1, 1, 1, 2, 2, 3, 3, 3, 3, 4, 4, 5, 5, 5, 5, 6, 6, 9, 9 
```

Enter fullscreen mode Exit fullscreen mode

这些示例程序也可以在`BufferTree` 的[要点的底部找到。](https://gist.github.com/quoll/ec455ae7a66cf4d683768b41dc805628)

## 再生长

这篇文章只是重复了上一篇文章中描述的树形结构，并结合了第 6 部分中描述的缓冲包装方法。这在磁盘上创建了一个二叉树，可以很容易地重新读取。

我们现在将缓冲区中的`0`偏移量称为无效地址，这允许我们使用缓冲区中的初始值(总是`0`)来指示空条目。这也允许我们将树的一些元数据转移到文件的头部，尽管随着元数据需求在以后的文章中扩展，这将具有有限的效用。

考虑到大多数数据库都使用某种形式的 B 树，这种对二叉树的讨论可能看起来是一个不寻常的焦点，但它最终会变得清晰。(我保证！)

但在此之前，我们应该看看[如何平衡这些树](https://dev.to/quoll/a-balanced-tree-27ik)。