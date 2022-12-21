# hacker rank:Python 中的循环数组旋转

> 原文：<https://dev.to/therealdarkmage/hackerrank-circular-array-rotation-in-python-52e2>

# 圆形阵列旋转

所以我在 HackerRank 上，**剧透警报**

[https://www . hacker rank . com/challenges/circular-array-rotation/problem？utm _ campaign =挑战-推荐&UTM _ medium = email&UTM _ source = 24 小时-活动](https://www.hackerrank.com/challenges/circular-array-rotation/problem?utm_campaign=challenge-recommendation&utm_medium=email&utm_source=24-hour-campaign)

这花了我大约五分钟。

这实际上并不是关于我如何解决这个问题的，尽管我会简单地谈一谈。

[https://stack overflow . com/questions/2150108/efficient-way-to-shift-a-list-in-python](https://stackoverflow.com/questions/2150108/efficient-way-to-shift-a-list-in-python)

我问自己/Google“python 数组右移”，被提醒有`collections`和`deque`存在。我以前用过。Python 有这么多我总是忘记的很酷的工具。你不能因为某人不记得每一件事而惩罚他。

```
def circularArrayRotation(a, k, queries):
    from collections import deque 
    items = deque(a) 
    items.rotate(k)
    ret_list = []
    for q in queries:
        #print(items[q])
        ret_list.append(items[q])
    return ret_list 
```

Enter fullscreen mode Exit fullscreen mode

现在，你会注意到一些有趣的事情，我已经注释掉了循环中的 print 语句。

当我第一次编写函数时，它是这样的:

```
def circularArrayRotation(a, k, queries):
    from collections import deque 
    items = deque(a) 
    items.rotate(k)
    ret_list = []
    for q in queries:
        print(items[q]) 
```

Enter fullscreen mode Exit fullscreen mode

这正是问题描述所要求的。

> 对于每个查询，在新的一行上打印旋转数组索引处的元素值

我以为我做到了，但是后来 HackerRank 编译器/测试器出错了:

```
Compiler Message
Runtime Error
Error (stderr)
Traceback (most recent call last):
  File "Solution.py", line 42, in <module>
    fptr.write('\n'.join(map(str, result)))
TypeError: 'NoneType' object is not iterable
Input (stdin)
3 2 3
1 2 3
0
1
2 
```

Enter fullscreen mode Exit fullscreen mode

所以，我说“嗯……”并检查主要:

```
if __name__ == '__main__':
    fptr = open(os.environ['OUTPUT_PATH'], 'w')

    nkq = input().split()

    n = int(nkq[0])

    k = int(nkq[1])

    q = int(nkq[2])

    a = list(map(int, input().rstrip().split()))

    queries = []

    for _ in range(q):
        queries_item = int(input())
        queries.append(queries_item)

    result = circularArrayRotation(a, k, queries)

    fptr.write('\n'.join(map(str, result)))
    fptr.write('\n')

    fptr.close() 
```

Enter fullscreen mode Exit fullscreen mode

重要的部分是这个:

```
result = circularArrayRotation(a, k, queries) 
```

Enter fullscreen mode Exit fullscreen mode

似乎他们想要一个*返回值*！啊哈！然后我明白了我做错了什么！

看，调试很容易，:D

因此，我注释掉了 print 语句，并构造了一个给定参数要返回的项目列表，然后！100%通过测试。

几天前的晚上，我在 HackerRank 上的排名从 90 万上升到 60 万。刚刚，我搬到了 544328。

老实说，这整个讨论提出了一件事，如果在我之前没有人这样做，我将在不久的将来愤怒/写博客，但它主要是关于旧学校和新学校发展风格的哲学差异。

很久以前，我们没有超高速的互联网来查找东西，开发人员必须非常高效地了解一切，或者能够参考实体书上的东西。

如果说我从公立学校学到了什么，那就是如何使用书的索引，找到问题的答案，或者问题的解决方案。

这与你自己设计`deque`和`rotate`的效用是不同的。但是，HackerRank 并没有让我从头开始设计它。他们只是要求你解决问题。

会查东西算出轨吗？如果你依赖现成的工具，你真的是一个程序员吗？

我认为:一旦你有能力自己制作工具，你就可以使用预制工具。我经常遇到的问题是“使用什么工具？”，但到目前为止，Python 一直是它。

从技术上来说，谷歌也是。

* * *

# [如果你需要一名计算机科学导师、代码审核员，或者仅仅是一个可以和程序配对的人，请联系我](https://codementor.io/mikebell66)