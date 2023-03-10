# R 中的运算符重载

> 原文：<https://dev.to/awwsmm/operator-overloading-in-r-19fc>

*照片由[阿德利·瓦希德](https://unsplash.com/@adliwahid?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/s/photos/burden?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

虽然[操作符重载](https://cafe.elharo.com/programming/operator-overloading-considered-harmful/)通常是一个 [double plus ungood](https://en.wikipedia.org/wiki/Newspeak) 的想法，但是知道一门语言能够做什么是很好的，万一你发现自己在将来需要一些东西。

我最近发现 r 中的运算符重载。

大多数 R 程序员都知道如何定义函数:

```
>  myfun  <-  function(x,  y)  x  +  y  >  myfun(3,  4)  [1]  12 
```

Enter fullscreen mode Exit fullscreen mode

但是你也可以定义一个名字中带有特殊符号的函数，只要你总是用反斜杠将函数名括起来:

```
>  `g^h`  <-  function(x,  y)  x  +  y  >  `g^h`(3,  4)  [1]  12 
```

Enter fullscreen mode Exit fullscreen mode

r 还提供了[创建*中缀*操作符](http://adv-r.had.co.nz/Functions.html)的能力(出现在和`+`两个参数之间的操作符*)。你需要做的就是上面所说的，但是也要用百分号将函数名括起来:* 

```
>  `%ytho%`  <-  function(x,  y)  x  +  y  >  3  %ytho%  4  [1]  7 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面看到的，当使用中缀操作符时，你可以去掉反勾号。这正是 R 管道操作符的定义:

```
>  "%>%"  <-  function(x,f)  do.call(f,list(x))  >  sqrt(pi)  [1]  1.772454  >  pi  %>%  sqrt  [1]  1.772454 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可能会想:“嘿，也许我可以将`|`重新定义为 R 中的管道操作符，就像在大多数 shells 中一样”。你是对的:

```
>  "|"  <-  function(x,f)  do.call(f,list(x))  >  pi  |  sqrt  [1]  1.772454 
```

Enter fullscreen mode Exit fullscreen mode

但是为什么我们不需要在这个函数名周围加百分号呢？这是因为你实际上并没有定义一个新的函数，你只是在重载一个现有的*。在这种情况下，它是矢量 OR 运算符。*

 *你也可以做一些傻事，比如:

```
>  "+"  <-  function(x,  y)  x  *  y  >  3  +  4  [1]  12  >  4  +  5  [1]  20 
```

Enter fullscreen mode Exit fullscreen mode

...尽管我强烈警告不要那样做。(很混乱，从来没有必要。)*