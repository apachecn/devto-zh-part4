# 在一个星球大小的 JavaScript 语句中从字符串中删除重复项

> 原文：<https://dev.to/sergeis/removing-duplicates-from-a-string-in-one-planet-size-javascript-statement-3gp2>

JavaScript 数组函数是从函数式编程中借用的一个特性，相对容易理解。我以前写过在一个星球大小的 JavaScript 语句中做 [FizzBuzz，这篇文章是关于在一个语句中解决另一个基本的编码面试问题:从一个字符串中删除重复项。](https://dev.to/sergeis/fizzbuzz-in-one-planet-size-javascript-statement-4oef)

消除重复的一个标准方法是[在我们遍历字符串时创建一组可见的字符，只保留那些没有被看到的字符](http://www.ardendertat.com/2012/01/06/programming-interview-questions-25-remove-duplicate-characters-in-string/)。但这不是唯一的方法。注意，我们可以很容易地对一个数组进行排序，所以所有重复的元素都聚集在一起，然后将重复的序列减少到第一个元素。注意单词 **reduce** ，因为这是我们将要使用的数组函数！

使用 sort()来完成我们所需要的似乎是昂贵和浪费的，但是请记住，比较排序是 O(n log(n))，与 O(n)相差不远，这是字符串操作可能的最低顺序。

所以，我们打算这么做:

1.  对给定的字符串进行排序，以便所有重复的字符串都在一起
2.  用重复的字符替换重复字符的所有子字符串
3.  恢复原来的秩序。

我们可以在一条 JavaScript 语句中完成所有这些工作，尽管这会有点长。下面是一个带注释的示例，您可以将其复制并粘贴到调试控制台:

```
'4366447654434567876'.split('')             // string to array
    .map((e,i)=>({val:e,pos:i}))            // remember the original position of each character
    .sort((a,b)=>a.val.localeCompare(b.val))// sort by value
    .reduce((acc,e)=>acc.length == 0 
        || acc[acc.length-1].val!=e.val?    // keep if not a duplicate 
        acc.concat([e]):acc, 
        [])                                 // empty array as a seed        
    .sort((a,b)=>a.pos-b.pos)               // restore the original order
    .map(e=>e.val)                          // select original values
    .join('')                               // back to string!
; 
```

而答案是，不出所料

```
"436758" 
```

上面的大部分应该是不言自明的，但是有必要解释一下我们在 reduce()函数中做了什么:

1.  从空的累加器数组[]开始
2.  无条件插入第一个元素(因此检查 acc.length == 0)
3.  对于每个后续元素，如果它不是重复的，则将其添加到累加器数组中
4.  否则，保持累加器不变。

现在，这肯定比使用中间可见字符集更浪费，所以不要在编码面试时把它作为你的第一选择！但是很高兴知道我们可以在一行(很长的一行)中完成所有这些。或者，如果你是一个对一遍又一遍地问同样的问题感到厌烦的面试官，你可能想通过挑战可怜的受访者用一行回答这样的问题来混淆视听。

又一张纸条。作为一行程序来执行“set of seen”算法并不难，您能做到吗？