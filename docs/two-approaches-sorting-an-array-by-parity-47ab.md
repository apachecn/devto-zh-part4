# 两种方法:按奇偶校验对数组排序

> 原文：<https://dev.to/kcarrel/two-approaches-sorting-an-array-by-parity-47ab>

在我的采访经历中，我遇到了一些问题，这些问题为解决未来的问题提供了很好的参考框架。一个这样的问题是按奇偶校验排序数组的问题。

## 问题

*给定一个非负整数的数组 A，返回一个由 A 的所有偶数元素组成的数组，后跟 A 的所有奇数元素
你可以返回任何满足这个条件的答案数组。*

## 蛮力:

```
def sort_array_by_parity(a)
    i = 0
    j =  a.length - 1
    while i < j
        if a[i] % 2 > a[j] % 2
            temp = a[i]
            a[i] = a[j]
            a[j] = temp
        end 
        if a[i] % 2 == 0
            i += 1
        end
        if a[j] % 2 == 1
            j -= 1
        end
    end
  return a
end 
```

在我第一次尝试解决这个问题时，我决定尝试两个指针的方法，向数组的中间迭代。这个解决方案通过了测试，但是运行时间只有 56 毫秒，比 LeetCode 上的大多数 ruby 提交都要慢。所以让我们做得更好！

## 最终方案:

```
def sort_array_by_parity(a)
  # create an index to store the next even number you run into
  index = 0
  i = 0
  #iterate through
  while  i < a.length 
     # if you encounter an even number move it to a[index]
    if a[i] % 2 == 0
      temp = a[index]
      a[index] = a[i]
      index += 1
      a[i] = temp
    end
    i += 1
  end
  return a
end 
```

这个解决方案为我赢得了 40 毫秒的运行时间，并击败了所有其他 ruby 解决方案提交的 98.98%。

# 我的外卖

*   使用索引变量对于跟踪下一个实例的放置位置非常有用
*   临时变量可以帮助你在数组中切换变量

快乐解题！