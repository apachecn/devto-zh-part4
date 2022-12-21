# leet code“3 sum Closest”

> 原文：<https://dev.to/takakd/leetcode-3sum-closest-1akh>

我无法解决这个问题，所以我参考了[这个](https://leetcode.com/problems/3sum-closest/discuss/367111/Python-or-O(n**2)-or-Faster-than-91)😢

[3 总和最接近](https://leetcode.com/problems/3sum-closest/)

```
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:

        nums.sort()

        len_nums = len(nums)
        closest = nums[0] + nums[1] + nums[len_nums - 1]
        for i in range(len_nums - 2):
            left = i + 1
            right = len_nums - 1
            while left < right:
                sum_nums = nums[i] + nums[left] + nums[right]
                if sum_nums == target:
                    return target
                else:
                    if abs(sum_nums - target) < abs(closest - target):
                        closest = sum_nums
                    if sum_nums < target:
                        left += 1
                    else:
                        right -= 1

        return closest 
```

Enter fullscreen mode Exit fullscreen mode