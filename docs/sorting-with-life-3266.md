# 用生命排序

> 原文：<https://dev.to/learn_sumit/sorting-with-life-3266>

我们经常会遇到这样的情况，我们开始责备某人，却没有考虑他为什么不能取悦我们。为什么不呢？因为这很明显地指向那个手指。在那一刻，那个人对我们来说似乎很讨厌。后来，如果有人足够幸运，那么这一点被带回去思考。事物以数组的形式排序。我感觉这和冒泡排序有相似之处。更大更好的东西会在最后送来，重点是小元素。在冒泡排序中，元素数组分为两部分:未排序的和排序的。排序后的部分从最后开始构建。我们每次都从一开始就争论不休，并在最后放弃较大的(读起来更好的)部分。

```
begin BubbleSort(list)
   for all elements of list
      if list[i] > list[i+1]
         swap(list[i], list[i+1])
      end if
   end for
   return list
end BubbleSort 
```

因此，这个问题的时间复杂性大大增加。好事情是，它会被排序，但你将不得不付出时间的代价。

让我们退一步想想，是否可以应用我们最喜欢的快速排序算法。

```
Quicksort(A,p,r) {
    if (p < r) {
       q <- Partition(A,p,r)
       Quicksort(A,p,q)
       Quicksort(A,q+1,r)
    }
}

Partition(A,p,r)
    x <- A[p]
    i <- p-1
    j <- r+1
    while (True) {
        repeat
            j <- j-1
        until (A[j] <= x)
        repeat
            i <- i+1
        until (A[i] >= x)
        if (i A[j]
        else 
            return(j)
    }
} 
```

现在的问题是，为什么我们不能在此之前采取这一步骤，而不是让事情变得更糟和拖延。是的，有时候争论会导致更好的理解。但是有一个词“有时”。

当时采取的谨慎措施能避免这些灾难吗？在愤怒达到顶峰的短暂时间里，你有可能站在别人的立场上思考。好吧，我们亲爱的枢纽元素可以在这里服务的目的。快速排序算法智能地选择更多的枢纽元素更少的时间进行排序。
我们能否明智地选择支点并划分问题？
这里存在一个模糊性，这就是为什么我们有最好的情况，一般情况和最坏的情况。

让我们在这里总结一下，那个完美地融入其中的人赢得了这个时刻，并活在其中。因为支点选得很巧妙。这就是为什么快速排序比冒泡排序执行得更好，因为它从一开始就启动了良好的流程。不用等多久就开始了。

## 参考文献

1.  [快速排序](https://runestone.academy/runestone/books/published/pythonds/SortSearch/TheQuickSort.html)