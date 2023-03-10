# 3 求和算法

> 原文：<https://dev.to/rygelxvi/3sum-algorithm-499j>

## 3 求和问题-无重复

给定 n 个整数的数组 nums，nums 中有元素 a，b，c 使得 a + b + c = 0 吗？找出数组中所有唯一的三元组，其和为零。

没有重复。

这个问题最棘手的部分是重复(取决于语言)。乍一看，蛮力方法的三次循环迭代是有意义的。

```
for (i = 0; i < nums.length - 2; i++) {
   for (j = i + 1; j < nums.length - 1; j++) {
      for (k = j + 1; k < nums.length; k++) {
       ///additional code here
      }
   }
} 
```

这导致 O(n^3 时间复杂度，并且移除重复是困难的，因为目标是返回数组的数组。

我推荐的解决这个问题的方法是使用两个指针来降低 O(n^2).的复杂度

通俗地说，我们将有一个数字，我们将通过移动两个指针来检查所有相关的组合，看它们是否与目标匹配。

为此，我们需要做的第一件事是对数组进行排序。

```
nums = nums.sort() 
```

这应该行得通吧？

```
let nums = [0,5,2,-5,-2]
nums.sort()
Array(5) [ -2, -5, 0, 2, 5 ] 
```

不，javascript 中的默认排序是按字母顺序进行的。

因此，我们必须改变排序方法。如果你想阅读我推荐的文档[这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)

```
let nums = [0,5,2,-5,-2]
nums.sort(function(a,b) {return a-b})
Array(5) [ -5, -2, 0, 2, 5 ] 
```

成功！

现在我们可以开始寻找我们独特的三胞胎 [![triplets](img/a92654c37ffbfd0a4de35874063c1c6d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yWYaGsmD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://image.shutterstock.com/image-photo/triplets-cavalier-king-charles-260nw-757983475.jpg)

所以在排序之后，我们需要创建一个 for 循环。这个循环将代表一个数字，我们将所有的东西与之进行比较。

i =我们没有改变的数字
j =低指针起点
k =高指针起点

[ ***i:-5*** ， **j:-2** ，0，2， **k:5**

以及循环的下一次迭代(i = 1)

[ -5， ***i:-2*** ， **j:0** ，2， **k:5**

然后

[ -5，-2， ***i:0*** ， **j:2** ， **k:5** ]

因此低指针 j 将总是从 i + 1 开始，高指针 k 将总是从 nums.length - 1 开始。

到目前为止，我们有

```
var threeSum = function(nums) {

    let result = []

    nums = nums.sort(function(a,b) {return a-b})

    for (let i = 0; i < nums.length - 2; i++) {

        let j = i + 1
        let k = nums.length - 1

    }      

    return result
}; 
```

因为 j 应该总是小于 k，所以我们可以把剩下的大部分代码放入 while 循环中。

然后就把剩下的逻辑过一遍。

如果这三个数的和等于目标数(leetcode 问题中的 0 ),那么将它推送到数组上，否则递增或递减适当的指针，使我们更接近目标数。

```
var threeSum = function(nums) {

    let result = []

    nums = nums.sort(function(a,b) {return a-b})

    for (let i = 0; i < nums.length - 2; i++) {

        let j = i + 1
        let k = nums.length - 1
        while (j < k) { 
            let sum = nums[i] + nums[j] + nums[k]

            if(sum === 0) {
                result.push([nums[i], nums[j], nums[k]])
                j++
                k--
            } else if (sum < 0) {
                j++
            } else {
                k--
            }

        }

    }      

    return result
}; 
```

这基本上是可行的，除了它允许一些重复，当然我们目前忽略了我们的基本情况。

为了确保找到三元组后没有重复，我们需要将指针移动到下一个唯一的数字，否则我们可能会以相同的三元组结束。

例如[0，0，0，1，1，1，2，2，2]会产生许多重复的三元组。

所以我们在添加到结果数组之后添加了以下内容。

```
while (nums[k] === nums[k - 1]) k--
while (nums[j] === nums[j + 1]) j++ 
```

这个概念也适用于‘I’所以我们用 continue 让它进行下一个循环是正确地处理变量。

```
if (i > 0 && nums[i] === nums[i - 1]) continue 
```

和基本情况。有很多方法来写这些。我认为 reduce 练习起来会很有趣。

```
 if (nums.length < 3) {return [] }
    if (nums.length === 3) {
      let sum = nums.reduce(function(acc, cv) {
            return acc + cv
        }, 0)
      return sum === 0 ? [nums] : []
    } 
```

以下是 JS
中的完整代码

```
var threeSum = function(nums) {

    let result = []

    if (nums.length < 3) {return [] }
    if (nums.length === 3) {
      let sum = nums.reduce(function(acc, cv) {
            return acc + cv
        }, 0)
      return sum === 0 ? [nums] : []
    }

    nums = nums.sort(function(a,b) {return a-b})

    for (let i = 0; i < nums.length - 2; i++) {

        let j = i + 1
        let k = nums.length - 1
        if (i > 0 && nums[i] === nums[i - 1]) continue

        while (i < j) { 
            let sum = nums[i] + nums[j] + nums[k]

            if(sum === 0) {
                result.push([nums[i], nums[j], nums[k]])
                while (nums[k] === nums[k - 1]) k--
                while (nums[j] === nums[j + 1]) j++
                k--
                j++
            } else if (sum < 0) {
                j++
            } else {
                k--
            }

        }

    }      

    return result
}; 
```

这是红宝石色的。在 Ruby 中，副本更容易处理，因为。uniq 将删除它们，这样我们就没有*拥有*来进行 while 循环。

```
def three_sum(nums)
  result = []

  return result if (nums.length < 3)

  return [nums] if (nums.length === 3 && nums.sum === 0)
  nums.sort!
  for i in 0..nums.length - 3
    j = i + 1
    k = nums.length - 1

    while j < k
      sum = nums[i] + nums[j] + nums[k]
      if sum < 0
        j += 1
      elsif sum > 0
        k -=1
      else
        result.push([nums[i],nums[j],nums[k]])
        j += 1
        k -= 1
      end

    end
  end

  return result.uniq
end 
```