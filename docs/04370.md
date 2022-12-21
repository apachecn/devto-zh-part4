# 为什么没有 JavaScript 散列排序！？如何对一个简单的散列进行排序？

> 原文：<https://dev.to/andershornor/why-no-javascript-hash-sort-how-to-sort-a-simple-hash-2jnb>

### 一点背景故事:

今天，我发现自己重新访问了一个我称之为 ReadThat(又名 Reddit 克隆)的项目，并意识到我只将程序状态的一半转移到 Redux 中。我认为这是一个很好的主意，完成转让，并把我能搬到 redux 商店。我之前已经将大部分状态上移，主要需要将我的个人计票渲染和投票功能移到投票操作和减少器中。将投票计数功能集成到初始渲染中并不困难，使其具有反应性需要向状态存储中添加一个新变量，但也相当简单。然后是重新使用按投票计数排序/过滤的功能。看起来很容易，但我有一个大问题，排序 votecount 散列。

投票计数散列是有意为之的，因为我只想将净投票总数与每个帖子 id 相关联，以便能够显示每个帖子的总数。所以我的 hash 是一个以帖子 id 为键，以投票总数为值的集合。当我试图在 hash 上使用经典的数组排序方法时，我很快意识到自己的幼稚，于是求助于 google 获取更多信息。MDN 文档提到了如何对散列数组进行排序，但是没有提到直接散列。经过更多的挣扎，我在 3 年 10 个月前的[堆栈溢出帖子](https://stackoverflow.com/questions/33572347/sort-hash-ascending-by-value-using-sortby-in-javascript)中发现了黄金，并带着它跑了，所以这不是我的主意，但我发现这很有用。

我改编的排序函数看起来像这样:

```
filterPosts = async () => {

        const { selectedPosts, posts, countedVotes} = this.props
        let keys = Object.keys(countedVotes)
        keys.sort((a, b) => {return countedVotes[a] - countedVotes[b]})
        let filteredPosts = keys.map(key => {
            return {[key]: countedVotes[key]}
        })

    } 
```

不太疯狂吧？

### 工作原理:

#### 【Tl:dr】

它基本上是将散列关键字转换成一个数组，然后通过使用每个关键字访问原始散列中的值并比较它们来对该数组进行排序。然后，它按照已排序的顺序重新创建原始散列，方法是向下处理现在已排序的散列键数组，并按照它们在数组中的排列顺序从原始散列中获取每个键值对。

#### 更深入一点

第一行分解了传递给组件的属性，以便于使用。第二行利用内置的类方法从投票计数散列中获取所有的键，并将它们保存在一个数组中。下一行利用 sort 函数来组织新的 hash-key 数组，按照原始投票计数散列中每个键记录的投票数排序。最后，通过映射现在组织的散列键数组，并通过从原始散列中获取每个键的相关值来为每个键创建一个对象，从而创建一个新的散列。瞧，一个排序的散列！现在根据这个新散列重新组织用户提要，我的 Redux 存储将是最新的。

感谢您的阅读，下周还有更多！也许更令人兴奋:P