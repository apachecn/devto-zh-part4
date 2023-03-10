# JS 中的单链表

> 原文：<https://dev.to/rygelxvi/singly-linked-list-in-js-1b62>

# 数据结构-单链表

单链表由许多节点组成，每个节点包含数据和指向下一个节点的指针。列表中的最后一个节点指向 null，这就是为什么您知道您到达了末尾。列表的前面是头节点。

下面是一些显示 LinkedList 和 Node 类初始化的 Javascript 代码。

```
 class LinkedList {

  constructor() {
    this.head = null
    this.size = 0
  }

///Other methods here

end

class Node {

  constructor(data, next) {
    this.data = data
    this.next = next
  }

} 
```

###### 头节点- >节点- >节点- >节点- >...更多节点...- >空

这是一个非常简单的结构。它不要求节点在内存中彼此相邻，因此很容易在链表的头部添加和删除节点。它可以在常数或 O(1)时间内完成，这是可能的最佳时间复杂度。

Javascript -添加到头中并从头中移除

```
 addToHead(data) {
    let node
    if (!!data) {
      node = new Node(data, this.head)
      this.head = node
      this.size += 1
    } else {
      return null
    }
}

  removeFromHead() {
    if (this.size != 0) {
      this.head = this.head.next
      this.size -= 1
    }
} 
```

这种结构的一个缺点是，在列表中搜索或添加/删除到末尾(而不是头)的开销更大。每个节点都必须被迭代，直到我们意外地遇到包含我们正在寻找的数据的节点，或者直到我们到达末尾，或者直到下一个指针指向 null。

```
// finds the Node that contains the data. If it doesn't exist returns null
  find(data) {
    let current = this.head
      while (current != null) {
        if (current.data === data) {
          return current
        } else {
          current = current.next
        }
      }
    return null
}

  addToTail(data) {
    let node
    let size = this.size
    let current = this.head

    if (!!data) {

// when list is empty just add to head
      if (current === null) {
        this.addToHead(data)
      } else {

// otherwise find the end of the list and add a Node
        while (size > 0) {

// if size === 1 than it is the last node. Add new Node to end.
          if (size === 1) {
            node = new Node(data, null)
            current.next = node
            this.size += 1
          }
// size is > 1 than decrement size and check the next Node
          current = current.next
          size -= 1
        }
      }
    } else {
      return null
    }
} 
```

因为我们每次都必须遍历列表，这导致 O(n)复杂度，这不是最佳的。

从列表中间插入和删除也同样困难，因为我们必须创建新节点并将其指向“下一个”节点*。在*之前，我们将当前节点置于新节点的旁边。否则我们没有指针，列表就丢失了。

```
 insertAtIndex(data, index) {
    let node
    let current
    let size = this.size

// check if !!data and if index is within bounds
    if (!!data && index >= size - 1 && index >= 0) {
      current = this.head

// base case if list is empty or if adding to head
      if (current == null && index === 0 || index === size-1) {
        this.addToHead(data)
        return true
      }
// base case when adding to tail
      if (index === 1) {
        this.addToTail(data)
        return true
      }

// find spot before index and insert to next Node
      while (size > index) {
        if (size - 1 === index) {
          node = new Node(data, current.next)
          current.next = node
          this.size += 1
          return true
        }
        current = current.next
        size -= 1
      }

    }
    return false
} 
```

这就是为什么这些结构经常被用于堆栈和队列。它为操作系统提供了灵活性，因为它不需要像数组那样的内存块，这在我看来是堆栈和队列的最佳选择。对于堆栈，我们总是操作最后添加的内容，因为它是后进先出(LIFO)结构，而队列是先进先出(FIFO)数据结构。我们可以改天再谈这些。

这里是我的 [JS 链表代码](https://github.com/Rygel-XVI/my-linked-list-js)的链接