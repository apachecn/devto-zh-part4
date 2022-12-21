# JS 集-第 1 部分

> 原文：<https://dev.to/andershornor/js-set-part-1-5e72>

正如我现在所经历的一切新事物一样，我在寻找一种从对象数组中删除重复对象的方法时，遇到了强大的 JS Set 对象。

最近，我一直在改造和升级我的一个项目，在将一个投票功能集成到一个评论功能时，我遇到了一个排序功能的问题。它添加了一个被排序的事物的重复实例！我没有修改排序函数排序的内容(我仓促决定使用过于稀疏的散列来存储投票计数)；或者修改从服务器传递到客户端的信息(修改后端来存储投票计数(太贵了*可能是*))我选择删除重复的，并称之为好。众所周知，计划是关键，但似乎不是每个人都知道或承认的是，当事情不按计划进行时，有 99.9999%的可能性会有工作。所以我选择了我的工作，也就是 Set 方法； [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)给那些有此倾向的人。

### 什么是设定

Set 是一个强大的工具，允许用户创建原始值或对象的唯一值的集合。

> Set 对象允许您存储任何类型的唯一值，无论是原始值还是对象引用。

而且从描述来看；

> 集合对象是值的集合。您可以按插入顺序循环访问集合中的元素。集合中的值只能出现一次；它在该系列中独一无二。

- [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

它基本上是一个独特值的超级开放式集合，允许通过创造性地和有见地地使用各自的方法来桥接数组和对象方法。

#### 快速，如何制作一套...(这不就是你来这里的目的吗！？)

“但是安德斯！？牛肉呢？”

我不会太深入到 set 方法中，但是简单地告诉你如何创建一个 Set。

```
let thing = [[1 ,"two", #], {4: $}, {5: %}]

let thingSet = new Set(thing) 
```

或者只是

```
let thingSet = new Set([[1 ,"two", #], {4: $}, {5: %}]) 
```

#### 让一个集合变成一个数组——皮斯！

使用设定对象相对容易。尽管集合对象是可迭代的，但我发现使用集合对象最有效的方法是用 [`Array.from()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 或 [`Object.fromEntries()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries)
将它们转换成一个更通用的数据结构

```
//thingSet from before BTW!
let thingSetArr = Array.from(thingSet) 
```

#### 用集合对对象数组进行排序...(你是不是不被娱乐了！？)

所以现在你来的目的是“找到牛肉”。呃，呃，好吧。
现在来看看你来这里的目的，整理一系列的物品。
回想起我在这篇文章开始时的困境，经过深思熟虑和一篇常见的 stackoverflow 文章之后，我有了一个解决问题的办法。有了 Set 对象及其相关方法和`Array.from()`方法，我的路就畅通了。为了达到平庸和制造 bug 的愤怒，我必须从我的 Object-Array 中`Array.map()`出一个 id 数组，创建这些 id 的一个*唯一的*集合，用`Array.from()`将那个集合转换回一个数组，同时保持在一个变量赋值中遍历它的能力，最后映射 id 数组，同时从原始 Object-Array 返回找到的对象。瞧啊。一个 **独特的** 对象数组！

```
 const output = Array.from(new Set(placeholder.map(a => a.id))).map(id => {
        return placeholder.find(a => a.id === id);
    }) 
```

感谢您查看本周的版本。有工作给我吗？在 linkedIn 上联系我！ :D