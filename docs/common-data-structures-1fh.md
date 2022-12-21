# 4 种常见的数据结构

> 原文：<https://dev.to/highcenburg/common-data-structures-1fh>

*最初发布在我的[博客](https://highcenburg.herokuapp.com)*

# 1。)数组

*   由索引或关键字标识的元素的集合

### 举例:

```
ex_arr = [1, 'string', 3, 'four']
print(ex_arr[3])

```

### 回答:

```
four

```

# 2。)链接列表

*   数据元素的集合，称为节点，包含对列表中下一个节点的引用，并保存应用程序需要的任何数据

### 例子:

### 节点类

```
class Node(object):
    def __init__(self, val):
        self.val = val
        self.next = None

    def get_data(self):
        return self.val

    def set_data(self, val):
        self.val = val

    def get_next(self):
        return self.next

    def set_next(self, next):
        self.next = next

```

### 链接列表类

```
class LinkedList(object):
    def __init__(self, head=None):
        self.head = head
        self.count = 0

    def get_count(self):
        return self.count

    def insert(self, data):
        new_node = Node(data)
        new_node.set_next(self.head)
        self.head = new_node
        self.count += 1

    def find(self, val):
        item = self.head
        while (item != None):
            if item.get_data() == val:
                return item
            else:
                item = item.get_next()
        return None

    def deleteAt(self, idx):
        if idx > self.count:
            return
        if self.head == None:
            return
        else:
            tempIdx = 0
            node = self.head
            while tempIdx < idx-1:
                node = node.get_next()
                tempIdx += 1
            node.set_next(node.get_next().get_next())
            self.count -= 1

    def dump_list(self):
        tempnode = self.head
        while (tempnode != None):
            print("Node: ", tempnode.get_data())
            tempnode = tempnode.get_next()

```

### 创建一个链表并插入一些条目

```
itemlist = LinkedList()
itemlist.insert(38)
itemlist.insert(49)
itemlist.insert(13)
itemlist.insert(15)

itemlist.dump_list()

```

### 练习列表

```
print("Item count: ", itemlist.get_count())
print("Finding item: ", itemlist.find(13))
print("Finding item: ", itemlist.find(78))

```

### 删除一项

```
itemlist.deleteAt(3)
print("Item count: ", itemlist.get_count())
print("Finding item: ", itemlist.find(38))
itemlist.dump_list()

```

### 回答:

```
Node:  15
Node:  13
Node:  49
Node:  38
Item count:  4
Finding item:  
Finding item:  None
Item count:  3
Finding item:  None
Node:  15
Node:  13
Node:  49

```

# 3。)堆栈和队列

*   Stacks 是支持推送和弹出操作的操作集合。最后推送的项目是第一个弹出的项目。

### 举例:

### 创建一个新的空栈

```

stack = []

```

### 将项目推到堆栈上

```
stack.append(1)
stack.append(2)
stack.append(3)
stack.append(4)

```

### 打印堆栈内容

```
print(stack)

```

### 从堆栈中弹出一个项目

```
x = stack.pop()
print(x)
print(stack)

```

### 回答:

```
[1, 2, 3, 4]
4
[1, 2, 3]

```

*   堆栈是支持推送和弹出操作的操作集合。最后推送的项目是第一个弹出的项目。

### 举例:

```
from collections import deque

```

### 创建一个新的空队列对象，它将作为一个队列

```
queue = deque()

```

### 向队列中添加一些项目

```
queue.append(1)
queue.append(2)
queue.append(3)
queue.append(4)

```

### 打印队列内容

```
print(queue)

```

### 从队列前弹出一个项目

```
x = queue.popleft()
print(x)
print(queue)

```

### 回答:

```
deque([1, 2, 3, 4])
1
deque([2, 3, 4])

```

# 4。)哈希表(字典)

*   将键映射到其关联值的数据结构

### 好处:

*   键到值映射是唯一的
*   哈希表非常快
*   对于小型数据集，数组通常更有效
*   哈希表不以可预测的方式对条目进行排序

### 举例:

#### 一次创建一个哈希表

```
items1 = dict(
        {
            "key1": 1, 
            "key2": 2, 
            "key3": "three"
        }
    )
print(items1)

```

### 逐步创建哈希表

```
items2 = {}
items2["key1"] = 1
items2["key2"] = 2
items2["key3"] = 3
print(items2)

```

### 替换一个项目

```
items2["key2"] = "two"
print(items2)

```

### 迭代字典中的键和值

```
for key, value in items2.items():
    print("key: ", key, " value: ", value)

```

### 回答:

```
{'key1': 1, 'key2': 2, 'key3': 'three'}
{'key1': 1, 'key2': 2, 'key3': 3}
{'key1': 1, 'key2': 'two', 'key3': 3}
key:  key1  value:  1
key:  key2  value:  two
key:  key3  value:  3

```

# 真实世界的例子:

### 过滤掉重复的项目

### 定义一组我们想要减少重复的项目

```
items = ["apple", "pear", "orange", "banana", "apple",
         "orange", "apple", "pear", "banana", "orange",
         "apple", "kiwi", "pear", "apple", "orange"]

```

### 创建一个哈希表来执行过滤

```
filter = dict()

```

### 循环遍历每一项并添加到哈希表中

```
for item in items:
    filter[item] = 0

```

### 从哈希表中的结果键创建一个集合

```
result = set(filter.keys())
print(result)

```

### 输出:

```
{
    'kiwi',
    'apple',
    'pear',
    'orange',
    'banana'
}

```

### 求最大值

### 声明要操作的值列表

```
items = [6, 20, 8, 19, 56, 23, 87, 41, 49, 53]

def find_max(items):
    # breaking condition: last item in list? return it
    if len(items) == 1:
        return items[0]

    # otherwise get the first item and call function
    # again to operate on the rest of the list
    op1 = items[0]
    print(op1)
    op2 = find_max(items[1:])
    print(op2)

    # perform the comparison when we're down to just two
    if op1 > op2:
        return op1
    else:
        return op2

```

### 测试功能

```
print(find_max(items))

```

### 输出:

```
6
20
8
19
56
23
87
41
49
53
53
53
87
87
87
87
87
87
87

```

### 清点物品

### 定义我们要计数的一组项目

```
items = ["apple", "pear", "orange", "banana", "apple",
         "orange", "apple", "pear", "banana", "orange",
         "apple", "kiwi", "pear", "apple", "orange"]

```

### 创建一个哈希表对象来保存项目和计数

```
counter = dict()

```

### 迭代每个项目，并增加每个项目的计数

```
for item in items:
    if item in counter.keys():
        counter[item] += 1
    else:
        counter[item] = 1

```

### 打印结果

```
print(counter)

```

### 输出:

```
{'apple': 5, 'pear': 3, 'orange': 4, 'banana': 2, 'kiwi': 1}

```