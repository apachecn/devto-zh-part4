# 一种在一个范围内无重复地挑选随机数的算法

> 原文：<https://dev.to/babak/an-algorithm-for-picking-random-numbers-in-a-range-without-repetition-4cp6>

# 简介

从密码学到游戏，在一个范围内挑选不重复的随机数是一个常见的任务。有数学方法可以实现这一点，例如，像线性同余生成器(lcg)这样的伪随机数算法。这些方法各有利弊。一些优点包括它们可以很快并且在恒定的 O(1)空间复杂度下操作。常见的缺点包括，它们可能无法产生所有可能的排列，而且设置起来很棘手。

本文探讨了挑选随机数的问题，涵盖了所有可能的排列，空间和时间复杂度为 O(M ),其中 M 是所需的生成数，由 0 <= M <= N 给出，N 是范围的长度。让我们探索一下如何做到这一点:

一个简单的方法可能是这样的:

1.  随机选择一个指数。
2.  看看我们以前是否在一组中选过那个号码。
3.  如果我们之前已经选择了号码，请执行第一步
4.  否则，使用选中的号码并记录下来，这样我们就不会再次选中它。

这种方法的问题是第三步——必须选择另一个数字。这将减慢我们的算法，随着我们选取的项目数达到数组的长度，在步骤 3 会有更多的重复。

对此的一个解决方案是只对我们想要的项目数应用 Fisher-Yates 算法，然后返回混洗的切片。这里的问题是，如果我们想在 0 到 N 的范围内挑选 M 个数字而不改变原始数组，我们必须复制整个数组并对 M 个元素应用 Fisher-Yates。

# 算法

我们可以做的是使用哈希映射来跟踪否则会在 Fisher-Yates 中就地发生的刷卡。这个想法类似于使用一个[稀疏矩阵](http://btechsmartclass.com/data_structures/sparse-matrix.html)，只记录对我们的数组所做的改变。此外，只要我们需要，我们就会保留这些信息。让我们看看这是如何工作的。

假设我们有一个包含 5 个整数的数组。

<center>1 2 3 4 *5</center>

从最后开始，让我们从 1 到 5 中选择一个数字。假设我们选择 3。此时，我们将创建一个如下所示的表

| 绘图 |
| --- |
| 3 => 5 |

那么我们将返回 3

接下来，我们移动到 4，从 1 到 4 中选择。假设是 2。记录映射并返回 2。

<center>1 2 3 *4 5</center>

| 绘图 |
| --- |
| 3 => 5 |
| 2 => 4 |

返回值:3，2

现在我们在 3 点。假设我们现在选 3。我们看到 3 对 5 的映射。所以我们返回 5。我们删除 3，因为我们已经完成了。

<center>1 2 *3 4 5</center>

| 绘图 |
| --- |
| 2 => 4 |

返回值:3，2，5

好吧，这个会很棘手。对于索引 2，我们选择 1。我们可以写 1 => 2，但是 2 已经作为映射出现，所以我们将解决这个问题并设置 1 => 4。然后我们返回 1。下面是我们现在的状态:

<center>1 *2 3 4 5</center>

| 绘图 |
| --- |
| 1 => 4 |

返回值:3，2，5，1

最后，对于索引 1，我们解析为 4。我们的数组看起来像这样:

<center>3 2 5 1 4</center>

# 代码

让我们看看它的代码可能是什么样子。这是用普通的 JavaScript 写的

```
 // Helpers
function randInt( num ) {
  return Math.floor( Math.random() * ( num + 1 ) )
}
function getMapOrElse( map, key, fallback ){ 
  return map.has( key ) ? map.get( key ) : fallback
}
let getMapKeyOrValue = ( map, key ) => getMapOrElse( map, key, key )

// Virtual Shuffle

export function* virtualShuffle( maxLength, quantity, randRange = randInt  ) {
  // set up hash map to record swaps
  let swapMap = new Map()

  // bind swapMap to helper function
  let swapMapValueOrKey = getMapKeyOrValue.bind( null , swapMap )

  // boundaries for quantity
  if (quantity > maxLength) quantity = maxLength
  if (quantity < 0) quantity = 0

  // define start and end points
  let start = maxLength - 1
  let end = start - quantity

  for( let pointerIndex = start; pointerIndex >= end ; pointerIndex--) {
    // select a random index up to the current pointer
    let randomIndex = randRange( pointerIndex )

    // yield the current randomIndex unless we have a
    // swap recorded. If so, use the recorded swap 
    yield swapMapValueOrKey( randomIndex )

    // set the swap map at the selected random index
    // unless there is a recorded swap to use instead
    swapMap.set( randomIndex, swapMapValueOrKey( pointerIndex ) )

    // we will no longer need the recorded swap at the
    // current pointer
    if( swapMap.get( pointerIndex )) {
      swapMap.delete( pointerIndex )
    } 

  }
} 
```

此外，这里还有一个 Python 版本。Python 语言非常适合表达算法，读起来常常像伪代码。

```
import random

# Helpers def dict_value_or_key( dict, key ): 
  return dict[ key ] if key in dict else key

# Virtual Shuffle def virtual_shuffle( max_length, quantity, seed = None ):
  # hash map to keeo track of swapped indexes
  swap_map = {}

  # optional seeding of random generator
  if( seed != None ): random.seed( seed )

  # quantity is bound to range
  if quantity > max_length: quantity = max_length
  if quantity < 0: quantity = 0

  # define range start/end points
  start = max_length - 1
  end = start - quantity

  # main algorithm
  for pointer_index in range( start, end, -1 ):
    random_index = random.randint( 0, pointer_index )

    # yield random_index unless swap_map has a swap value
    # in the that case, use the recorded swap value
    yield dict_value_or_key( swap_map, random_index )

    # set swap_map for the randomly selected index to
    # the current pointer_index unless swap_map has
    # a record swap, in that case, use the swap value
    swap_map[ random_index ] = dict_value_or_key( swap_map, pointer_index )

    # we will no longer need recorded swaps recorded
    # for the current index
    if pointer_index in swap_map:
      del swap_map[ pointer_index ] 
```

像 Fisher-Yates 一样，我们选择一个最大到`pointer_index`的随机数，其中`pointer_index`从我们范围内的最大值开始，每次迭代递减 1。对于每个周期，我们做三件关键的事情:

1.  如果我们在字典中找到随机索引的映射，则产生我们选择的随机索引或交换值
2.  记录随机选择的索引到当前指针的映射
3.  删除当前指针的已存储映射，因为所有未来迭代都将小于该值

真正简化代码的一个助手是`dict_value_or_key`。这是一个在哈希表/字典中查找关键字的函数，如果找到关键字，则返回值；否则，它返回密钥。

这种方法能够从所有排列(N！).

# 结论

通过使用哈希映射，我们能够模拟 Fisher-Yates 算法，在 0 到 N 的范围内挑选 M 个元素，空间和时间复杂度为 O(M)。