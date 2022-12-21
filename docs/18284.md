# 代码 2018 第八天的来临:记忆策略

> 原文：<https://dev.to/steadbytes/aoc-2018-day-8-memory-maneuver-34jf>

> 本帖原载于[steadbytes.com](https://steadbytes.com/blog/advent-of-code-2018/08/)T2【完整解决方案见 [GitHub](https://github.com/SteadBytes/advent-of-code-2018/blob/7087a1e89ae7424b63f38c137d36d24b56a2965b/08/solution.py)

## 第一部分

我们的[谜题输入](https://github.com/SteadBytes/advent-of-code-2018/blob/1a02e64c19f485fd2730462a2e69cbe89c5ba136/08/input.txt)是圣诞老人雪橇上导航软件的许可文件(可惜不是[开源](https://opensource.org/licenses))。许可证由一列代表*序列化* [树](https://en.wikipedia.org/wiki/Tree_(data_structure))数据结构的数字组成。不幸的是，该软件的创造者要么没有听说过标准的[序列化格式](https://en.wikipedia.org/wiki/Serialization#Serialization_formats)，要么有一个[不是这里发明的综合症](https://en.wikipedia.org/wiki/Not_invented_here)(我很快会深入研究确切的格式)。我们被要求解析许可证文件，以找到许可证文件中存在的所有元数据条目的**总和。**

### 输入格式

通过根据树的层次结构“嵌套”树中每个节点的值，在许可证文件中对树进行序列化。我们被告知一个**节点**包括:

*   一个*标题*
*   零个或多个*子节点*
    *   嵌套在父节点中，紧跟在标题之后。
*   一个或多个*元数据项*
    *   紧跟在所有子节点之后。

*标题*由**恰好两个数字**组成，代表:

*   子节点
*   元数据条目

使用来自谜题的示例输入:

```
# license file
2 3 0 3 10 11 12 1 1 0 1 99 2 1 1 2

# tree
A -> [1, 1, 2]
|
|-B -> [10, 11, 12]
|
|-C -> [2]
  |
  |-D -> [99] 
```

Enter fullscreen mode Exit fullscreen mode

### 解析输入

因为我们只需要计算元数据条目的总和，所以没有必要将许可证文件反序列化为完整的树数据结构。下面概述的算法只需要许可证文件中的数字列表。这可以通过使用列表理解将文件中的每个数字转换成整数来完成:

```
with open("input.txt") as f:
    entries = [int(e) for e in f.read().split()] 
```

Enter fullscreen mode Exit fullscreen mode

对于示例输入，这会生成列表:

```
[2, 3, 0, 3, 10, 11, 12, 1, 1, 0, 1, 99, 2, 1, 1, 2] 
```

Enter fullscreen mode Exit fullscreen mode

### 算法

和涉及树的问题一样，递归算法是一种自然的方法。树中的**总元数据**由以下递归给出:

```
children(node) == []: sum(meta(node))
children(node) == [child_1, child_2,..., child_n]: sum(meta(child_1), meta(child_2), ..., meta(child_n)) 
```

Enter fullscreen mode Exit fullscreen mode

更具体地说，给定条目的列表(许可证文件中的编号):

1.  检索第一个节点的头
2.  如果标头指示没有子节点，则返回元数据值的总和
    *   即标题之后列表中所有剩余条目的总和
3.  否则，递归从条目列表中删除步骤 1 中的头

```
def part_1(entries):
    def recurse(entries):
        # retrieve header of current node
        n_child, n_meta = entries[:2]
        # remove header (move to next node)
        remaining = entries[2:]

        # total metadata from current node -> leaves
        meta_total = 0

        # node has children: recurse to find total metadata from children -> leaves
        # removes all entries between current node header and current node metadata
        for _ in range(n_child):
            child_total, remaining = recurse(remaining)
            meta_total += child_total

        # calculate total metadata for the current node
        current_node_meta = remaining[:n_meta]
        current_node_meta_total = sum(current_node_meta)

        meta_total += current_node_meta_total

        return meta_total, remaining[n_meta:]

    meta_total, _ = recurse(entries)
    return  meta_total 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，答案是 **48260** 。

## 第二部分

现在我们需要找到树中根节点的**值。节点的值被指定为:** 

```
children(node) == []: sum(meta(node))

children(node) == [child_1, child_2,..., child_n], meta(node) = [meta_1, meta_2, ..., meta_n]:
    sum(value(children(node)[meta_1]), (children(node)[meta_2]), ..., (children(node)[meta_n])) 
```

Enter fullscreen mode Exit fullscreen mode

*   如果节点有子节点，每个元数据值代表子节点的一个*索引*。然后，节点的值是由元数据表示的所有子节点的值的总和。
    *   跳过不存在的子节点索引
    *   跳过了 0 个索引

### 算法

1.  检索第一个节点的头
2.  如果标头指示没有子节点，则返回元数据值的总和
    *   即标题之后列表中所有剩余条目的总和
3.  否则，通过递归查找每个子节点的值，从条目列表中删除步骤 1 中的头
4.  合计节点元数据中引用的每个子节点的值

```
def part_2(entries):
    def recurse(entries):
        # retrieve header of current node
        n_child, n_meta = entries[:2]
        # remove header (move to next node)
        remaining = entries[2:]

        # values of all children of the current node -> leaves
        child_node_values = []

        # node has children: recurse to find values from children -> leaves
        # removes all entries between current node header and current node metadata
        for _ in range(n_child):
            child_value, remaining = recurse(remaining)
            child_node_values.append(child_value)

        # calculate total metadata for the current node
        current_node_meta = remaining[:n_meta]
        current_node_meta_total = sum(current_node_meta)

        if n_child == 0:
            current_node_value = current_node_meta_total
        else:
            # sum up all values of children nodes referenced be index in metadata
            current_node_value = sum(
                child_node_values[i - 1]  # account for 0 based index
                for i in current_node_meta
                # skip 0 index and non-existent indexes
                if i > 0 and i <= len(child_node_values)
            )

        return current_node_value, remaining[n_meta:]

    root_node_value, _ = recurse(entries)
    return root_node_value 
```

Enter fullscreen mode Exit fullscreen mode

对于我的谜题输入，答案是 **25981** 。

这个算法**与第一部分的**非常相似，事实上仍然需要为第一部分的
树中的每个节点总结元数据。因此，`part_1`和`part_2`的重复逻辑可以重构为一个函数:

```
def parse_entries(entries):
    """ Find sum of all metadata entries (part 1) and the root node value (part 2)
    from the list of entries in the license file.

    Returns:
        (int, int): sum of all metadata entries, root node value - see puzzle
            description
    """

    def recurse(entries):
        # retrieve header of current node
        n_child, n_meta = entries[:2]
        # remove header (move to next node)
        remaining = entries[2:]

        # total metadata from current node -> leaves
        meta_total = 0
        # values of all children of the current node -> leaves
        child_node_values = []

        # node has children: recurse to find values from children -> leaves
        # removes all entries between current node header and current node metadata
        for _ in range(n_child):
            child_total, child_value, remaining = recurse(remaining)
            meta_total += child_total
            child_node_values.append(child_value)

        current_node_meta = remaining[:n_meta]
        current_node_meta_total = sum(current_node_meta)

        meta_total += current_node_meta_total

        if n_child == 0:
            current_node_value = current_node_meta_total
        else:
            # sum up all values of children nodes referenced be index in metadata
            current_node_value = sum(
                child_node_values[i - 1]  # account for 0 based index
                for i in current_node_meta
                # skip 0 index and non-existent indexes
                if i > 0 and i <= len(child_node_values)
            )

        return meta_total, current_node_value, remaining[n_meta:]

    return recurse(entries)[:2]

def part_1(entries):
    return parse_entries(entries)[0]

def part_2(entries):
    return parse_entries(entries)[2] 
```

Enter fullscreen mode Exit fullscreen mode

### 资源

*   [树木](https://en.wikipedia.org/wiki/Tree_(data_structure))
*   [递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))
*   [OSS 许可证](https://opensource.org/licenses)
*   [这里没有发明综合症](https://en.wikipedia.org/wiki/Not_invented_here)