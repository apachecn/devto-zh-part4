# 算法:最接近二和

> 原文：<https://dev.to/tttaaannnggg/algorithms-closest-to-twosum-3npj>

今天我被介绍了另一种算法。对我来说，它类似于 TwoSum，但有一个主要的警告——它不是计算数组中的两个数字是否/哪两个数字加到一个目标数字上，而是要求计算最接近目标的两个数字的总和。

如果这还不清楚，让我们看一个例子。

如果我们有一组`[1,5,12,6,13]`，目标为`12`，我们能得到的最接近的要么是`1+12`，也就是`13`(距离 1)，要么是`5+6`，也就是`11`(也是距离 1)。

在`O(n^2)`时间内做这件事并不太难。我们可以计算所有可能的和，或者在最后比较它们(`O(n)`空间复杂度)，或者保持一个`lowestDistance`值，当我们继续导航我们的数组(`O(1)`空间)时，我们更新这个值。可能是这样的:

```
function closestTwo(arr, target){
  let lowestDistance = Infinity;
  let nums = [null, null];
  for(let i = 0; i < arr.length; i++){
    for(let j = i+1; j < arr.length; j++){
      const sum = arr[i] + arr[j];
      const dist = Math.abs(target-sum);
      if(dist<lowestDistance){
        lowestDistance = dist;
        nums[0] = arr[i];
        nums[1] = arr[j];
      }
    }
  }
  return nums;
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，我们实际上可以做得比`O(n^2)`更好。好多少？我们会找到答案的。

起初，我对此感到困惑；根据问题提出的方式，我猜测解决方案可能类似于二和，并且可能与这种逆向思维有关。这是我走过的几条路。

*   我们可以做 twoSum 的事情，从每个数字中减去我们的目标，将它们存储在一个我们可以快速检查的数据结构中，比如一个`Set`。
    *   然而，如果我们谈论的是“接近”，而不是准确，我们不能模糊我们要给我们的`.has()`的东西——我想通过某个范围来划定它，但即使是最接近的数字也可能离我们的目标非常远，如果我们必须检查范围内的每一步，它最终会非常慢。
*   我们可以对数组进行排序。它是`O(nlogn)`，这将使数组的行为更加可预测
    *   但是，我们如何从那里找到解决办法呢？也许二分搜索法是可能的？
    *   如果我们做二分搜索法，我们怎么知道我们在找什么？

嗯，这种方法和二分搜索法的方法确实有效。它仍然不是最快的，但我们可以在`O(nlogn)`时间内完成，这是迄今为止我得到的最好的时间复杂度，尽管它可以进一步优化。

这种方法的工作原理如下:

1.  从数组的开头开始一个指针
2.  从目标总和中减去它
3.  不管余数是多少，二分搜索法遍历它后面的所有项目，并获取你能找到的最接近的值
4.  将指针移动到数组中的下一项，并重复该过程
5.  与之前的最低距离进行比较，保留更接近的答案
6.  重复，直到你遍历了整个数组

我们来写吧。

```
function closestTwo(arr, target){
  arr.sort((a,b)=>a-b);
  let lowestDist = Infinity;
  let lowestNums = [null,null];
  for(let i=0; i<arr.length; i++){
    const searchTarget = target - arr[i];
    let guess = Math.floor((i + arr.length)/2);
    let lastGuess = guess;
    let lastDist = Math.abs(searchTarget - guess);
    while(Math.abs(guess-lastGuess)!=1){
      let dist;
      if(searchTarget>guess){
        guess = Math.floor((guess + arr.length)/2);
        dist = Math.abs(searchTarget - arr[guess]);
      }
      if(searchTarget<guess){
        guess = Math.floor((i+guess)/2);
        dist = Math.abs(searchTarget - arr[guess]);
      }
      if(dist > lastDist){
        break;
      }
      lastGuess = guess;
      lastDist = dist;
    }
    if(lastDist<lowestDist){
      lowestDist = lastDist;
      lowestNums[0] = arr[i];
      lowestNums[1] = arr[lastGuess];
    }
  }
  return lowestNums
} 
```

Enter fullscreen mode Exit fullscreen mode

所以，这很好，但是它在做 2 个`O(nlogn)`操作。第一个是我们对它进行排序，第二个是我们对数组进行迭代，并对每次迭代进行二分搜索法。这和时间复杂度一样好，但是我们可以稍微调整一下，做一个`O(nlogn)`操作(排序)和一个`O(n)`操作。

下面说说我们是怎么做到这一点的。

还记得我之前说过，在我们对数组进行排序之后，它的行为变得更容易预测了吗？让我们想想如何利用这一点。如果我们从低到高排序，我们知道越靠近数组的开头，你的数字越低，越靠近末尾，数字越高。最低可能的总和是数组的前两项，最高可能的总和是数组的后两项。

`[1,2,3,4,5]`有一个最低可能的总和`1+2`，和一个最高可能的总和`4+5`——但是我们如何找出这两者之间的东西呢？它的神奇之处在于，我们可以通过移动几个指针来做到这一点，这将不可避免地收敛到最接近的和。确保我们更接近期望的解决方案的方法是，我们将使用两个指针——一个在数组的开始，一个在数组的结尾。它是这样工作的:

1.  对数组排序
2.  将指针放在数组的开头
3.  将指针放在数组的末尾
4.  将我们指向的两个值相加
5.  我们的总和是高于还是低于目标？
6.  如果总和太高，将结束指针移动到下一个最低的项目。如果太低，将低指针移动到下一个较高的项目
7.  再次求两个值的和
8.  如果该总和的距离大于上一个总和，则返回以前的值
9.  否则，继续该过程。

看起来是这样的:

```
function closestSum(arr, target){
  arr.sort((a,b)=>a-b);
  let lowPointer = 0;
  let highPointer = arr.length-1;
  let nums = [null,null];
  let closestDist = Infinity;
  while((lowPointer+1)!==highPointer){
    const sum = arr[lowPointer] + arr[highPointer];
    const dist = Math.abs(target-sum);
    if (dist < closestDist){
      closestDist = dist;
      nums[0] = arr[lowPointer];
      nums[1] = arr[highPointer];
      if(sum<target){
        lowPointer++;
      }
      if(sum>target){
        highPointer--;
      }
    }else{
      break;
    }
  }
  return nums;
} 
```

Enter fullscreen mode Exit fullscreen mode

它也不是最容易阅读的，但我们基本上要么向上移动我们的低位指针，要么向下移动我们的高位指针。我们知道，如果我们的猜测越来越糟糕，或者如果我们的指针直接紧挨着彼此，那么我们就可以简单地跳出循环并返回我们的值。

主要要点:

*   进行比较和类比是好的，但是不要让自己陷入其中——如果我不知道二和的话，我可能会更快地解决这个问题
*   初始排序通常是操作数据集的关键，并且可以为您提供搜索数据集的有用工具(特别是 O(logn)中的二分搜索法)。
*   关于处理数据集，还有其他类型的直觉需要开发——我不能凭直觉认为从外向内移动指针就能保证完全覆盖数组，但现在我很清楚，这样做可以到达任何子数组。这可能对其他算法有用。
*   这种解决方案也适用于 twoSum，尽管时间复杂度稍差。如果数组是预先排序的，我们就能在 O(n)时间和 O(1)空间内完成。