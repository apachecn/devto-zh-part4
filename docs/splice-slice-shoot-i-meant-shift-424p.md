# 拼接！切！该死，我是说换档！

> 原文：<https://dev.to/laurieontech/splice-slice-shoot-i-meant-shift-424p>

在 JavaScript 领域有一个笑话，数组有太多的函数有着可怕的相似名字。你可能看过这条关于它的推文:

> ![Laurie profile image](img/ed82805e4048b1b025fcda51dccd1038.png)劳里[@ laurieontech](https://dev.to/laurieontech)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)*谷歌数组拼接*
> 该死，我的意思是拆分！*谷歌数组分裂*
> 该死，我是说切！
> 🤦🏻‍♀️2019 年 6 月 14 日下午 13:50[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1139530549159170049)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1139530549159170049)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1139530549159170049)

或者这个:

> ![unknown tweet media content](img/00d62af07cb15c7487018f4919a2a4a0.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/tweet_video/D-F-IKRXUAAxr4J.mp4" type="video/mp4"></video>![Erin 🦁 profile image](img/92bd7f90eaf19a66e7f149b7895564e0.png)埃琳🦁[@ erinfranmc](https://dev.to/erinfranmc)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)如果一开始不成功...2019 年 6 月 27 日下午 19:59[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1144334474269908994)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1144334474269908994)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1144334474269908994)

甚至是这个:

> ![Erika with a 'k' profile image](img/029fbf3b189898095e4e5e4a3d5c628c.png)Erika 带个‘k’[@ eripdev](https://dev.to/eripdev)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)拼接、切片、拆分今天都在脑子里一起跑。我知道我今晚要刷新什么 lol😩🤓2019 年 6 月 11 日下午 15:52[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1138473999208464386)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1138473999208464386)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1138473999208464386)

有了这些介绍，我们来看看它们是什么，以及它们之间的区别！

# 拼接

Splice 实际上可以做很多事情。这是因为它需要多达三个参数(这并不完全准确，但你马上就会明白为什么)。让我们从第一个开始。

### 单参数

第一个参数是索引。如果只传入那个参数，`splice`将删除数组中所列索引之后的所有元素。(在这种情况下，您可能会认为它是数组中从第一个元素开始剩余的一些元素)。

```
let content = ['post', 'tweet', 'video', 'talk']
let removed = content.splice(2)
// content is ['post', 'tweet']
// removed is ['video', 'talk'] 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，只剩下两个元素。从数组中的第一个元素(索引 0)开始，直到它到达指定的索引 2。

### 两个论点

第二个参数是要删除的元素数量。此计数从第一个参数中给出的索引开始。让我们看几个例子。

```
let content = ['post', 'tweet', 'video', 'talk']
let removed = content.splice(1, 2)
// content is ['post', 'talk']
// removed is ['tweet', 'video'] 
```

Enter fullscreen mode Exit fullscreen mode

你也可以使用负指数。

```
let content = ['post', 'tweet', 'video', 'talk']
let removed = content.splice(-1, 1)
// content is ['post', 'tweet', 'video']
// removed is ['talk'] 
```

Enter fullscreen mode Exit fullscreen mode

### 三+论据

最后一个参数是要添加的元素。这些元素将从第一个参数中指定的索引开始添加。让我们从一个例子开始，其中我们的第二个参数是`0`，所以我们没有删除任何东西。

```
let content = ['post', 'tweet', 'video', 'talk']
content.splice(1, 0, 'dev')
// content is ['post', 'dev', 'tweet', 'video', 'talk'] 
```

Enter fullscreen mode Exit fullscreen mode

您也可以一次添加多个元素。

```
let content = ['post', 'tweet', 'video', 'talk']
content.splice(1, 0, 'dev', 'ten mile')
// content is ['post', 'dev', 'ten mile', 'tweet', 'video', 'talk'] 
```

Enter fullscreen mode Exit fullscreen mode

最后，您可以同时添加和删除元素。

```
let content = ['post', 'tweet', 'video', 'talk']
let removed = content.splice(1, 2, 'dev', 'ten mile')
// content is ['post', 'dev', 'ten mile', 'talk']
// removed is ['tweet', 'video'] 
```

Enter fullscreen mode Exit fullscreen mode

正如你在这些例子中看到的，`splice()`修改了数组本身。

# 切片

Slice 生成数组的浅层副本。如果你需要了解什么是浅层拷贝，看看这篇文章。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 深层与浅层拷贝-示例

### 劳丽 7 月 24 日 194 分钟阅读

#javascript #webdev #learning #beginners](/laurieontech/deep-vs-shallow-copy-with-examples-cfb)

这个副本是原件的子集。与 splice 一样，slice 有多个参数，在本例中有两个，但都不是必需的。

### 没有争论

如果你在没有任何参数的情况下使用`slice`,它会像浅层复制整个数组一样运行。

```
let social = ['twitter', 'instagram', 'facebook', 'myspace']
let sliced = social.slice()
// social is ['twitter', 'instagram', 'facebook', 'myspace']
// sliced is ['twitter', 'instagram', 'facebook', 'myspace'] 
```

Enter fullscreen mode Exit fullscreen mode

### 单参数

第一个参数是索引。复制将从给定的索引开始，并包括它之外的所有元素。

```
let social = ['twitter', 'instagram', 'facebook', 'myspace']
let sliced = social.slice(1)
// social is ['twitter', 'instagram', 'facebook', 'myspace']
// sliced is ['instagram', 'facebook', 'myspace'] 
```

Enter fullscreen mode Exit fullscreen mode

注意，与`splice`示例不同，原始数组保持不变。

### 两个论点

第二个参数也是一个索引，但是它指定了被复制数组的结束点。

```
let social = ['twitter', 'instagram', 'facebook', 'myspace']
let sliced = social.slice(1,3)
// social is ['twitter', 'instagram', 'facebook', 'myspace']
// sliced is ['instagram', 'facebook'] 
```

Enter fullscreen mode Exit fullscreen mode

再次，`social`不变。使用`slice`时总是如此。

# 轮班

轮班不需要任何争论。它用于移除给定数组的第一个元素。然而，与切片不同，虽然与拼接相似，但它直接操纵阵列。

```
let antisocial = ['tv shows', 'movies', 'puzzles', 'board games']
let firstElement = antisocial.shift()
// antisocial is ['movies', 'puzzles', 'board games']
// firstElement is ['tv shows'] 
```

Enter fullscreen mode Exit fullscreen mode

这就是它的全部功能！

# 分割

老实说，这一个真的没有那么相似。但是，它包含在我的原始推文中，所以我在这里简单提一下！

Split 接受一个字符串，并使用一个分隔符将它分成数组元素。类似这样的。

```
let msg = 'what sentence can I use for this example'
let split = msg.split('  ')
// [
//   'what', 'sentence',
//   'can',  'I',
//   'use',  'for',
//   'this', 'example'
// ] 
```

Enter fullscreen mode Exit fullscreen mode

它可能被认为有点类似的原因是，如果你不传递任何参数，你会得到一个数组中的字符串的副本。

```
let msg = 'what sentence can I use for this example'
let copy = msg.split()
// [ 'what sentence can I use for this example' ] 
```

Enter fullscreen mode Exit fullscreen mode

这是它唯一的功能。

# 莎拉·德拉斯纳来救我们了

当你读完之后，忘记这一切是可以的。想知道为什么吗？不可思议的 [@sarah_edo](https://dev.to/sarah_edo) 做了一个很牛逼的工具叫 Array Explorer！下次当你想操作一个数组，却不记得你想要什么函数的时候，试试看。

[https://sdras.github.io/array-explorer/](https://sdras.github.io/array-explorer/)

> 是的，我知道我很残忍，让你读完这些，然后给你一颗神奇的子弹。但是学习是好的！

# 再见了

所以我们有它。和往常一样，如果你正在寻找类似的内容，看看这些帖子。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 深层与浅层拷贝-示例

### 劳丽 7 月 24 日 194 分钟阅读

#javascript #webdev #learning #beginners](/laurieontech/deep-vs-shallow-copy-with-examples-cfb)[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 引入 Object.fromEntries

### 劳丽 7 月 2 日 193 分钟阅读

#javascript #webdev #learning #productivity](/laurieontech/introducing-object-fromentries-1d5l)