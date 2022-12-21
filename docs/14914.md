# 最具水码挑战的容器

> 原文：<https://dev.to/rygelxvi/container-with-the-most-water-code-challenge-34ff>

[赤经问题 11](https://leetcode.com/problems/container-with-most-water/)

这个问题非常简单。

给定一组高度，找出两个指数之间可能包含最多的水。

通常有两种方法可以解决这个问题，强力法和双指针法。

### 蛮力

这种方法将计算每一种可能的组合来确定答案。这需要一个嵌套循环，导致 O(n^2).的复杂性

Javascript

```
var maxArea = function(height) {
    let mostWater = 0

    for (let l = 0; l < height.length - 1; l++) {
        for (let r = height.length - 1; r > l; r--) {
            mostWater = Math.max(mostWater, (Math.min(height[l], height[r])*(r-l)))
        }        
    }
    return mostWater
} 
```

Enter fullscreen mode Exit fullscreen mode

更好的方法是利用两个指针。

### 两指针法

对于这种方法，我们利用放置在数组两端的两个指针来迭代一次，导致时间复杂度为 O(n)。当前左(l)和右(r)索引之间的区域，如果该区域大于当前最大值，则设置为最大值。

在这种情况下，基于哪一个较小或者是否等于左边的一个来移动索引。它是任意的，在 if 语句中检查。

Javascript

```
var maxArea = function(height) {
    let max = 0

    let l = 0
    let r = height.length - 1

    while (l < r) {
        if (height[l] > height[r]) {
            max = Math.max(height[r] * (r-l), max)
            r--
        } else {
            max = Math.max(height[l] * (r-l), max)
            l++ 
        }       
    }

    return max
}; 
```

Enter fullscreen mode Exit fullscreen mode

用红宝石...

```
def max_area(height)
    max_area = 0

    l_idx = 0
    r_idx = height.length - 1

    while (r_idx > l_idx)  
        if height[r_idx] >= height[l_idx]
            max_area = [(height[l_idx] * (r_idx - l_idx)), max_area].max
            l_idx += 1
        else 
            max_area = [(height[r_idx] * (r_idx - l_idx)), max_area].max
            r_idx -= 1
        end        
    end

    return max_area
end 
```

Enter fullscreen mode Exit fullscreen mode

为了提高可读性，你可以将 max_area (ruby)或 max (javascript)分成多行。

Javascript

```
if (height[l] > height[r]) {
    maybeMax = height[r] * (r-l)
    r--
} else {
    maybeMax = height[l] * (r-l)
    l++ 
}        

max = Math.max(maybeMax, max) 
```

Enter fullscreen mode Exit fullscreen mode

红宝石

```
if height[r_idx] >= height[l_idx]
    maybe_max = height[l_idx] * (r_idx - l_idx)
    l_idx += 1
else 
    maybe_max = height[r_idx] * (r_idx - l_idx)
    r_idx -= 1
end

max_area = maybe_max if maybe_max > max_area 
```

Enter fullscreen mode Exit fullscreen mode

因为 Ruby 和 JS 解决方案看起来非常相似，所以我决定混合最后一行的语法。