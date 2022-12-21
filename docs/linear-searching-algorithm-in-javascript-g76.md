# Javascript 中的线性搜索算法

> 原文：<https://dev.to/generally23/linear-searching-algorithm-in-javascript-g76>

# 朴素线性搜索法

## 首先什么是线性搜索？

线性搜索是在一个列表或一组值中查找某个值的过程(例如，以线性方式在 javascript 数组中查找值)，这意味着按顺序查找。因为 javascript 数组是有索引的，所以我们可以将我们要查找的内容与数组中的当前值进行比较，如果找到，则返回 true，否则返回 false。请注意，有不同的搜索方式，但在这篇文章中，我们将只关注线性搜索

### 实现

```
// first, let's create the function used to do the search 
```

```
// as you can see in this post I am using ES6(new version of Javascript) 
```

```
const linearSearch = (arr, query) => {
    let i;
    for (i = 0; i < arr.length; i++) {
      let currentValue = arr[i];
      if (query === currentValue) {
         return i;
      } 
    }
    return -1; 
} 
```

```
// Hell no!!! what is all of this? don't worry it's simple 
```

```
 javascript
/*
1\. First we created a function called linearSearch.
2\. We passed two arguments to our function.
3\. The first argument is the list we are searching in.
4\. The second argument is the value to look for inside the list.
5\. We declared a variable called i.
6\. Now we want to iterate or (loop) over the list.
7\. We are comparing the currentValue on the list to the query argument
8\. if they are equal we are returning the index we found the value we were looking for.
9\. if not we are returning -1 which means we did not found it.
/*

That's it Enjoy!!! 
```